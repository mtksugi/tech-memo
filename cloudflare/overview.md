---
title: Cloudflare メモ
---

# Cloudflare

## R2

bucketを作ったら、aws cliで操作する

- .aws/configに設定追加
```config
[default]
region = ap-northeast-1
output = json

[profile cf]    # ←cloudflareのprofileを追加する
region = auto
output = json
```

- s3コマンド
```bash
aws s3 list s3://[bucket name] --recursive --endpoint-url https://431da4a1e9b60560fd44c4c44f3e6e98.r2.cloudflarestorage.com --profile cf 
```

## wrangler

- インストール
```bash
npm install wrangler
```
- ログイン
```bash
npx wrangler login
```
- プロジェクトの作成...今回プロジェクト名に意味はない
```bash
npx wrangler pages project create [プロジェクト名]
```
コンソールに従ってプロジェクト名とブランチ名を入力する

- デプロイ
```bash
npx wrangler pages deploy [publicなどのフォルダ名]
```
このあと、ダッシュボードでこのドメインに対してカスタムドメインを設定する。  
pagesは必ず、~.pages.devへのCNAMEでカスタムドメインを紐つける、という手法になる模様。

- 修正後の更新
```bash
npx wrangler pages deploy [フォルダ名] --project-name [プロジェクト名]
```


## tunnel

https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/get-started/create-remote-tunnel/

- Zero Trustを始める。（チームの作成を促される？がチーム名はあとから変えられる。Tunnelには関係ない）
- Networks > Tunnels から、Tunnelの作成
  - コネクタ名を入れる（特に意味なし）
  - 次のページで画面に表示されているコマンドを実行（環境ごと）
    - cloudflaredをインストール
```bash
brew install cloudflared && 
sudo cloudflared service install eyJhI~~(keyと思われる)
```

- localhost:5000に対してtunnelを設定
```bash
cloudflared tunnel --url http://localhost:5000
```

複数設定可能。  
毎回別の名前になる。  
固定ドメインを保有すれば固定名を指定できるらしい。  
