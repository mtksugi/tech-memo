---
title: GCP CLI/SDK メモ
---

# Google Cloud CLI（Cloud SDK）

## インストール

これに従う
https://cloud.google.com/sdk/docs/install-sdk#mac

- インストーラダウンロード
- 展開
- install.shの実行
- 初期化
```
gcloud init
```
これでgoogleアカウントを認証する
プロジェクトを選択するリストが出てくるので最初にプロジェクトを作っておく

- コンポーネントのインストール
```
gcloud components update
gcloud components install beta
```
- デフォルトリージョンの設定
```
gcloud config set functions/region asia-east1
```

- python環境でのライブラリのインストール
```
pip install --upgrade google-cloud-storage
```
## ローカルでの開発

- Functions Frameworkのインストール
```
pip install functions-framework
```
- http関数の場合、Functions Frameworkを実行すると、localhost:8080で結果を見ることができる
```
functions_framework --target=YOUR_FUNCTION_NAME
```
- ↓はjsonでデータを渡す例
```
curl http://localhost:8080 -H "Content-Type:application/json"  -d '{"name":"Jane"}'
```
- function_frameworkの引数は、port(=8080)、signature-type(=http)がある（カッコ内はデフォルト）

## デプロイ

- Google Cloud上でプロジェクトを新しく作った場合は、
```
gcloud init
```
でプロジェクトを選択し直して、以下を実行. 
```
gcloud functions deploy ExtractRivalKeywords --entry-point extractRivalKeywords --runtime python38 --trigger-http --region asia-northeast1
```
トリガーの種類は↓に
https://cloud.google.com/functions/docs/concepts/events-triggers


