---
title: MySQL メモ
lang: ja
---


# MySQL

## Macにインストール

[Archive](https://downloads.mysql.com/archives/community/)からOSバージョンにあうDMGパッケージをダウンロードする.  
macOS 10.14のMojaveは8.0.18.  

インストール後、bash_profileにパス（/usr/local/mysql/bin）を追加する

```bash:.bash_profile
export PATH="/usr/local/mysql/bin:$PATH"
```
反映は、

```bash
source .bash_profile
```

## サービスの起動、停止

- パッケージで入れた場合
システム設定にMySQLアプリの設定があるのでその中で停止できる  
また、自動起動のオンオフもある

- brewで入れた場合
開始

```bash
brew services start mysql
```
停止

```bash
brew services stop mysql
```

## MySQL Clientの使い方

- mysqlの起動

```bash
mysql -u [ユーザ名] -p
```
パスワードを聞かれるので入力する.  

- データベースを指定する場合

```bash
mysql -u [ユーザ名] -p [データベース名]
```

- sqlの実行

```bash
mysql -u root -p -e "select ... from tbl1" [database名] --batch --silent
```

- sqlファイルの実行

```bash
mysql -u root -p [database名] < [sqlファイル]
```

- データベースに接続

```sql
connect [データベース名]
```

## SQL
### メタ情報
- get column list

```sql
describe [table_name];
```

- get table list

```sql
show tables;
```

- get database list

```sql
show databases;
```
### create database

```sql
create database [db名];
```

### テーブルバックアップ

```sql
create table table1_bak as select * from table1;
```

### 日付関数
- UTC時間を日本時間で表示する

```sql
CONVERT_TZ(current_timestamp, 'UTC', 'Asia/Tokyo')
```

## 設定 Configuration
### config file
/usr/local/mysql/my.ini　などに書く。

```text
[mysqld]
max_allowed_packet=128M
```
AWS RDSなどはコンソールから設定する。
Macはシステム設定にMySQLがあり、そこで設定したりする。


### 接続関係

- 最大接続数の表示

```sql
SHOW GLOBAL VARIABLES LIKE 'max_connections';
```
一般的には`max_connections`のデフォルト値は151。この値は設定ファイル（通常は`my.cnf`または`my.ini`）で確認および変更することができる。  
AWS RDSの場合は、`DBInstanceClassMemory/12582880`で計算されて設定されるらしい。

- 最大接続数の変更

```sql
SHOW VARIABLES LIKE 'max_connections';
```

- 現在の接続数の表示

```sql
show status where `variable_name` = 'Threads_connected';
```
### パケットサイズ（SQLサイズ）

```sql
SHOW VARIABLES LIKE  'max_allowed_packet';
```
デフォルト32M。LONGBLOBなどを使う場合は大きくする必要がある。

### トランザクションの開始

rollbackで確認したいときは、`set autocommit`を実行して、`start transaction`する必要がある。

```sql
set autocommit = 0;
start transaction;
update tab1 set col1 = 'hoge' where key1 = 'key-1';
rollback;
```

commitは`commit`でよい

## connect for Python

pythonでmysqlを使うライブラリはいくつかあるよう（mysqlclient, MySQL-Python, pymysql）だが、mysqlclientを使って`NameError: name '_mysql' is not defined`のエラーが出た場合は、シェルに以下の環境変数を追加したら解消した.  

```bash:.bash_profile
export DYLD_LIBRARY_PATH="/usr/local/mysql/lib:$PATH"
```

## rootのパスワード変更
mysqlにログイン

```bash
mysql -u root -p
```

```sql
FLUSH PRIVILEGES;
ALTER USER 'root'@'localhost' IDENTIFIED BY 'newpassword';
FLUSH PRIVILEGES;
```
`newpassword`は空にもできる  
このあとサービスを再起動する

## パスワード認証方式

mysql8.0からパスワードの認証方式が違うらしい。  
一部のライブラリで接続できない事象が発生。（VSCodeプラグインなどでも発生する）  
`caching_sha2_password`が新しい認証方式。  
`mysql_native_password`にすると接続できるようになる。  
以下で変更する。（mysqlのuserごと）  

```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password'
```




