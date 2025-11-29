---
title: Git/GitHub メモ
lang: ja
---

# Git/GitHub

## MacのGit
- デフォルトでXCodeのApple Gitというものが入っている. 新しくするにはbrewでインストールする.
- 確認する

```
git version
```
- 出力結果が↓ならApple Git
> git version 2.21.1 (Apple Git-122.3)

- 本家をインストール

```
brew install git
```

## 始め方

1. ローカルでプロジェクトを作る

以下でローカルフォルダにgitのローカルプロジェクトが作成される

```bash
git init
git add .
git commit -m 'first create'
```
2. GitHub上でリポジトリを作る（README.mdファイルを作成しないで作成）

ローカルで、画面に表示される以下のコマンドを実行すればよい

```bash
git remote add origin https://github.com/[ユーザ名]]/[プロジェクト名].git
git branch -M main	# ←ブランチ名をmainに変更
git push -u origin main
```

### 既存のディレクトリにソースがあってそれをリモートリポジトリに接続する方法

```bash
git init
git remote add origin https://~.git
git fetch
git reset --hard origin/main  # 強制的にリモートブランチの最新のコミット状態にリセットする
git pull origin develop
```

## git push -u origin mainでAuthentication errorになる場合

- gitはパスワード認証を認めていなくて、OSやデバイスに保存する認証情報を使用するので、過去の情報が残っていたり、それに登録がないとこのようなエラーになる

## 既存のプロジェクトを使用する場合

`git pull`ではなく`git clone`することで、自動的に`git remote add origin`されたことになり、接続が生成される

### ブランチ指定でcloneする

```bash
git clone -b [branch name] [url]
```

## 接続の確認

```bash
git remote -v
```

## ブランチの表示/作成
- 表示

```bash
git branch
```
- リモートリポジトリのブランチ表示

```bash
git branch -r
```
- 作成

```bash
git branch [ブランチ名]
```

### ブランチを作成してから空コミット | commit -a

```bash
git commit -a
```

## ブランチの切り替え
`release-1.39`のブランチから`master`に切り替える（masterに最新化する）場合

```bash
git checkout master
```
- リモートリポジトリのブランチをローカルに取得してチェックアウト

```bash
git checkout -b ローカルブランチ名 origin/リモートブランチ名
```

## ブランチの削除

```bash
git branch -d [ブランチ名]
```

## ブランチのマージ
- github上でpull requestして、開発ブランチからステージブランチにマージしたりするが、コンフリクトするファイルがあると調整する必要がある。

- feature/1からdevelopブランチにマージする手順

```bash
git checkout develop 	# developに移動
git pull origin develop	# developブランチを最新にする
git checkout feature/1	# feature/1に移動
git merge develop 		# developをfeature/1にマージする...このあとコンフリクトを解消する作業を行う
git push origin feature/1	# feature/1をリモートにプッシfュする
```
つまり、pull requestでdevelopとfeature/1を同期させた上でdevelopにマージするために、まずはfeature/1に対してdevelopを適用する必要がある

### マージの取り消し

```bash
git merge --abort
```

## 特定のコミットの反映 | cherry-pick

```bash
git cherry-pick [c2b9b96907b7~などのcommit hash値]
```
- commitは範囲で指定できる.  hashを..でつなげる

```bash
git cherry-pick fe87c8ab11638c6c73db6374fb47c02236a5720a..4d270af741fc5d3fdcc4247fe1c8f2f9b8f10d12
```
このとき、開始点のcommitは含まれないので注意.  
逆に言えば、指定するのは反映済のcommitを指定すればよい.  
開始点を含めたい場合は、

```bash
git cherry-pick fe87c8ab11638c6c73db6374fb47c02236a5720a^..4d270af741fc5d3fdcc4247fe1c8f2f9b8f10d12
```
で、`^`をつける

## commitを調べる | log

```bash
git log
```
特定ブランチのcommitを知りたい場合は、続けてブランチ名でよい.  

## commitされたファイルの一覧を出力する | show

```bash
git show --pretty=oneline --name-only <commit_hash>
```
`--pretty=oneline`: コミットのハッシュとメッセージを1行で表示します。--name-only と組み合わせることで、ファイルリストの前にコミット情報が付加されます。  
`--name-only`: 変更されたファイルの名前だけを表示し、実際の差分（diff）は表示されません。

## 変更を退避する | stash
ローカルソースを変更中に、別のcommitを取り込みたいとき、変更内容が破棄されてしまうので、一旦退避して戻すことができる.

```bash
git stash -u
```
-u: untrackedファイルも含めて保存. -uなしならuntrackedファイルは無視

```bash
git stash push -m "メモ"
```
で退避して、

```bash
git stash apply stash@{n}
```
で戻す.  `stash@{n}`はstashした履歴のどれを戻すか指定する.  

```bash
git stash list
```
で表示される先頭の文字.  

`stash`したファイルを表示するには、

```bash
git stash show -p stash@{0}
```

特定のファイルのみ戻したい場合は、

```bash
git checkout stash@{0} -- path/to/file
```
とする.  

stashリストは消えないので、

```bash
git stash drop stash@{n}
```
で削除できる.  もしくは`apply`ではなく`pop`でもいいらしい.  

## コミットを戻す（コミットの取り消し） | reset / revert
- 履歴を残さない取り消し
- commit hash値は戻したいcommitの一つ前までを指定する

```bash
git reset [commit hash値]
```
- 履歴を残す（新たに戻すcommitを残して取り消し）
- commit hash値は直前から順番に指定する。もしくは、..で繋いでcommit hashの範囲を指定する。

```bash
git revert [commit hash値]
```
- git revertの取り消しは、`git revert --abort`

その後リモートリポジトリにもそれを反映させるには以下を実行

```bash
git push origin [ブランチ名] --force
```
逆にリモートブランチでローカルを強制的に合わせるには以下

```bash
git reset --hard origin/[ブランチ名]
```

commit hash値のところはHEAD^とすることで、直前のcommitを取り消すことができる.   
`reset`には`--soft`、`--hard`、`--mixed`のオプションがある.  

例えば上記の例として、最新から5個のcommitがあって、4番目まで戻して、1番目のコミットだけ適用したいみたいな場合、

```bash
git reset --soft [4番目の前のcommit hash値]  # これで最新から4番目までのコミットがなくなる
git log  # 確認する
# vscode上は1番目から4番目の変更がステージングされているので、必要なものだけにする
git commit -m "message" # 1番目の変更内容をコミットする
git push origin [ブランチ名] --force # リモートと同期する
```
これでリモートブランチとローカルブランチは戻る。
このリモートブランチを他の人が`git pull`するとコンフリクトしてしまうので、

```bash
git fetch origin
git reset --hard origin/<branch-name>
```
として今度はリモートブランチと強制的に同期をとる


### resetの別クライアントでの反映

`reset`とすると、別のクライアントでresetする前のコミットを取得済の場合、`git pull`しても取り消したcommitは取得できなくなってしまう。そのクライアント側でも`reset`する必要がある。

`reset`して、

```bash
git checkout -- [ファイル名]
```
としてリモートリポジトリのファイルを再取得する必要がある。


### 最新をpullしたらバグってたので任意のコミット箇所に戻す

この場合もresetでよい

```bash
git reset --hard [commit hash値]
```

### 直前のコミットメッセージを間違えたのでやり直す

```bash
git reset --soft HEAD~1
# リモートに反映済みの場合
git push --force-with-lease origin [ブランチ名]
```

`--soft`は変更は保持するオプション。変更も取り消すのは`--hard`。  
`--force-with-lease`は、他の人がpushしていたら防ぐオプション。気にしない場合は、`--force`。  

## git pull=git fetch & git merge

`git pull`はfetchしてmergeすること。  
`git fetch`は、リモートリポジトリの最新の変更（コミット、ブランチ、タグなど）をローカルリポジトリにダウンロードしますが、現在の作業ブランチの作業ツリー（ワーキングディレクトリ）やインデックス（ステージングエリア）は変更しません。  
`git merge`は作業ツリーやインデックスに統合すること。

## 別ブランチの特定のファイルを取り込む

作業中（取り込み先）のブランチで

```bash
git checkout [別ブランチ名] -- /path/file.js
```

## git pullなどで`SSL certificate problem: unable to get local issuer certificate`エラー

一時的にSSL認証を無視するオプションを付けて実行

```bash
git -c http.sslVerify=false pull
```
基本的にgitのバージョンが古いケース。gitをバージョンアップする。


## git pullの強制 | reset --hard
ローカルファイルを変更していて、`git pull`が効かないが、リモートリポジトリで強制的に上書きしたい場合、
（masterブランチを上書きする）

```bash
git fetch origin master
git reset --hard origin/master
```

## ディレクトリのみリポジトリに入れるgitignoreの書き方

[log]ディレクトリのみリポジトリに入れる場合、log/.gitkeepファイルを作成して以下の記述

```text
log/*
!.gitkeep
```
!は直前ルールの否定. 

## git worktree

- 複数ブランチをローカルで運用するときに、ブランチごとに別フォルダを作れる
- 毎回 `git checkout`しなくて便利

- 作成:

```bash
git worktree add -b feature/foo /mydir/feature-foo
```
* 既存ブランチを使う場合は逆で指定する

```bash
git worktree add /mydir/feature-foo feature/foo
```

- 一覧/削除:

```bash
git worktree list
git worktree remove /mydir/feature-foo   # 片付け
git worktree prune                       # 孤児掃除
```

## .gitignoreでgitの追跡を解消する

すでにgit管理済みのファイルをあとから.gitignoreにファイルを追加しても、対象外にできない。
その場合は以下の手順が必要。

```bash
git rm --cached [ファイル名] 	# gitのcacheを削除する（ファイルはそのままになる）
git commit -m "ファイルを削除などのメッセージ" 	# コミット
```
このあと、.gitignoreファイルに対象ファイルを記載する
 
## git アカウント
一つのPCで複数のgitアカウントを使う場合は、グローバル設定がデフォルトになるので要注意。
グローバル設定を変更するには

```bash
git config --global user.name "change name"
git config --global user.email "change email"
```

## git difftool

別ブランチの同一ファイルをビジュアルに比較できるツール
- 設定（比較結果をvscodeで開く設定）

```bash
git config --global diff.tool vscode
git config --global difftool.vscode.cmd "code --wait --diff $LOCAL $REMOTE"
```
- 比較

```bash
git difftool ブランチ1 ブランチ2 -- path/to/file
```

## Git CLIでissueを出力

以下でタブ区切りのリストを出力できる
https://cli.github.com/manual/gh_issue_list

```bash
gh issue list -s all -a [ユーザ名] -L 100
```
`-s`: open | closed | all  
`-a`: アサインユーザー名  
`-L`: 出力行数（デフォルト30件）  

コンソール表示では、タイムスタンプが`about 5 days ago`などとなるが、標準出力すればタイムスタンプになる。  
タイムスタンプはclose issueならcloseした日付。

## Macの場合

↓に従ってghで認証情報を作成する  
https://docs.github.com/ja/get-started/getting-started-with-git/caching-your-github-credentials-in-git

```bash
brew install gh
gh auth login
```
すると選択式で進む  
GitHub.com/GitHub Enterprise
https/SSH→https  
??/GitHub CLI→GitHub CLI  
でGitHubにブラウザからログインして、表示されている認証コードを入力する

macの場合キーチェーンアプリにGitHub.comの認証情報が追加される
