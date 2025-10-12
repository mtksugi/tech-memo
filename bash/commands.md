---
title: Bash コマンドメモ
lang: ja
---

# bash 色々

## lsオプション
- 縦に並べる: -1
- 更新日時降順: -t
- 逆順: -r
- 更新日時降順: -tr
- サブディレクトリ: -R
- M, Gのサイズ表記: -lh


## フォルダの中のファイル数を調べる

```bash
ls -1 | wc -l
```
wcはファイル、標準入力の行数や文字数を表示するコマンド

サブディレクトリ含む場合は

```bash
find /path/to/directory -type f | wc -l
```

## フォルダサイズ

```bash
du -h -s [ディレクトリ名]
```
-h: M, Gのサイズ表記  
-s: フォルダ合計  
-x: そのディスクのみ。外部媒体を含めない  
-d n: 深さ制限 n を指定する. -d 1 なら直下の1階層のみ  

## 1行ずつパイプ: xargs
findした結果のファイルを`ls`して表示する

```bash
find -name "hoge.php" | xargs ls -l
```

## mkdir -p
存在しない親ディレクトリを自動的に作成する

```bash
mkdir -p .github/workflows
```

## 環境変数表示

### 表示

```bash
printenv
```

### 設定

```bash
export PATH="/user/local/bin:$PATH"
```

### bashの場合のプロファイル
- ~/.bash_profile または ~/.bashrc

### ファイルに以下を書き込む

```bash
export HOGE_VARIABLE="value"
```

### プロファイルの読み込み

```bash
source ~/.bash_profile
```


## グループにユーザを追加する
/etc/groupを修正する

```
compiler:x:978:cpanel,user1
```
コロンのあとにカンマ区切りでユーザ名を書く  
`vigr`でも編集できる.  （本来はコレでやる？）  

ログイン中のユーザを修正した場合は、/etc/gshadowも修正する.  
これも`vigr -s`で編集する.  

## chmod

```bash
chmod u+x [フォルダ名またはファイル名]
```
uの部分は、u: user（所有者）、g: group、o: other  
xの部分は、x: execute（実行）、r: read（読み取り）、w: write（書き込み）  
ファイル削除はw権限だが、ファイルが含まれるフォルダに対するwも必要.  

## cp
-r : フォルダコピー  

## rm
-r : ディレクトリ削除、再帰的削除  
-f : 読み取り専用を確認せずに削除  

## scp
-i : 証明書ファイル  
-r : フォルダコピー  
リモート先の指定 :  [ユーザ名]@[ホスト名]:[ディレクトリ]  
ex : bitnami@host.net:/tmp  

## head/tail
- 行数指定 : -n

## find

```bash
find -name "[ファイル名]"
```
でサブディレクトリを含めて検索する

Macでは以下でルートを検索

```bash
find / -name "[ファイル名]"
```

### 日付指定
更新日付が5/18のもの

```bash
find [ディレクトリ名] -type f -newermt 2023-05-18 ! -newermt 2023-05-19
```
→5/18 00:00より新しく、5/19 00:00より新しくない　を表す.  
newermt: 更新日時  
newerct: 作成日時  
newerat: アクセス日時  

### ディレクトリ除外

```bash
find [ディレクトリ名] -type f -not -path "[除外ディレクトリ]/*"
```

## grep
拡張子指定で検索する

```bash
grep -r "[検索キーワード]" --include="*.[拡張子]" [ディレクトリ]
```

## tar
tgzファイルの解凍

```bash
tar zxfv hoge.tgz
```
tgzファイルに圧縮

```bash
tar czfv hoge.tgz [フォルダ名]
```
tgzファイルの中身の表示

```bash
tar ztfv hoge.tgz
```

## rsync
windowsでいうrobocopy.  Macで外部媒体へのコピーしたらめちゃ速かった

```bash
rsync -av --delete /sourceDir/ /destinationDir/
```
ディレクトリの後ろにスラッシュ必須.  
-a: アーカイブモードを有効にし、ファイルのメタデータやパーミッションを維持  
-v: verbose  
--delete: ミラーコピーでコピー先のみのファイルを削除  

sshを使用したリモートサーバへのコピーも可能。  

```bash
rsync -avP -e "ssh -i key.pem" /Volumes/source ec2-user@remotehost:/dest/
```
-z：圧縮（scp の -C と同じ）  
-P：進捗表示＋中断再開を有効化（--progress と --partial の略）  
--whole-file: 差分アルゴリズムを無効化し、ファイルを丸ごと送る（LANや速い回線向け）  
--inplace: 一時ファイルを作らず直接上書き（ディスクI/O減）  

### コピー後のチェック

```bash
rsync -avcn -e "ssh -i key.pem" /Volumes/source ec2-user@remotehost:/dest/
```
-n / --dry-run → 実際にはコピーせず差分だけ表示  
-c → サイズとタイムスタンプだけでなく チェックサム で厳密比較（遅いけど確実）  

## apt / yum
- ソフトがあるか確認する

```bash
sudo apt info ffmpeg
```

```bash
yum search ffmpeg
```
- 例えばffmpegをインストールする

```bash
sudo apt-get -y install ffmpeg
```

```bash
sudo yum install ffmpeg
```

## curl
- curlでダウンロード: -OL

```bash
curl -OL https://~~~/xxx.zip
```
※macにwgetはないのでcurlを使う

- ヘッダのみ表示（状態の確認など）: -I

```bash
curl -I https://google.com
```
- プロキシを使う: --proxy

```bash
curl --proxy [IPアドレス]:[ポート番号] https://google.com
```
- クライアントにつながっている送信元IPアドレスを調べる

```bash
curl -4 ifconfig.me
```

## 日付表示

```bash
date
```

## フォルダの中のファイルをランダムに更新日付を変更する

```bash
ls |sort -R |xargs -I % sh -c 'touch %;sleep 1;
```

## ドメインからIPを確認する

```bash
dig [ドメイン]
```
出力例. 

```
> ; <<>> DiG 9.10.6 <<>> uwatuki.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 53652
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1
;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;uwatuki.com.			IN	A
;; ANSWER SECTION:
uwatuki.com.		4340	IN	A	163.44.185.209
;; Query time: 7 msec
;; SERVER: 172.20.10.1#53(172.20.10.1)
;; WHEN: Mon Dec 12 20:13:39 JST 2022
;; MSG SIZE  rcvd: 56環境変数表示
```

```bash
printenv
```


## 空いているポートを調べる
使っているプログラムもわかる

```bash
lsof -i:[port番号]
```

## arch
Macのロゼッタのコマンド

現在稼働しているアーキテクチャの確認

```bash
arch
```

x86でbashを起動する

```bash
arch -x86_64 bash
```


## nano
下にショートカットが表示されているが  
Macの場合、  
^: Ctrlキー  
M-: メタキーという. Macのデフォルトはescキー  
を表す.  （WindowsだとM-はAltキーらしい）  

escキーはoptionキーに変更することができる.  
ターミナル - プロファイルの中に、メタキーとしてOptionキーを使用というチェックを入れると設定できる.  

Ctrl + X: 終了. 保存 or 未保存  
Ctrl + K: 行カット  
Option + 6: 行コピー  
Ctrl + U: 貼り付け  
Option + U: アンドゥ  

nanoはテキストエディタなのでバイナリや制御文字が入っていると表示されないことがある.  
そのようなときは`vi`や`cat`を使う.  

### vi
コマンドモード:  `:`を入力  
保存して終了: `wq`  
保存しないで終了: `q!`  
検索: `/`でキーワードを入力  


## 乱数

```bash
echo $RANDOM
```
- 四則演算

```bash
echo $((1+1))
```
- 変数の四則演算（剰余）

```bash
echo $(($RANDOM%60))
```

## crontab
### 日、月、曜日の指定

```text
* * * * * command
│││││
││││└─── 曜日 (0 - 6) (0 または 7 が日曜日)
│││└──────── 月 (1 - 12)
││└────────── 日 (1 - 31)
│└───────────── 時間 (0 - 23)
└─────────────── 分 (0 - 59)
```

```text
m h  dom mon dow   command
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').
```


## crontab
### cron再起動

```bash
sudo systemctl restart cron
```

### cronのランダム実行
- ↓60分以内のランダムスリープ. これをcommandのシェルに入れる.

```bash
sleep $(($RANDOM%60))m
```
↑これを`sh`で実行すると、
> arithmetic expression: expecting primary:

が出るので、`./[ファイル名]`で実行する

### cronで結果をsyslogに書き込む

```
0 21 * * * [command]  2>&1 | logger -t [ログタイトル] 
```

### cronの実行ユーザとshell
- cronの実行ユーザはcrontabの登録ユーザ
- shellはデフォルトbashではなくてsh
- /etc/environmentに定義されている

### cronの環境変数読み込み
- ~/.bashrc , .bash_profileに書き込んでいる個別の環境変数がcrontabに読み込めない場合、/etc/environmentに書き込む

```
HOGE_VARIABLE="value"
```
- source ./.bashrc や、#!/bin/bash -l などでいけるという情報が出てくるが、aws ubuntuはうまく行かなかった

## sshで切断されても継続する...tmux
- 切断しても継続するセッション開始

```bash
tmux
```
を実行後にそのセッション内で使用する  

- 切断後にもう一度そのセッションに入る

```bash
tmux a
```

- Ctrl+B →Dで抜ける（"Ctrl+b"でコマンドモード、"d"（detouch）で抜ける）

- 実行中のセッションを表dd示

```bash
tmux ls
```

- セッションを指定して入る場合

```bash
tmux a -t [表示された先頭の番号]
```

- セッションを強制終了

```bash
tmux kill-session -t [セッション番号]
```

## その他のバックグラウンド実行
### &を後ろにつける

```bash
mysql -u root -p your_database < aaa.sql &
```
### nohup

```bash
nohup mysql -u root -p your_database < aaa.sql > mysql_output.log 2>&1 &
```
### screen
tmuxに類似のライブラリにscreenがある

## ssh-keygen

```bash
ssh-keygen -t rsa [ファイル名]
```
-b: ビット数の指定.  デフォルトは3072でデフォルトで十分と言われる. 
ex.

```bash
ssh-keygen -t rsa -b 4096 [ファイル名]
```

コマンドを実行すると、パスフレーズを聞いてくるのでなしにする. （`ssh`のたびに毎回パスフレーズの入力が必要になる）  
実行すると、~/.sshにid_rsaとid_rsa.pubの2ファイルができる.  
id_rsa: 秘密鍵.  sshのときに指定する.  awsだとaws側で作ってくれるが、そのときは拡張子は.pemになる.  
id_rsa.pub: 公開鍵. sakuraなどは利用者側で作るので、この中身をサーバ作成時に貼り付けることになる.  

## sed
### 置換
`PAGE_CHECKSUM=1 TRANSACTIONAL=0`を置換する

```bash
sed 's/PAGE_CHECKSUM=1 TRANSACTIONAL=0//g' prev.sql > post.sql
```
### バイナリを含む場合
先頭に`LC_ALL=C`をつける

```bash
LC_ALL=C 's/PAGE_CHECKSUM=1 TRANSACTIONAL=0//g' prev.sql > post.sql
```
### 特定の行を削除する
14891行目を削除する

```bash
sed '14891d' hoge.sql > hoge_fixed.sql
```
### 特定の行を修正する
14891行目の`PAGE_CHECKSUM=1 TRANSACTIONAL=0`を置換する

```bash
sed '14891s/PAGE_CHECKSUM=1 TRANSACTIONAL=0//' hoge.sql > hoge_fixed.sql
```

## jq
jsonファイルを扱うライブラリ

### インストール

```bash
# macOS
brew install jq

# Ubuntu
sudo apt-get install jq

# CentOS
sudo yum install jq
```

### ファイルの中身を表示

```bash
cat hoge.json | jq
```
#### オプション

```bash
# 整形せずに出力（-c）
jq -c '.' data.json

# 色付けなしで出力（-M）
jq -M '.' data.json

# raw文字列として出力（-r）
jq -r '.name' data.json
```

### 基本的な使い方

```bash
# 特定のキーの値を取得
cat data.json | jq '.name'

# 配列の要素数を数える
cat data.json | jq '.items | length'

# 配列の特定の要素を取得
cat data.json | jq '.items[0]'
```
#### 配列の場合

```bash
# 配列の各要素のiconを取得
cat data.json | jq '.[].icon'

# または
cat data.json | jq '.[] | .icon'
```

### フィルタリング

```bash
# 条件に合う要素を抽出
cat data.json | jq '.items[] | select(.price > 1000)'

# 特定のキーだけを持つオブジェクトを作成
cat data.json | jq '.items[] | {name: .name, price: .price}'
```

### 加工

```bash
# 配列の要素を変換
cat data.json | jq '.items[] | .price * 1.1'

# 配列をマップ
cat data.json | jq '.items | map(.name)'
```
