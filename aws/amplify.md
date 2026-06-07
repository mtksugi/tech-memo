---
title: Amplify Hosting（静的 SPA の公開）
lang: ja
---

# Amplify Hosting（静的 SPA の公開）

**AWS Amplify Hosting** は、Git 連携で静的サイト（React / Vite 等）をビルド・公開するマネージドサービス。裏側はおおむね **S3 + CloudFront** 相当で、バケット・CDN・HTTPS・SPA 用 rewrite・デプロイをコンソール中心でまかなえる。

公式: [AWS Amplify](https://aws.amazon.com/amplify/)

---

## いつ使うか

| 目的 | 選択の目安 |
|------|------------|
| 社内モックをすぐ URL で渡す | Cloudflare Pages / Vercel も同程度に手軽 |
| **AWS アカウント内で完結**したい | Amplify Hosting |
| インフラを自分で理解したい | S3 + CloudFront 直叩き（手順は多い） |

Vite + React の **CSR SPA**（`BrowserRouter`）が典型用途。

---

## 1. コンソールでアプリ作成（GitHub 連携）

1. **AWS コンソール** → **Amplify** → **新しいアプリを作成** → **GitHub をホスト**。
2. GitHub 認可（初回のみブラウザで GitHub 側の操作あり）。
3. リポジトリと **デプロイするブランチ** を選択（`main` 以外の feature ブランチでも可）。
4. ビルド設定（次節）。
5. **保存してデプロイ** → `https://<branch>.<app-id>.amplifyapp.com` が発行される。
6. 以降、そのブランチへの **push で自動ビルド・デプロイ**（URL は変わらない）。

`amplify.yml` は **なくても** コンソール設定だけで動く。後から YAML に移してもよい。


---

## 2. モノレポ（アプリのルート）

リポジトリ直下に `package.json` がなく、サブフォルダにアプリがある場合は **「私のアプリケーションはモノレポです」にオン**。

| 項目 | 値（例） |
|------|----------|
| モノレポ | **オン** |
| アプリのルート（`AMPLIFY_MONOREPO_APP_ROOT`） | `myproj-mock` |
| ビルドコマンド | `npm ci && npm run build` |
| 成果物ディレクトリ（baseDirectory） | `dist` |

チェックを入れないとリポジトリルートで `npm ci` し、**ビルド失敗** しやすい。

---

## 3. ビルド設定の詳細（デフォルトでよいもの）

初回デプロイでは次を **デフォルトのまま** でよいことが多い。

| 項目 | 目安 |
|------|------|
| ビルドイメージ | Amazon Linux 2023 |
| `AMPLIFY_DIFF_DEPLOY` | `false`（初回は気にしなくてよい） |
| Cookie キャッシュキー | オフ（静的 SPA では不要） |
| SSR ログ | オフ（Vite CSR のため不要） |
| ビルドインスタンス | Standard（小規模モックで十分） |

---

## 4. パスワード保護（Basic 認証）

Step 3 などで **パスワード保護を有効** （Basic 認証の追加）にできる。

---

## 5. URL の挙動

```
https://main.d1v2pcxxx.amplifyapp.com
        │   └─ アプリ ID（作成時に固定）
        └─ ブランチ名
```

| 操作 | URL |
|------|-----|
| 同じブランチに push して再デプロイ | **同じ** |
| 別ブランチを Amplify に追加 | **別 URL**（ブランチごと） |
| アプリを削除して作り直す | **変わる**（アプリ ID が変わる） |

---

## 6. 料金（Hosting のみ・ざっくり）

**月額固定はない**（従量課金）。小規模モックなら実質無視できるレベル。

| 項目 | 無料枠の目安 | 超過後 |
|------|-------------|--------|
| ビルド時間 | 月 1,000 分 | $0.01/分 |
| 配信（転送） | 月 15 GB | $0.15/GB |
| 保存 | 月 5 GB | $0.023/GB・月 |

静的 SPA（SSR なし）なら SSR 用課金は不要。WAF 等は通常不要。

