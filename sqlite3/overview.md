---
title: SQLite3 メモ
---

# How to use sqlite3 

## 使い方

```bash
sqlite3 
```

- プロンプトが出たら、.openでデータベースファイルに接続したりする

```bash
.quit
```
または

```bash
;
```
で終了する

### headers 
デフォルトでselectするとカラム名が表示されない.  

```sql
.headers on
```
で表示させる.  

## メタsql

### テーブル一覧

```sql
.tables
```

### カラム一覧

```sql
PRAGMA table_info([テーブル名]);
```

## CSVでインポート

```sql
.mode csv
.import --skip 1 /path/to/user_1001.csv user
```
`skip`はヘッダなどをスキップ

## sql

### 現在時刻、タイムスタンプ

```sql
current_timestamp
```

### 日付演算・表示
- 現在の3日前（日時で返す）

```sql
datetime(current_timestamp ,'-3 days')
```
- 現在の3日前（日付で返す）

```sql
date(current_timestamp ,'-3 days')
```
- 日付表示

```sql
strftime('%Y-%m-%d', timestamp)http://localhost:3000/summaria
```

### テーブルバックアップ

```sql
create table table1_bak as select * from table1;
```

### truncate 

`truncate table`はないので、`delete from`

### 副問合せによる更新
- joinによる更新はサポートしていないらしい。次のsqlは通らない

```sql
update manual_contents_list set description = manual_contents_list_bk.description from manual_contents_list join manual_contents_list_bk
on manual_contents_list.pagepath = manual_contents_list_bk.pagepath;
```
- 代替はこれ

```sql
UPDATE manual_contents_list
SET description = (
    SELECT manual_contents_list_bk.description
    FROM manual_contents_list_bk
    WHERE manual_contents_list.pagepath = manual_contents_list_bk.pagepath
)
WHERE EXISTS (
    SELECT 1
    FROM manual_contents_list_bk
    WHERE manual_contents_list.pagepath = manual_contents_list_bk.pagepath
);
```

### テーブルのリネーム

```sql
ALTER TABLE old_table_name RENAME TO new_table_name;
```

## sql文の吐き出し

```bash
sqlite3 [データベースファイル]
> .output [出力したいsqlファイル]
> .dump [テーブル名(未指定なら全テーブル)]
> .quit 
```

## sql文の実行

```bash
sqlite3 [データベースファイル] < [sqlファイル]
```
