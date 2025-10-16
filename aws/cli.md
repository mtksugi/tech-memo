---
title: AWS CLI メモ
lang: ja
---



# AWS CLI

## configure
~/.awsに`config`と`credentials`ができる.  

```bash
aws configure
```
で生成できる.  

## profile
configファイルはプロファイルごとに設定し、CLIをプロファイルごとに実行できる。

```text: config
[default]
region = ap-northeast-1
output = json

[profile cf]
region = auto
output = json
```

```text: credentials
[default]
aws_access_key_id = AKIxxx
aws_secret_access_key = iB/xxx

[cf]
aws_access_key_id = xxx
aws_secret_access_key = xxx
```
cfの部分がプロファイル名。`aws`コマンドでは以下のように指定。

```bash
aws s3 cp [from] [to]  --profile [プロファイル名]
```
boto3では以下のように指定。

```python
import boto3
aws_session = boto3.Session(profile_name="[プロファイル名]")
s3 = aws_session.client('s3')
```

## S3

### list
/media/image/300で始まるファイルの取得

```bash
aws s3 ls s3://s3-url/media/image/300
```

### upload
カレントフォルダのファイルを全て`s3://s3-url/media/image`にアップロード

```bash
aws s3 cp . s3://s3-url/media/image --recursive
```
-- recursive: フォルダの中の全ファイルの指定

### mv
`s3-url/media/stream`にあるファイルをすべて`s3-url/media/movie`に移動

```bash
aws s3 mv s3://s3-url/media/stream s3://s3-url/media/movie/ --recursive
```
- 大量データをローカルPCからコマンド発行したら途中でaccess deniedになった.  aws lightsailから実行すると継続できた. 大量のapi発行は制限がかかっているのかもしれない


### rm: フォルダを丸ごと削除する

```bash
aws s3 rm s3://bucket/image/venv/ --recursive 
```

#### --dryrun: 削除対象を確認する

```bash
aws s3 rm s3://bucket/image/venv/ --recursive --dryrun
```

#### 特定拡張子以外を削除

ex. jpegファイル以外を削除

```bash
aws s3 rm s3://bucket/media/image/ --recursive \
  --exclude "*.jpg" --exclude "*.jpeg" --exclude "*.JPG" --exclude "*.JPEG" \
```

### cloudflare R2の場合
1. regionは`auto`とする。
2. エンドポイントを指定する。
例: aws CLIでの指定

```bash
aws s3 cp . s3://dir  --recursive  --endpoint-url  https://[cloudflareアカウントID].r2.cloudflarestorage.com  --profile [プロファイル名]
```
例: boto3の場合

```python
import boto3
aws_session = boto3.Session(profile_name="[プロファイル名]")
s3 = aws_session.client('s3', endpoint_url="https://[cloudflareアカウントID].r2.cloudflarestorage.com")
```

