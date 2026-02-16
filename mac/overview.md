---
title: Mac メモ
lang: ja
---



# Macのいろいろ

## ソフトウェアのインストール

### インターネットからダウンロードしたファイルがセキュリティではじかれてインストールできない場合

```bash
sudo spctl --master-disable
```
を実行すると、`システム環境設定 - セキュリティとプライバシー - すべてのアプリケーションを許可`が表示され、インストールできるようになる

### 〜が悪質なソフトウェアかどうかをAppleでは確認できないため、このソフトウェアは開けません。

`システム環境設定 -> プライバシーとセキュリティ`の下の方に、ブロックされたアプリケーションの警告があり”そのまま開く”をクリックすると使えるようになる。


## dmgファイルのマウントをコマンドで

```bash
hdiutil mount [dmgファイル名]
```

## python

- デフォルトは `/usr/bin/python3`の模様。Sonoma14では、3.9がインストールされている。

## Macのスタートアップ
/Library/LaunchDaemons
ココにXML形式で書くらしい

## ファインダーでtmpを開く

command + shift + Gでパスを指定して開く

## ファインダーで.（ドット）始まりのファイルを表示する

command + shift +  .（ドット）で表示・非表示が切り替わる

## ストレージ容量をCLIで確認する

```bash
diskutil apfs list
```

## 記号・絵文字など: 文字ビューアを表示

ctrl + command + space

## スピーカープロセスの再起動

音がでなくなったときに

```
sudo killall coreaudiod
```
アクティベティモニタで`coreaudiod`プロセスを強制終了でもよい（終了後自動起動する）

## 壁紙フォルダが巨大すぎる

壁紙設定を、空撮をシャッフルなどとしていると以下フォルダにmovファイルがたまりまくって20Gを超えるので定期的に削除する

`~/Library/Application\ Support/com.apple.wallpaper`

## Mac用コマンド

### スリープの一時停止

MacBook を閉じてもスリープしないようにする

```bash
caffeinate -i
```
ディスプレイだけスリープしない

```bash
caffeinate -d
```
1時間だけスリープしない

```bash
caffeinate -t 3600
```

### サイズの大きいフォルダを探す

```bash
sudo du -x -h -d 5 / | sort -h | tail -n 30
```

### タイムマシーンスナップショットの表示

```bash
tmutil listlocalsnapshots /
```


## Apple Silliconへの切り替え

Intel MacからM3 Macに移行したとき、pythonライブラリのtensorflowが動かなくなった。  
arm版でcursor（vs code）を起動して、そこで起動していたため。  
元はといえば、このときに、archコマンドで、x86で起動しておけばなにも問題なかった。  
しかし`arch`コマンドを知らなかったので、tensorflowを動かすにはbrewでライブラリを再インストールしなければならなくなり、
x86のbrewをアンインストールしてしまった。  
で、arm版のbrewをインストールしtensorflowは動いたのだが、今度はx86のbrewライブラリを参照している既存アプリのflaskが起動しなくなった。  
原因は、x86のbrewでインストールした`mysql`と`pkg-config`がなくなってしまったため。  
pythonライブラリのmysqlclientを再インストールしても、ライブラリの参照がうまくいかず利用できない。  
なので、再度x86のbrewをインストールし、`mysql`と`pkg-config`をインストールして、mysqlclientを再インストールしたところ治った。  

### archコマンド

`arch`で今のターミナルの起動アーキテクチャがわかる。`arm` or `i386`と出力される  
デフォルトはarm版だが、

```bash
arch -x86_64 bash
```
とすることで、x86版のbashが起動する。bashの部分はコマンドやアプリでよい

### brew

x86版は /usr/local/bin, /usr/local/Cellarを使う。
`brew`自体は

```
/usr/local/bin/brew -> ../Homebrew/bin/brew
```

arm版は、/opt/homebrew/bin, /opt/homebrew/Cellarを使う。
`brew`コマンドは、環境変数のPATHの先頭に、/usr/local/binを入れるか、/opt/homebrew/binを入れるかで異なる。

x86版のbrewを使うには、`arch -x86_64`で切り替えて、PATHを変更して使う必要がある。

### mysqlclient

pythonライブラリの`mysqlclient`を使うには、brewで`mysql`と`pkg-config`をインストールする必要がある。公式ドキュメントにもそのように記載されている。  
インストール時は、環境変数のDYLD_LIBRARY_PATHにも、先頭に`/usr/local/mysql/lib`を入れる必要があるかもしれない




