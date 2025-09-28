---
title: AWS 資格メモ
---


AWS Developer ~の資格を取ろうとしたけどやめちゃったときのメモ


# TODO

- [ ] セッションステータスの情報をデバイス間で確実に維持する
https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/design-interactions-in-a-distributed-system-to-mitigate-or-withstand-failures.html
https://aws.amazon.com/jp/getting-started/hands-on/building-fast-session-caching-with-amazon-elasticache-for-redis/~~
- [ ] API GateWay
https://docs.aws.amazon.com/apigateway/latest/developerguide/amazon-api-gateway-using-stage-variables.html
- [ ] 一度に 1 つのロールのみを引き受けられるのは、EC2 インスタンス
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html
- [ ] 複数のポリシーを 1 つの IAM ロールに添付できます
https://docs.aws.amazon.com/IAM/latest/UserGuide/intro-structure.html#intro-structure-authorization

# AWS

## AWS CloudShell、AWS Systems Manager

マネージコンソールにログイン後にshellを使うことができる。認証はログインしたユーザのものが通っているので、AWS CLIが使える。

## Code Whisperer

コメントからコード生成

## Organizations

- 複数の AWS アカウントの一元管理
- 普通、本番環境とテスト環境でAWSのアカウント自体を分けるものらしい。その場合、billingなどは統合管理したいのでOrganizationsでまとめるとのこと。

## SAM (Serverless Application Model)

- AWS でサーバーレスアプリケーションを構築するためのオープンソースのフレームワーク。テンプレート仕様とコマンドラインインターフェイス（CLI）ツールを提供。

## ECS (Elastic Container Service) / EKS (Elastic Kubernates Service)

- コンテナサービス
- EKSはKubernatesを使う場合

## SQS (Simple Queue Service)

- メッセージキュー
- Amazonが顧客のカート挿入操作を絶対喪失させないために作った機能だとか。

## X-Ray

- サービスを横断したトレース、分析、デバッグするサービス

## AppSync

- GraphQLに関するサービス

## AWS Secrets Manager

- APIキーの保存。ローテーション設定が可能。

---
## 研修メモ

### 試験対策

- black beltとサービスのFAQ
- テキストのデプロイ戦略

### 低レベルAPIと高レベルAPI

- クライアント〜: 低レベル

```python
client = boto3.client('s3')
```
- リソース〜: 高レベル


### IAM

- ポリシーはユーザまたはグループに付与される
- グループに属するユーザはグループに付与されたポリシーを行使できる
- ロールはユーザに一時的な権限を与えるケースと、サービス自体に権限を付与するケースに使用する
- ロールはセッション時間を設定できる。1h〜7dayくらい
- アカウントIDは12桁の数字
- AWSにはアカウントとユーザは別の概念
- ポリシーには別アカウントのリソースへのアクセスを許可することができる
- プリンシパル: AWSのAPIを起こす実体（ユーザ、ロール、ポリシー、サービス）
- アイデンティティポリシー: ユーザに対して。
- リソースベースポリシー: サービスに対して。`Principal`が必須。これがあればリソースベース。
- リソースベースポリシーのほうが優先。リソースの方にかかれていればアイデンティティにかかれていなくても有効。
- "許可"は上記のとおりだが、”拒否”は最優先。
- **アクセス許可の境界**？

### ユーザ管理

- AWSアカウントは本番・開発・テストと環境分ける場合、アカウントも分けるのが通常
- その際、AWSアカウントごとにユーザを作らない。ユーザを作るとそれぞれにパスワードが必要。
- そうではなく、ユーザは一つ、アカウントをまたいでロールを設定して各環境にアクセスさせるのがベストプラクティス。

### 開発環境

- 認証情報の優先順位: コードで指定> 環境変数> 認証情報プロファイル（.aws/credentials）> インスタンスプロファイル（←cloud9などで使用するec2インスタンスに割り当てられた認証情報）
- 一時的な認証情報: STS（Security Token Service）からIDとSECRET KEYを受領できる

### S3

- 同時に3つのAZに保存している（そのために高可用性を実現）
- フォルダ構造は見た目だけで、中身はフラットな構造
- バージョン管理はデフォルトオフ。バージョン管理して、削除すると、"削除マーカ”がつくだけで実削除は行われない。実削除はバージョンを指定して削除する必要がある。
- ACLでポリシー管理できるが、これはS3独自の権限管理。IAMが登場する前からS3サービスがあるから。現在は廃止。現在はバケットポリシーかIAMポリシーで制御する。
- 1度のAPI呼び出しでアップロードできるオブジェクトサイズの上限は5GB
- バケットに保存できるオブジェクトサイズの上限は5TB
- 5G以上のファイルをアップロードするには"マルチパートアップロード"を使用する。ベストプラクティスは100MB以上。
- `S3 Select`: コンテンツの中身をSQL式でバケットからオブジェクトを取得できる。json, csvなどが対応
- 署名付きURL:  一時的に他人にデータを渡すとか。有効期限の追加長い文字のURLを生成する。PUTも可能。
- ウェブサイトホスティング: `aws s3 website`するとホスト名を叩くと/index.htmlに転送してくれる。また、corsを許可する
- ` upload_file`と`put_object`: `upload_file`はファイル自体をs3に上げる。`put_object`はpythonの中でバイナリを読み込んでs3に上げる。マネージド転送という。下りの`download_file`と`download_fileobj`も同様。

### DynamoDB
- NoSQLデータベース。key-value方式
- 複数のAZに保存
- DynamoDB Localが存在する...ローカルでの開発目的
- テーブル、カラム単位でIAMの制御が可能
- パーティションキー≒PK=保存する物理的な場所（パーティション別に保存する）
- PKはパーティションの値＋ソートキーの値
- 読み取り容量単位（RCU: read capacity unit）: 1秒あたり最大4KBに対して可能な読み取り回数
- 書き込み容量単位（WCU）: 1秒あたり1KBの書き込み回数。
- RCUとWCUのデフォルトは5。これによってコストが決まる。非常に単純明快。5なら$2以下。
- RCU、WCUを超えた読み書きは取りこぼす
- パーティションキーとソートキー以外で検索できない。→基本的にアプリケーションとして読み取りが発生するデータを洗い出して設計時に決定しておく必要がある
- パーティションキーは完全一致のみ。範囲指定・部分一致などは不可。ソートキーは範囲指定・部分一致などの条件索引に対応。
- 1度の読み取り結果は1MB以下まで
- 項目最大サイズは400KB
- `scan`して`filter`はあるが、内部では全件読み取り後のフィルタなのであまり意味がない。RCUも全件消費する
- 1つのテーブル（ドキュメント）にはパーティションキーとソートキーはそれぞれ1つしか設定できない
- 別にセカンダリインデックスを設定して、サーチできるようにする。
- セカンダリインデックスにはローカルセカンダリインデックスとグルーバルセカンダリインデックスがある。
- ローカルセカンダリインデックス: テーブル生成時に指定。5個まで。
- グローバルセカンダリインデックス: 随時設定可能。20個まで。これはテーブルのコピーなので整合性がズレる可能性がある。
- アダプティブキャパシティとバーストキャパシティ
- DynamoDB Accelerator（DAX）: DynamoDBの前に置くインメモリキャッシュDB。DynamoDBは単体でミリ秒単位の読み取りを保証するので通常は不要。マイクロ秒単位の読み取りを実現したいときに使用する


#### テーブル設計
- `パーティションキーとソートキー以外で検索できない`→基本的にアプリケーションとして読み取りが発生するデータを洗い出して設計時に決定しておく必要がある
- 正規化を考えない。検索効率を重視する。

#### 暗号化
- デフォルトでの暗号化。DynamoDB は、書き込まれる際に、すべてのテーブルを透過的に暗号化および復号します。**保管時の暗号化を有効または無効にするオプションはありません。**暗号化キーはKMSでの管理。
- テーブルに関連するオブジェクトも暗号化されます。保管時の暗号化は、永続的なメディアに書き込まれるたびに、DynamoDBストリーム、グローバルテーブル、バックアップを保護します。
- アクセスすると、項目は復号されます。テーブルがアクセスされるとき、DynamoDB は、ターゲット項目を含むテーブル部分を復号し、プレーンテキスト形式で項目を返します。
- 転送中のデータ: DynamoDB のすべてのデータは転送時に暗号化されます。デフォルトでは、DynamoDB との通信において、SSL/TLS 暗号化を使用してネットワークトラフィックを保護する HTTPS プロトコルが使用されます。
- 使用中のデータ: DynamoDB 送信する前のデータを保護するには、クライアント側の暗号化（AWS Database Encryption SDK for DynamoDB（旧Amazon DynamoDB Encryption Client））を利用する。

#### DynamoDB Streams
- DynamoDBにトリガーを設定できる。Streamを有効にしたテーブルは、変更が24時間記録される。
- StreamはLambdaと統合しているため、DB変更を検知して、メール通知、ワークフローの開始などを実装することができる。
- AWS Lambda サービスは、1 秒に 4 回、ストリームをポーリングして新しいレコードを探します。新しいストリームレコードが利用可能になると、Lambda 関数が同期的に呼び出されます。同じ DynamoDB ストリームに最大 2 つの Lambda 関数をサブスクライブできます。


### Lambda

- 最低メモリ125MB、最大実行時間15分
- pythonの外部ライブラリなどは”レイヤー”に入れる。"レイヤー”にはAmazonが用意するライブラリ群もある
- IAMはlambdaに対するポリシー（S3を使う場合のS3の読み取りなど。IAMロール）、lambdaへのポリシー（イベント発生元がlambdaを呼び出すなど。リソースポリシー）
- lambdaを呼び出すには、同期、非同期、ポーリングの3種類
- 非同期の場合は、lambdaにイベントキューがあるので、イベントキューに溜まったものが都度実行される。（S3に書き込まれたオブジェクトをサイズ変更・複数ファイル化するなど）再試行が2回まで。
- ポーリング＝lambdaが他のDynamoDBをポーリングして条件に一致したら実行するなど
- 実行までにコールドスタート・ウォームスタート期間がある。コールドスタートを避けるには、スケジュールして一定間隔で実行させておくか、プロビジョニングしてランタイム環境を用意しておくことができる
- ハンドラ関数は引数にイベントとコンテキスト（実行環境に関する情報）を持つ
- バージョン管理機能がある。バージョンを作成（発行という）すると、その状態をロックする。ARNには末尾にバージョン（:1など）を付与して使用する。（→修飾ARN）

修飾ARNの例: 

```
arn:aws:lambda:aws-region:acct-id:function:helloworld:42
```
バージョンなしを非修飾ARNという

```
arn:aws:lambda:aws-region:acct-id:function:helloworld
```
- 非修飾ARNは暗黙的に`$LATEST`を呼び出す。
- エイリアス: `lambda のエイリアスとは、更新可能な関数のバージョンへのポインタです。`
- エイリアスもARNの末尾に付与して使用する。`:prod`など。
- エイリアスのルーティング設定: `トラフィックの一部を 2 番目の関数バージョンに送信します。たとえば、エイリアスを設定して、ほとんどのトラフィックを既存のバージョンに送信し、トラフィックの一部を新しいバージョンに送信するように設定することで、新しいバージョンを展開するリスクを軽減できます`

### API Gateway

- 必須じゃないが、APIの呼び出しを抽象化することが目的
- APIの公開、維持、モニタリング、保護
- REST API, HTTP API, WebSocketの3種類がある
- スロットル制限（呼び出し回数を制限できる機能。サブスクサービスで、何回までならFreeみたいな）
- SDKの生成
- 呼び出し側テストのためのモックの生成
- 統合タイプ: プロキシ、非プロキシ: パラメータを自動でマッピングするかどうか。マッピングテンプレート（VTLという言語）を使用して変換する。
- Swagger（APIを記述するドキュメント規定）で設計
- デプロイ時にはステージ変数をつける: 本番/開発、v1/v2など
- カナリアリリース（バージョンアップのときに安定バージョンから新バージョンの少しずつユーザを移していく手法）に対応 `canaryの設定`

### モダンアプリケーションとは

- マイクロサービス、サーバレス、DevOps
- マイクロサービス: 単一機能を実行する / ⇔モノリス（全機能を実装）/ 機能をコンテナに閉じ込める / 各機能はAPIでのみ公開する
- マイクロサービスを統合するのに、イベント駆動型にする場合、AmazonではAWS SQSを活用。
- DevOps: Developers and Operators. マイクロサービスごとに開発から運用までを行う。サービスごとのチーム分け。/ Infastructure as Code / 継続的インテグレーション（CI）/ 継続デリバリー（CD）
- DevOpsを助けるサービス: CodeCommit: 閉じたGit / CodeBuild: CodeCommitのbuild / CodeArtifact: ミドルウェアの管理 / CodeDeploy: CodeBuildのデプロイ / CodePipeline: Code〜の各種サービスの統合 

### Cognito

- 認証と認可を司るサービス
- 不特定多数のユーザにAWSサービスを使う場合に使用する
- ユーザープールとIDプール
- ユーザープール: Amazon Cognito におけるユーザーディレクトリ。`ユーザーは、Amazon Cognito を介してウェブやモバイルのアプリケーションにサインインできるようになります。また、ユーザーは Google、Facebook、Login with Amazon、Apple などのソーシャルアイデンティティプロバイダー、および Security Assertion Markup Language (SAML) 2.0 のアイデンティティプロバイダー経由でサインインすることもできます。ユーザーが直接またはサードパーティーを通じてサインインするかどうかにかかわらず、ユーザープールのすべてのメンバーには、ソフトウェア開発キット (SDK) を通じてアクセスできるディレクトリプロファイルがあります。`

### CloudWatch, X-Ray

- オブザーバビリティ: 収集・モニタリング・アクション・分析
- ロギング、メトリクス→CloudWatch / トレース→X-Ray
- X-Ray: マイクロサービス間のトレースを可能にする。X-RayデーモンとX-Ray API
- `AWS X-Ray は、アプリケーションが処理するリクエストに関するデータを収集し、そのデータを表示、フィルタリング、考察することで、問題点や最適化の機会を特定するために使用できるサービスです。トレースされたリクエストは、リクエストとレスポンスの詳細情報を参照できるほか、アプリケーションがダウンストリームの AWS リソース、マイクロサービス、データベース、および HTTP Web API に対して行うリクエストについても確認することができます。`

### Key Management Service（KMS）

- 暗号化キーの一元管理。他のAWSサービスと統合している。（Dynamo DBはデフォルト暗号化されるが、その暗号化キーはKMSで管理されている）
- S3は`Amazon S3 マネージドキーによるサーバー側の暗号化 (SSE-S3) `を行うが、KMS管理のキーによる暗号化に設定が可能。
- 中国だけ暗号化計算が異なる。

> AWS KMS は、次のタイプのデータキーペアをサポートしています。
・RSA キーペア: RSA_2048、RSA_3072、RSA_4096
・楕円曲線キーペア、ECC_NIST_P256、ECC_NIST_P384、ECC_NIST_P521、ECC_SECG_P256K1
・SM キーペア (中国リージョンのみ): SM2

- デフォルトは対称暗号化キー。署名、検証用の非対称暗号化キーも作成・管理が可能。
- HMAC（Hash-based Message Authentication Code）の生成も可能。HMACキーは対称キー。
https://docs.aws.amazon.com/ja_jp/kms/latest/developerguide/key-types.html#symm-asymm-choose-key-usage
- 自動ローテーション: `AWS KMS は、ローテーションが有効になってから 1 年 (約 365 日) 後にキーマテリアルをローテーションし、その後は毎年 (約 365 日間隔で) ローテーションします`
https://docs.aws.amazon.com/ja_jp/kms/latest/developerguide/rotate-keys.html#rotate-keys-how-it-works

### AWS Systems Manager

- JP1エージェントのようなものだが、パラメータストア、変更管理など、より広範な機能を持つ模様。
> AWS Systems Manager を使用することで、複数の AWS のサービスの運用データを一元化し、AWS 上およびマルチクラウドやハイブリッド環境内のリソース全体のタスクを自動化できます。アプリケーション、アプリケーションスタックのさまざまなレイヤー、本番環境と開発環境といったリソースの論理グループを作成できます。Systems Manager では、リソースグループを選択し、その最新の API アクティビティ、リソース設定の変更、関連する通知、運用アラート、ソフトウェアインベントリ、パッチコンプライアンス状況を表示できます。運用ニーズに応じて、各リソースグループに対してアクションを実行することもできます。Systems Manager により、AWS 上およびマルチクラウドやハイブリッド環境内のリソースを一元的に表示および管理でき、運用を完全に可視化して制御できます。

- オンプレミスで実行されるインスタンスも管理
- `AWS Windows AMI と Amazon Linux AMI にはデフォルトで Systems Manager エージェントがインストールされており、Amazon Linux リポジトリで利用できます`
- パッチマネージャー: OSパッチの適用・運用管理
- パラメータストア: 環境変数などのパラメータ文字列を一元管理。各サービスで呼び出せる。

### AWS CodeDeploy

- `Amazon EC2 インスタンス、およびオンプレミスで稼働するインスタンスを含む、さまざまなインスタンスへのコードのデプロイを自動化するサービス`
- `AWS CodeDeploy はさまざまな設定管理システム、継続統合およびデプロイシステム、ソースコントロールシステムと連動します`
https://aws.amazon.com/jp/codedeploy/product-integrations/
- デプロイのために指定の必要があるパラメーター: 次の 3 つ
リビジョン – 何をデプロイするか指定します。（リビジョン: リビジョンとはソースコード、ポストビルドアーティファクト、ウェブページ、実行ファイル、デプロイスクリプト、 AppSpec ファイルなどのデプロイコンテンツの特定のバージョン）
デプロイグループ – どこにデプロイするか指定します。
デプロイ設定 – デプロイ方法を指定する任意のパラメータです。

- AppSpec ファイル: `AppSpec ファイルとは、コピーするファイルおよび実行するスクリプトを指定する設定ファイルです。AppSpec ファイルは YAML フォーマットを使用しており、リビジョンのルートディレクトリに含める必要があります。`

### Step Functions

- lambdaとlambdaを結合して、判断・例外処理・並列化などを行う。マイクロサービスの統合化（オーケストレーション） / ワークフローの生成
- jsonでフローを定義
- ユースケース
データ処理
Machine Learning
マイクロサービスのオーケストレーション
IT およびセキュリティのオートメーション

- Amazon States Language (ASL) で記載する
例: https://docs.aws.amazon.com/ja_jp/step-functions/latest/dg/concepts-amazon-states-language.html#example-amazon-states-language-specification
- ワークフローには、標準（Standard）とエクスプレス（Express）がある。Expressの方が断然安価なので、通常はエクスプレス。エクスプレスの最大実行時間は5分
https://docs.aws.amazon.com/ja_jp/step-functions/latest/dg/concepts-standard-vs-express.html

### ElastiCache

- `Amazon ElastiCache は、Memcached または Redis プロトコルに準拠したキャッシュのクラウドへのデプロイと実行を効率化するウェブサービスです。ElastiCache は、低速なディスクベースのシステムに依存する代わりに、高速で管理されたインメモリシステムから情報を取得できるようにすることで、アプリケーションのパフォーマンスを向上させます`

### RDS

- サポートするRDB
MariaDB
Microsoft SQL Server
MySQL
Oracle
PostgreSQL

- リードレプリカ: DB インスタンスの読み取り専用コピー。DBエンジン組み込みのレプリケーション機能を使用。

### コマンド

- リクエストの認証情報を確認する

```bash
aws sts get-caller-identity
```
- S3バケットを消す

```bash
aws s3 rb s3://~
```
 - デバッグする

```bash
aws s3 rb s3://~ --debug
```
- `deleteme`という名前のバケット名のバケットをリストする

```bash
aws s3api list-buckets --output text --query 'Buckets[?contains(Name, `deleteme`) == `true`] | [0].Name'
```
- ポリシーの確認

```bash
aws iam get-policy-version --policy-arn [ポリシーARN] --version-id v1
```
- ロールにポリシーをアタッチする

```bash
aws iam attach-role-policy --policy-arn [ポリシーARN] --role-name [ロール名]
```
- ロールにアタッチされたポリシーを確認する

```bash
aws iam list-attached-role-policies --role-name [ロール名]
```
- バケットを作成する
	- us-east-1: バージニア北部の場合と他のリージョンでパラメータが違うので以下のような書き方。

```python
    if current_region == 'us-east-1':
        response = s3Client.create_bucket(Bucket=name)
    else:
        response = s3Client.create_bucket(
          Bucket=name,
          CreateBucketConfiguration={
            'LocationConstraint': current_region
          })
```

