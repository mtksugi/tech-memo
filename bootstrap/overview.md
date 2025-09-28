---
title: Udemy Bootstrap 4 メモ
---


# Udemy Bootstrap4 

★bootstrap5でやるならヘルプを確認の上、設定すること！結構違うかも...

## ブレークポイント

| ブレイクポイント | 576px | 768px | 992px | 1200px |
|:---|:---|:---|:---|:---|
| 参考デバイス | スマホ | タブレット | PC | 大型ディスプレイ |
| クラス接頭辞 | col-sm | col-md | col-lg | col-xl |

class="col-md-N" とすると768px以上でカラム分割、768px以下で分割せず縦に表示になる

bootstrap5からは xxl として1400px以上が追加

## 色

- 文字色
```
class="text-primary"
class="text-secondary"
class="text-success"
```

- 背景色
```
class="bg-primary"
class="bg-secondary"
class="bg-success"
```


## スペーシング

[property][sides]-[size]

- property: m:マージン（外余白） p:パディング（内余白）
- sides: 辺 t:top b:bottom l:left r:right x:left&right y:top&bottom 
- size: 幅 rem（ルート要素の何倍か）指定 0:0rem 1:0.25rem 2:0.5rem 3:1rem 4:1.5rem 5:3rem

ex.
- class="ml-0"    ←左に外余白 
- c-lass"p-2" ← sideなしも可. 4辺に内余白


## タイポグラフィ

- divにh1を指定してh1と同じ書式
ex.
```css
<div class="h1">
```
- display-1でh1よりさらに大きい文字
```
<div class="display-1">
```css
- ulタグで行頭なしにする
```
<ul class="list-unstyled">
    <li>
    <li>
</ul>
```

- ulタグで要素を横並びにする
```css
<ul class="list-inline">
    <li class="list-inline-item">
    ...
```
## 画像

- 画像をレスポンシブな表示にする
```css
<img src="..." class="img-fluid">
```

- 画像をサムネイル形式にする（画像の周りにボーダーを表示する）
```css
<img src="..." class="img-fluid img-thumbnail">
```
## テーブル

- 行を縞模様
```css
<table class="table table-striped">
```

- マウスホバーで色を変える
```css
<table class="table table-hover">
```

- サイズを小さくする（サイズの指定）
```css
<table class="table table-sm">  ...スマホ用？余白が少しカットされる
```

## ボーダーの色と角

- 色
```css
<img src="" class="border border-primary">
```
- 角を丸くする
```css
<img src="" class="rounded">    ...-sizeで角の丸の大きさを指定可能（ver5から）
```

- 画像を丸く
```css
<img src="" class="rounded-circle"> ...円形
<img src="" class="rounded-pill">   ...カプセル形
```

## 影をつける

```css
<div class="shadow">
```

## ディスプレイプロパティ

d-[breakpoint]-[value]

- value...cssのdisplay:xxに相当

none / inline / inline-block / block / table / table-cell / table-row / flex / inline-flex

- breakpointを指定することで特定の画面幅のときの表示を設定


## サイジング

- 親要素の幅の何%にするかを指定する
w-nn

```css
<div class="w-25">...親要素の25%の幅にする
<div class="w-100">
<div class="w-auto">
```

- 親要素の高さの何%にするかを指定する
h-nn
```css
<div class="h-25">...wと同じ
```

- 画像のmax-width:100%, max-height:100%

```css
<img src="..." class="mw-100">
<img src="..." class="mh-100">
```

## 以下からコンポーネント

## アラート

- 何に使う？説明書きとか？
ex. 
```css
<div class="alert alert-primary" role="alert">
  A simple primary alert—check it out!
</div>
```

## バッジ

- テキスト横に吹き出しっぽく目立たせるボックスを表示
```css
<h1>Example heading <span class="badge bg-secondary">New</span></h1>
```

- カタチを丸く
```css
<span class="badge rounded-pill bg-primary">Primary</span>
```

## ボタン

- 通常
```css
<button type="button" class="btn btn-primary">Primary</button>
```

- 白抜き
```css
<button type="button" class="btn btn-outline-primary">Primary</button>
```

- 全幅ボタンは display と gap を組み合わせる
```css
<div class="d-grid gap-2">
  <button class="btn btn-primary" type="button">Button</button>
  <button class="btn btn-primary" type="button">Button</button>
</div>
```

- aタグ、inputタグにも使える
```css
<a class="btn btn-primary" href="#" role="button">Link</a>
<input class="btn btn-primary" type="button" value="Input">
```

## カード

- 商品明細の表示に使用するようなコンポーネント. 画像との組み合わせも可能
https://getbootstrap.jp/docs/5.0/components/card/

## カルーセル
https://getbootstrap.jp/docs/5.0/components/carousel/


## ドロップダウン

- ドロップダウンリストはdivとulの組み合わせで作るらしい
https://getbootstrap.jp/docs/5.0/components/dropdowns/

## フォーム

- form-control / form-label を使って生成するが、djangoで使うにはどうする？
https://getbootstrap.jp/docs/5.0/forms/overview/


## ナビゲーションバー

- ナブバーを使うと、PCのときは横並び、スマホではハンバーガーメニューの縦並びというナビゲーションバーを作ることが可能
https://getbootstrap.jp/docs/5.0/components/navbar/

- ナビゲーションと組み合わせて使用する

