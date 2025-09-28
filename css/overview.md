---
title: CSS メモ
---

# CSS Tips

## 改行をそのまま表示する

- 改行は活かす.  連続するホワイトスペースを詰めて一つにする.
```css
white-space: pre-line;
```
- 改行は活かす.  連続するホワイトスペースはそのまま.
```css
white-space: pre-wrap;
```

## リンクの下線を消す
```css
text-decoration: none;
```

## リストのマークを消す
```css
list-style: none;
```

## 画像の正円
```css
border-radius: 50%;
```

## flexアイテムを下寄せにする
flexアイテムが2つあって、1つ目を一番上に、2つ目を一番下に配置したいとき、`flex-grow`を使って余白を割り当てることで実現できる.  

```html 
<div class="chat__main">
    <div id="messages" class="chat__messages"></div>

    <div class="compose">
    ...
    </div>
</div>
```
```css
.chat__main {
    display: flex;
    flex-direction: column;
}

.chat__messages {
    flex-grow: 1;
}
```
`compose`クラスには`flex-grow`を割り当てないことでchat__messagesのdivにすべての余白が割り当てられる→composeが一番下に適用される

## Twitterで広告でない問題

position: -webkit-fixed;の追加

