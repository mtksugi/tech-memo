---
title: JavaScript メモ
lang: ja
---



# javascript Tips

##  標準入力
- process.argv

```javascript
const  command = process.argv[2]
```
argv[0] : javascriptのランタイム？ ex. nodeの場合、'/usr/local/Cellar/node@14/14.18.3/bin/node'
argv[1] : 実行されたファイル（フルパス）

## 配列操作
### 配列の中の合計
- reduce()を使う

```javascript
let x = [1, 2, 3]
let e = x.reduce((acc, cur) => acc + cur)
```
オブジェクトの場合は初期値を設定する

```javascript
let x = [{count: 1, name: 'one'}, {count: 2, name: 'two'}, {count: 3, name: 'three'}]
let e = x.reduce((acc, cur) => acc + cur.count, 0)
```

### 配列の中の最大/最小
- reduse()を使う.  ↓最小はMath.min()

```javascript
let x = [1, 2, 3]
let e = x.reduce((pre, cur) => Math.max(pre,cur), 0)
```

### ソート
- sort()

```javascript
let x = [1, 2, 3, 0]
x.sort((a, b) =>  a - b) //昇順
x.sort((a, b) =>  b - a) //降順
// オブジェクトの場合
let x = [{name: 'john', age: 44}, {name: 'paul', age: 40}]
x.sort((a, b) => a.age - b.age)
```

### すべて固定値で埋める
- fill()を使う.

```javascript
let arr = new Array(5)
arr.fill(true)
```

### すべての要素の判定
- every()を使う

```javascript
let arr = new Array(5)
arr.fill(true)
arr.every((x) => x)
```

### 配列の作成
- Array.from()
↓0〜9の配列を作る

```javascript
Array.from({ length: 10 }, (_, index) => index);
```
Array.fromは第一引数に配列オブジェクトを受け取るのだが、`{length: 10}`とすることで擬似的に10個の長さのオブジェクトを表し、それを繰り返すことで配列が作成される

### 配列のディープコピー
- 配列やオブジェクトのスプレッド構文（`const newArray = [...originalArray]`）やslice（`const newArray = originalArray.slice()`）によるコピーはシャローコピーと言って、配列の中がオブジェクトだとそのままの参照になる。中身も含めてコピーするのはディープコピーと言う。
- 中身がファンクションなどでなければ、JSON.parseとstringifyをかけるのが簡易的なディープコピー

```javascript
const deepCopy = JSON.parse(JSON.stringify(originalArray));
```
厳密にやるならloadashのライブラリを使うのがよいらしい

## 文字列操作
- 英数字の全角半角変換（半角→全角）

```javascript
String.fromCharCode('1'.charCodeAt(0) + 0xFEE0)
```
- 英数字の全角半角変換（全角→半角）

```javascript
String.fromCharCode('1'.charCodeAt(0) - 0xFEE0)
```
## 算術演算
- 四捨五入
小数点以下第2位を四捨五入

```javascript
const i = 1.555
i.toFixed(2)
// -> "1.56"
```
文字列になる。

- 切り捨て

```javascript
const result = 5.9;
const truncatedResult = Math.floor(result);
console.log(truncatedResult); // 出力: 5
```

## オブジェクト操作
### プロパティの削除: `delete`演算子

```javascript
let obj = {name: 'john', age = 48}
delete obj.age
```
ただし、参照オブジェクトでは元のオブジェクトに影響を与える場合があるので、下のレストパターンで除くほうが安全。


## 配列リテラルとオブジェクトリテラル

- 配列リテラル

```javascript
let coffees = ['French Roast', 'Colombian', 'Kona'];
```
- オブジェクトリテラル

```javascript
var car = { myCar: 'Saturn', getCar: carTypes('Honda'), special: sales };
```
> オブジェクトリテラルとは、プロパティ名とそれに関連付けられたオブジェクトの値との 0 個以上の組が波括弧 ({}) で囲まれたもので作られたリストです。

## スプレッド構文
> スプレッド構文 (...) を使うと、配列式や文字列などの反復可能オブジェクトを、0 個以上の引数 (関数呼び出しの場合) や要素 (配列リテラルの場合) を期待された場所で展開したり、オブジェクト式を、0 個以上のキーと値の組 (オブジェクトリテラルの場合) を期待された場所で展開したりすることができます。
> スプレッド構文は、オブジェクトや配列のすべての要素を何らかのリストに入れる必要がある場合に使用することができます。

↓これはECMAScript 2018 の新機能

```javascript
let objClone = { ...obj }; // オブジェクトのすべてのキーと値の組を渡す
```

```javascript
products = products.map( p => {
  if (p.id === req.body.id) {
    product = { ...p, ...req.body};
    return product;
  }
  return p;
})
```
↑これの途中のスプレッドは{}でオブジェクトにしていて、```p```に含まれるキーと同じキーが```req.body```にあると```req.body```の値（プロパティ）で書き換えられている

※ES2018より前はリストのスプレッドのみ.  オブジェクトのスプレッドが2018から.  

### 注意
スプレッドはシャローコピー（浅いコピー）である。
第1階層はコピーされるが、値が配列やオブジェクトの場合は、元の値が共有されるので注意が必要。

```javascript
const obj = { name: 'john', age: 40, sex: 'man' }
const copyobj = {...obj}
```
この場合のcopyobjはobjとは独立。

```javascript
const obj = { group: 'beatles', menbar : [{ name : 'john'}, {name: 'paul'}]}
const copyobj = {...obj}
```
この場合のcopyobj.menbarは、objと共有されている


## Object Destruct
ES6？から、オブジェクトを再構成できる（縮小コピーともいうべきか）

```javascript
product = {
  name: 'kokuyo notebook',
  price: 120,
  stock: 2000,
  salePrice: undefined
}
const {name, price, name:renameName, rating = 5, stock = 1800} = product
console.log(name, price)	// -> kokuyo notebook 120
console.log(renameName)		// -> kokuyo notebook これはプロパティのキー名を変更する例
console.log(rating)			// -> 5 存在しないキーはデフォルト値を指定できる. デフォルトを指定しなかったらundefined
console.log(stock)			// -> 2000 存在するキーはデフォルト指定は無視される
```
## レストパターン
スプレッド構文を左辺に使うと、先に宣言したプロパティ以外のオブジェクトを生成できる。

```javascript
const request = {
    jobkind: 'typeA',
    request: 'data',
    editing: true,
    anotherProp: 'value'
};

const { editing, ...rest } = request;

console.log(editing); // true
console.log(rest); // { jobkind: 'typeA', request: 'data', anotherProp: 'value' }
```

## import / export

### import

```javascript
import './util.js'
// .jsはなしても可
import './util'
```
これでファイル全体は読み込めるが、`util.js`の中の関数をimportしたスクリプトの中で使えないので、普通は次のように書く

```javascript
import { add } from './util'
// 関数が複数ある場合は
import { add, square } from './util'
```

### export
`const`の前に`export`を書ける

```javascript
export const square = (x) => x * x;
```
#### named export
次の書き方をnamed exportという

```javascript
const square = (x) => x * x;
export { square }
```
#### default export
ファイルの中で一つはdefault exportにできる

```javascript
const square = (x) => x * x;
export default square
//または export { square as default }
```
または

```javascript
const square = (x) => x * x;
export { square as default }
```
または

```javascript
export default square = (x) => x * x;
```
default exportはimport側では、同じ名前でなくていい.  `{}`もいらない

```javascript
import square2 from './util'
```

## Class / クラス

ES6からのclass propertyの書き方.
ES6以前

```javascript
class OldSyntax {
  constructor() {
    this.name = 'paul'
  }
  getGreeting() {
    return `Hi, my name is ${this.name}`
  }
}

const oldSyntax = new OldSyntax()
console.log(oldSyntax.getGreeting());  // ←これはOK
const getGreeting = oldSyntax.getGreeting
console.log(getGreeting())  // ←これはNG. `this`が効かないため
```
上記はclass propertyにして以下のように書く

```javascript
class NewSyntax {
  name = 'john'
  getGreeting = () => {			// ←アロー関数で書く＝class propertyになる
    return `Hi, my name is ${this.name}`
  }
}

const newSyntax = new NewSyntax()
const getGreeting = newSyntax.getGreeting
console.log(getGreeting())  // ←OK
```


## ファイル操作
fsモジュールを使う

- require

```javascript
const  fs = require("fs").promises;
```
→必ずプロミスを使う

- ファイル非同期読み込み（JSONパース）

```javascript
const  data = JSON.parse(await  fs.readFile(file));
```
- ファイル同期読み込み

```javascript
const  data = fs.readFileSync(file));
```

- ファイル/ディレクトリ群の読み込み

```javascript
const  items = await  fs.readdir(folderName, { withFileTypes:  true});
```

- ディレクトリ作成

```javascript
const  salesTotalsDir = path.join(__dirname, "salesTotals");
await  fs.mkdir(salesTotalsDir);
```
存在しない親パスを含めると例外が発生するのでtry / catchをかけること

- ディレクトリ作成（再帰）

```javascript
await fs.mkdir(path.join(__dirname, "newDir", "stores", "201", "newDir"), {
  recursive: true
});
```

- 空ファイル作成

```javascript
await fs.writeFile(path.join(salesTotalsDir, "totals.txt"), String());
```

- ファイル書き込み（追記）

```javascript
await fs.writeFile(path.join(salesTotalsDir, "totals.txt"),
  `${salseTotal}\r\n`, {flag: "a"});
```
## JSON
- jsonデータを読み込み、更新、書き込み

```javascript
const buf = fs.readFileSync('1-json.json')
const dataString = buf.toString()
const json = JSON.parse(dataString)	// json 文字列を json オブジェクトに変換

json.name = 'Motoki'
json.age = 44
fs.writeFileSync('1-json.json', JSON.stringify(json))	//json オブジェクトを json 文字列に変換
```
stringifyはJSONオブジェクト"{}"を扱うが、リスト"[]"も扱える

```javascript
const jsonStr = JSON.stringify({age: 48})
const jsonStr = JSON.stringify([1,2,3])
```


## httpserver

- 最小のhttpserverインスタンスのコード

```javascript
const http = require('http');
const PORT = 3000;

const server = http.createServer((req, res) => {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('hello world');
});

server.listen(PORT, () => {
  console.log(`listening on port ${PORT}`)
})
```
- ライブラリ express を使用したhttpserver

```javascript
const express = require("express");
const app = express();
// ミドルウェア : http headersの"authorization"をチェックする
function isAuthorized(req, res, next) {
  const auth = req.headers.authorization;
  if (auth === 'secretpassword') {
    next();
  } else {
    res.status(401);
    res.send('Not permitted');
  }
} 

const port = 3000;

app.get("/", (req, res) => res.send("Hello World!"));
// ミドルウェア関数を引数に入れる
app.get("/users", isAuthorized, (req, res) => {
  res.json([
    {
      id: 1,
      name: "User Userson",
    },
  ]);
});

app.get("/products", (req, res) => {
  res.json([
    {
      id: 1,
      name: "The Bluest Eye",
    },
  ]);
});

app.listen(port, () => console.log(`Example app listening on port ${port}!`));
```
- httpを呼び出すクライアント

```javascript
const http = require("http");

http.get(
  {
    port: 3000,
    hostname: "localhost",
    path: "/users",
    headers: { authorization : "secretpassword"},
  },
  (res) => {
    console.log("connected");
    res.on("data", (chunk) => {
      console.log("chunk", "" + chunk);
    });
    res.on("end", () => {
      console.log("No more data");
    });
    res.on("close", () => {
      console.log("Closing connection");
    });
  }
);
```
通常、fetchで済ませる

## CallbackとPromise
以下の処理はCallbackで書いたものをPromiseで書き直したものである.  

- Callbackで書いた場合

```javascript
const doWorkCallback = (callback) => {
  setTimeout(() => {
    // callback('This is my error!', undefined)		//←コチラを有効にすると呼び出し側のerrorが返る
    callback(undefined, [1,4,7])
  }, 2000)
}

doWorkCallback((error, result) => {
  if (error) {
    return console.log(error);
  }
  console.log(result)
})
```
- Promiseで書いた場合

```javascript
const doWorkPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve([7, 4, 1])
    // reject('Things went wrong')		//←コチラを有効にすると呼び出し側のcatchに入る
  }, 2000);
})

doWorkPromise.then((result) => {
  console.log('success', result);
}).catch((error) => {
  console.log('error', error);
})
```
PromiseとはCallbackをより簡便に読みやすくするためのもの.  
例えば上のCallbackの場合、`doWorkCallback`の`setTimeout`の中の`callback`は何度も書くことができるのでバグの温床となる.  
Promiseの場合、`resolve`または`reject`で処理が終了するので、1度しか書くことができない.  
また呼び出し側も`.then`、`.catch`とチェーンで書くことができるし、callbackで書いたときの`if (error)`の判定も不要になる.  

## Promise Chain
Promiseはつなげることができる. →`.then`を何度も書ける

```javascript
const add = (a, b) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(a + b)
    }, 2000)
  })
}
```
↑こんなPromiseがあるとすると...

```javascript
add(1, 2).then((sum) => {
  console.log(sum);

  add(sum, 2).then((sum2) => {
    console.log(sum2);
  }).catch((e) => {
    console.log(e);
  })
  
}).catch((e) => {
  console.log(e);
})
```
↑このように入れ子にしなくても...

```javascript
add(1, 2).then((sum) => {
  console.log(sum);

  return add(sum, 2)
}).then((sum2) => {
  console.log(sum2);
}).catch((e) => {
  console.log(e);
})
```
`then`をつなげて書くことができる.

## async / await
Promise chainをさらに発展させたのが async / await を使う書き方.  

まずは基本形

```javascript
const doWork = async () => { 
}
console.log(doWork());  // return Promise { undefined }
```
↑空の`async`ファンクションは`Promise`を返す.  
`async`ファンクションは必ず`Promise`を返す.  

中身を入れたのがこれ↓

```javascript
const doWork = async () => {
  throw new Error('something wrong')	//catchに返すならコチラ
  return 'John'  
}

doWork().then((result) => {
  console.log('result', result);
}).catch((e) => {
  console.log('e', e);
})
```
↑この書き方では特にPromiseと変わらない.  
awaitを使うと↓こうなる.  

```javascript 
const add = (a, b) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (a < 0 || b < 0) {
        reject('numbers must be non-negative')
      }
      resolve(a + b)
    }, 2000)
  })
}

const doWork = async () => {
  const sum = await add(1, 99)
  const sum2 = await add(sum, 50)
  const sum3 = await add(sum2, 3)
  return sum3
}

doWork().then((result) => {
  console.log('result', result);
}).catch((e) => {
  console.log('e', e);
})
```
`await`は必ず`async`の中に書くが、`await`を使うことによって、`Promise`chainのときの呼び出し側の`then`が一回でよくなる.  
`then`で`Promise` chainで書くと変数のスコープも限られてしまうが、この書き方だとその必要もなくなる.  


