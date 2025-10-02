---
title: Web API メモ
lang: ja
---


# Web API

## location

webページのURLにアクセスできる.  
`location.protcol`でhttp、`location.search`でクエリストリングなど.  

### リダイレクト
`location.href = '/'`でルートに戻る

## クライアントのクリップボード

`navigator.clipboard`

## クライアントの位置情報（GPS）

`navigator.geolocation`

現在地（緯度、経度）の取得

```javascript
navigator.geolocation.getCurrentPosition((position) => {
  console.log(position.coords.latitude, position.coords.longitude);
})
```

## 選択文字列: window.getSelection()

`window.getSelection().toString()`: 選択した文字列  
`window.getSelection().getRangeAt(0)`で`range`オブジェクトを返し、`range.startOffset`/`range.endOffset`で選択したノードにおける位置、`range.startContainer`で選択開始位置の`node`オブジェクトを返す  

## クエリパラメータを扱う: URLSearchParams

```javascript
// URLのクエリストリング例: ?name=John&age=30

const queryParams = new URLSearchParams(window.location.search);

// クエリパラメータの取得
const name = queryParams.get('name'); // "John"
const age = queryParams.get('age');   // "30"

// クエリパラメータの設定
queryParams.set('city', 'New York'); // 新しいクエリパラメータを追加
queryParams.set('age', '31');       // ageの値を更新

// クエリストリングの生成
const newQueryString = queryParams.toString(); // "name=John&age=31&city=New+York"
```

## localStorage

`window.localStorage`
保存

```javascript
localStorage.setItem('name','paul')
```
取り出し

```javascript
localStorage.getItem('name')
```
数値は保存できない。文字列のみ

```javascript
localStorage.setItem('age',48)
localStorage.getItem('age') # '48'が返る
```
なのでJSON文字列で保存する

```javascript
jsonStr = JSON.stringify({age: 48})
localStorage.setItem('data', json)
jsonobj = JSON.parse(localStorage.getItem('data'))
```

