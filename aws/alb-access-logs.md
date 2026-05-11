
---
title: ALB アクセスログ（S3 出力と Athena 集計）
lang: ja
---

# ALB アクセスログ（S3 出力と Athena 集計）

Application Load Balancer の**行単位のアクセスログ**は **Amazon S3** に出力する。CloudWatch Logs への直接配信は**サポートされない**（メトリクスは別途 CloudWatch に標準出力）。ここでは**マネジメントコンソール（GUI）**での手順をまとめる。

公式: [Application Load Balancer のアクセスログを有効にする](https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/application/enable-access-logging.html)

---

## 1. アクセスログの有効化（ALB 側）

1. **EC2** コンソール → 左メニュー **ロードバランサー**。
2. 対象の **Application Load Balancer** を選択。
3. **属性** タブを開く。
4. **アクセスログ** を **編集**。
5. **有効** にし、**S3 の場所** を指定する（例: `s3://バケット名/` またはプレフィックス付き `s3://バケット名/任意プレフィックス/`）。実際は、その下に、`/AWSLogs/[アカウントID]/elasticloadbalancing/[リージョン]/yyyy/mm/dd` となるのでバケット名だけでよい。
6. 保存する。

プレフィックスに **`AWSLogs` という文字列を含めない**（ALB の制約）。

ログは **数分遅れて S3 に到着**することがあり、リアルタイム用途ではない。アプリのレイテンシへの影響は通常は問題にならない設計となっている。

---

## 2. S3 側の準備とアクセス許可

**バケットを用意し、ALB のログ配信サービスが書き込めるようにする必要がある。** 

公式ページの **バケットポリシー** の節のサンプルをコピーし、次を**自環境用に差し替え**て S3 のバケット **アクセス許可 → バケットポリシー** に貼る。

- リージョン（例: `ap-northeast-1`）
- AWS アカウント ID
- バケット名・プレフィックス
- ロードバランサーの ARN（公式の条件付きポリシーで推奨される場合あり）

**Principal** は `logdelivery.elasticloadbalancing.amazonaws.com`（公式の最新手順に従う）。

### 動作確認

保存後、しばらく待ってバケット内にログオブジェクトが増えるか確認する。公式手順にある **`ELBAccessLogTestFile`** の有無で権限検証できる。

---

## 3. Athena でログを集計する

S3 上の gzip ログを **Athena** からクエリする。テーブルは **Glue データカタログ上の外部テーブル定義**であり、データ本体は常に S3 にある。

### 事前設定（GUI）

1. **Athena** → **クエリエディタ**。
2. **設定**（Settings）で **クエリの結果の場所**（Query result location）に、**クエリ結果用の S3 プレフィックス** を指定（未設定だとクエリが実行できない）。

### データベースとテーブル作成

- 必要なら `CREATE DATABASE` でデータベースを 1 つ作成し、選択する。
- **CREATE EXTERNAL TABLE** は、次の公式の DDL を**そのままコピー**し、**LOCATION** だけ自環境の S3 に合わせて変更するのが安全である。

  - [ALB アクセスログ用テーブルの作成](https://docs.aws.amazon.com/ja_jp/athena/latest/ug/create-alb-access-logs-table.html)

  このページの **`CREATE TABLE` 例には `PARTITIONED BY` がなく、パーティション用の列（`year` / `month` / `day` など）は定義されない。** 日付で絞るときは、同じ DDL に含まれる行の **`time`** 列を使う（下記「日付で絞る」の **time 列** の例）。

**LOCATION の例**（アカウント ID・プレフィックス・リージョンは置き換え）:

`s3://バケット名/プレフィックス/AWSLogs/アカウントID/elasticloadbalancing/リージョン/`

- **一度テーブル作成に成功すれば**、別日に Athena を開いても **同じテーブル定義が残る**（毎回 CREATE は不要）。`DROP TABLE` した・別リージョンで開いている・ログ形式や S3 パスを変えた、などのときだけ再作成や定義変更が必要。
- ログ形式が古く公式 DDL と合わない場合は、公式ページの注記どおり列を減らした定義に切り替える。
- **`year` / `month` / `day` といったパーティション列付きのテーブル**にしたい場合は、[パーティション投影の設定](https://docs.aws.amazon.com/ja_jp/athena/latest/ug/create-alb-access-logs-table-partition-projection.html) の DDL を使う（スキャン費用・速度の改善。基本の作成ページとは別手順）。

### クエリ例（パス別の件数）

公式 DDL では URL 部分は **`request_url`** 列として取れる想定。特定パス配下だけ数えたい場合のイメージ:

```sql
SELECT count(*) AS cnt
FROM alb_access_logs
WHERE request_url LIKE '%/parent/sub%';
```

内訳:

```sql
SELECT request_url, count(*) AS n
FROM alb_access_logs
WHERE request_url LIKE '%/parent/sub%'
GROUP BY request_url
ORDER BY n DESC
LIMIT 50;
```

### 日付で絞る

**`time` 列で絞る（基本の公式 DDL の場合こちら）** … 公式 DDL に含まれる **`time`**（リクエスト時刻の文字列）を使う。形式はログ仕様どおりだが、ISO 8601 風なら日付部分の比較で足りることが多い。

```sql
-- 例: 2026-05-11 の1日分（time の先頭が yyyy-mm-dd 形式である前提）
SELECT count(*) AS cnt
FROM alb_access_logs
WHERE request_url LIKE '%/parent/sub%'
  AND substr(time, 1, 10) = '2026-05-11';

-- 例: 期間（多日）
SELECT count(*) AS cnt
FROM alb_access_logs
WHERE request_url LIKE '%/parent/sub%'
  AND substr(time, 1, 10) >= '2026-05-01'
  AND substr(time, 1, 10) <= '2026-05-11';
```

`time` のパースで型を揃えたい場合は `date_parse` や `from_iso8601_timestamp` も使えるが、**小数秒やタイムゾーン表記が環境と完全一致するか**は、まず `SELECT time FROM alb_access_logs LIMIT 5` で実データを確認するとよい。

列名・パーティション列が上と違う場合は、作ったテーブル定義に合わせて `WHERE` を付け替える。

参考: [ALB アクセスログのクエリ例](https://docs.aws.amazon.com/ja_jp/athena/latest/ug/query-alb-access-logs-examples.html)

---

## 4. 補足（WAF ログとの違い）

- **AWS WAF** のログは CloudWatch Logs や S3 などに出せるが、**ログフィルタで BLOCK のみ**にしている構成では、通過リクエストの母数は取れないことがある。
- **パス別の通過済みリクエスト**を集計したい場合は、**ALB アクセスログ（S3）** や CloudFront のログなど、別ソースを検討する。

