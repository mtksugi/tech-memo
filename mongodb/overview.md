---
title: MongoDB メモ
---



# MongoDB

## installation
バイナリ版をダウンロードして解凍.  
解凍したものを/User/[name]の下にコピー.  
/usr/local/binにリンクする.  

```bash
sudo ln -s /Users/[name]/mongodb/bin/* /usr/local/bin/
```
## Run
dataフォルダを作って、mongdを実行.  

```bash
mongod --dbpath /Users/[name]/mongodb/data
```
公式はこれ.    

```bash
mongod --dbpath /usr/local/var/mongodb --logpath /usr/local/var/log/mongodb/mongo.log --fork
```
デフォルトポートは27017

## Stop
Macの場合、killでよい

```bash
kill [mongodのプロセスID]
```

linuxなら

```bash
mongod --shutdown
```
らしい


## DocumentのObjectId()

ObjectIdは12byteのバイナリデータである.  
> -   A 4-byte timestamp, representing the ObjectId's creation, measured in seconds since the Unix epoch.
> -   A 5-byte random value generated once per process. This random value is unique to the machine and process.
> -   A 3-byte incrementing counter, initialized to a random value.
先頭がUnixエポックの日時を表す


