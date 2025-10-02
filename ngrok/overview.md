---
title: ngrok メモ
lang: ja
---

# ngrok

tunnelを使うためにインストールしたが、cloudflareで事足りたので使わなかった

## tunnel

https://dashboard.ngrok.com/get-started/setup/macos

- インストール

```bash
brew install ngrok
```

- config

```bash
ngrok config add-authtoken 2zkx0xxx
```

- tunnel
    - 1つ起動する

```bash
ngrok http http://localhost:5000
```
personalプランまでは1つしか起動できない。

## 複数tunnelする方法

- configファイルを以下のように記入する

```ngrok.yml
version: 3
agent:
  authtoken: 2zkx0xxx
endpoints:
  - name: web
    url: https://
    upstream:
      url: http://localhost:3000
  - name: api
    url: https://
    upstream:
      url: http://localhost:5000
```
ngrok.ymlファイルは、"/Users/[ユーザー名]/Library/Application Support/ngrok/ngrok.yml"

- start

```bash
ngrok start web api
```

接続がないと10分くらいで切れてしまう
