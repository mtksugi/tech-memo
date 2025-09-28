---
title: PostgreSQL メモ
---

# How to use PostgreSQL

## install for mac

- homebrewで入れる方法
- [パッケージ](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)を入れる方法　

- パッケージで入れるとパスが通らないが、launchpadにpsqlのシェルができる

## サーバ起動
正式は

```bash
su postgres pg_ctl start -D [データファイル]
```

Macの場合は、/Library/LaunchDaemonsに以下で入っている

```bash
/Library/PostgreSQL/14/bin/postmaster -D /Library/PostgreSQL/14/data
```
実際は`postgres`ユーザで起動しなければいけないためコマンドラインで、バックグラウンド起動するなら以下.  

```bash
su postgres -c '/Library/PostgreSQL/14/bin/postmaster -D /Library/PostgreSQL/14/data >/tmp/postgres-log 2>&1 &'
```


## サーバ停止
Linux系はKillでいいらしい

```bash
kill -INT `head -1 /usr/local/pgsql/data/postmaster.pid`
```
Macの場合、パスは `/Library/PostgreSQL/14/data/postmaster.pid`

pg_ctlでも落とせるが、自分のMacでは以下は効かない

```bash
su postgres pg_ctl stop -D
```

## psqlの起動
- macでパッケージから入れた場合

```bash
sudo /Library/PostgreSQL/14/bin/psql -U postgres
```
## install for linux
- postgresのインストール

```bash
sudo apt-get install postgresql  
```  
- サービス確認

```bash
systemctl status postgresql.service
```
- サービス再起動

```bash
systemctl restart postgresql.service
```
- psqlでpostgresユーザでログイン

```bash
sudo -u postgres psql  
```  
- postgresにubuntuユーザがログインできるようにする

```sql
create role ubuntu LOGIN;  
```  
- ubuntuユーザがpostgresデータベースに接続

```bash
psql -d postgres  
```
- インストール時、postgresユーザのパスワードは未設定、psqlで以下を実行する

```sql
alter user postgres PASSWORD '新しいパスワード'
```
- pythonなどでパスワードでログインが必要な場合は、etcの中にある`pg_hba.conf`ファイルを編集する

```text
# TYPE  DATABASE        USER            ADDRESS                 METHOD
local all all peer
```
`peer`はOS認証でのログインのみ許すという意味なので、パスワード認証を許す`md5`にする  
変更したらサービス再起動と、パスワードファイルの作成（下に記載）


## sqlファイルの実行
- psqlで

```bash
psql>\i [sqlファイル]
```
- psqlを外から

```bash
psql -U postgres < [ファイル名]
```

## カラムの表示
- `select`でカラム名が表示されない場合は、`\t`で表示切り替え

## postgresバックアップ

- 基本コマンド

```bash
pg_dump -U postgres > backup.sql
```
sqlがそのまま吐かれる

- データのみ : -a / --data-only
- パスワード省略 : -w
- テーブル指定: -t / --table  ... 複数テーブルは -t を複数指定. 

## バックアップ&リストア
主にオブジェクトをすべて上書きする場合.  
- pg_dump... -c: clean と-if-existsを指定してバックアップ

```bash
pg_dump -U postgres -w -c --if-exists | gzip > [ファイル名]
```
- リストアはpsqlで実行

```bash
psql -U postgres < [ファイル名]
```

## パスワードファイル

- [パスワードファイル](https://www.postgresql.jp/document/11/html/libpq-pgpass.html)
  - ファイル ~/.pgpass を作ってパスワードを登録する

```
# hostname:port:database:username:password
*:*:*:*:[パスワード]
```

- パスワードファイルは自分のみ読み取り専用にする

```bash
chmod 0600 .pgpass
```

## ログファイル

- パッケージでインストールしたMacの場合 : /Library/PostgreSQL/14/data/log

## CSVファイルに出力
- psql -c "SQL" -A -F, > [ファイル名]

```bash
psql -U postgres -c "select product_id, title from movie;" -A -F, > movie_title_2.csv
```
- カンマ区切りは`-F,` タブ区切りは`-F $'\t'`

- 改行含むデータをダブルクォーテーションでくくりたいときは以下

```bash
psql -U postgres -c "COPY (select * from maker where id > ??) TO STDOUT WITH (FORMAT csv, HEADER false)" >  maker.tsv
```
これだと次のcopyコマンドでそのままインポートできる

## CSVファイルをインポート
SQLで実行

```sql
COPY maker FROM '/tmp/maker.tsv' WITH (FORMAT 'csv', DELIMITER E'\t', NULL '');
```


## 実行されたSQLをログに吐き出す（performance monitorっぽいやつ）
- 以下を実行する

```sql
ALTER SYSTEM SET log_statement = 'all';
SELECT pg_reload_conf();
```
- Macなら [/Library/PostgreSQL/14/data/log/postgresql-2022-07-22_000000.log] のログファイルにSQLが吐かれるようになる.


## SQL Tips
###  システムカタログ
- テーブル一覧 : pg_tables
- psqlでテーブル一覧

```bash
psql>\dt
```
- psqlでカラム一覧

```bash
psql>\d [テーブル名]
```

### cast

```sql
cast(hoge as numeric)
cast(hoge as character varying)
```

### getdate()

- date型

```sql
select current_date;
```
- timestamp型

```sql
select current_timestamp;
```


### Truncate table

```sql
truncate table [テーブル名] RESTART IDENTITY CASCADE;
```
RESTRICT : 外部キー制約がある場合は削除しない. ←デフォルト   
CASCADE : 外部キーを持つテーブルまで合わせて削除.  
CONTINUE IDENTITY : シーケンスを戻さない. ←デフォルト   
RESTART IDENTITY : シーケンスを戻す.  

### Drop table
ドロップも同様に外部キーがある場合は`cascade`の指定が必要.  
（外部キー指定がある場合、`cascade`未指定はdropが拒否される）  

### Auto Number型
- serial型とsequence型がある
- serial型は特に気にしない. SQL Serverのidentityと同じ

```sql
create table tab1 (  
auto_no serial ,
...
```
- sequence型はsequenceを事前に作る必要がある. djangoでmigrateで生成されるのはこっち

```sql
create sequence next_movies_id_seq start with 1;
CREATE TABLE IF NOT EXISTS public.next_movies (
id bigint NOT NULL DEFAULT nextval('next_movies_id_seq'::regclass),
...
```

### sequenceのリセット

- sequenceはデータをcopyなどで生成ときは別で設定しなければならない
- 現在の設定値を確認

```sql
select currval('actresses_id_seq');
```
- actressesテーブルのidを最大値で設定

```sql
select setval('actresses_id_seq', (select max(id) from actresses));
```

### 副問合せで更新
- SQLServerで

```sql
update a set col1 = b.col2 from tab1 a join tab2 b on a.col3 = b.col3 where ...
```
と書くやつは、

```sql
update tab1 set col1 = b.col2 from tab2 b where tab1.col3 = b.col3 where ...
```
と書く.  

### テーブルバックアップ
- SQL Serverで

```sql
select * into table1_bak from table1
```
と書くやつは、

```sql
create table table1_bak as select * from table1;
```
と書く.  

### random select

- 以下が簡単だが、全件走査が入るっぽい

```sql
where random() < 0.01 limit N
```
または

```sql
ORDER BY random() LIMIT 1;
```
`order by` でやるなら`where id = (select id ... order by random() limit 1`などで主キーのみにした方がまだマシ

- postgres公式のサンプリング方法は以下

```sql
select * from [table] tablesample system_rows(N)
```
- system_rowsを有効にするために事前に以下を実行

```sql
CREATE EXTENSION tsm_system_rows;
```
ただし、少数レコードには効かない
> SYSTEM_ROWSはブロックレベルのサンプリングを行うため、サンプルは完全にはランダムではなく、特にごく少数の行が要求されたときはクラスタリングの影響を受けます。


### 正規表現で置換
- regexp_replace(source, regex, replacement)

```sql
update movie set  title = regexp_replace(title, '【MGS[^】]+】', '')  where title like '%【MGS%';
```



## pythonでのpsycopg2を使った接続文字列

```python
import psycopg2
DSN = 'dbname=postgres user=postgres'
con = psycopg2.connect(DSN)
```
- 上記のとき、postgresがlinuxのsocketのデフォルトの/var/postgres？を指すらしい
- /tmpにsocketを作る場合は以下を指定

```python
DSN = 'dbname=postgres user=postgres host=/tmp/'
```

## socketの調べ方
/etc/postgres/(バージョン番号)/main/postgresql.conf
ファイルの中の`unix_socket_directories`にパスが書いてある
