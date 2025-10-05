---
title: GitHub IO メモ
lang: ja
---

# GitHub IOの特徴

- リポジトリは.mdファイル（マークダウン）のファイルのまま、公開ではhtmlとして公開することができる
- ディレクトリ直下の.mdファイルもhtmlになるので以下のような構成にすればそのまま公開できる

```
.
├── algorithm
│   └── overview.md
├── aws
│   ├── certification.md
│   ├── cli.md
│   └── lambda.md
...
```


# GitHub IOの始め方

- GitHubリポジトリのSettings - Pagesで、"Build and deployment"の中のBranchからIOで公開したいブランチを選択して"Save"するだけ
- 公開URLは `https://<ユーザー名>.github.io/<リポジトリ名>/`となる


# 検索機能をつける

- 調べると、`Simple-Jekyll-Search`で簡単なのは可能というのもでてくるが試したところ全然使えなかった
- GitHub Actionsで`Pagefind`をインストールすることでこのサイトの[トップページ](https://mtksugi.github.io/tech-memo/)で実現している検索ボックスを追加できる


## Pagefindの設定

### github actionsを追加する

- `.github/workflows/pages.yml`を追加する。
  - [pages.yml](https://github.com/mtksugi/tech-memo/blob/main/.github/workflows/pages.yaml)の通り
  - `chmod`が必要っぽい
- 対象ブランチにこのファイルをコミットすると、push 時に自動でデプロイ（github actions）が走る
  - 以降はページ変更の都度、github actionsが走ってPagefindのインデックスが構築される



# カスタムドメインを追加する

公式は[これ](https://docs.github.com/ja/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)

- GitHubリポジトリのSettings - Pages - Custom domain にドメインを入力する
- 自分のDNSのホストゾーンに、`custom domain` -> `<ユーザー名>.github.io.`のCNAMEレコードを追加する
- リポジトリ直下にCNAMEファイルができる。これがあると証明書を作ってくれるらしいので削除しない

# favicon

直下にfavicon.icoを置くことで自動的にファビコンを設定してくれる

