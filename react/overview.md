---
title: React メモ
---

# React Course Hello React

このメモは[Udemy講座](https://www.udemy.com/share/101XgI/)を受講して学んだ記録です。  
作者には感謝します。


## live-server

### install 
コースではyarnを使っていたが、なぜか動かないのでnpmで使う

```bash
npm i -g live-server
```
### run

引数にトップディレクトリを指定する

```bash
live-server public
```

たぶん、パッケージローカルにインストールしても動くが、その場合は、

```bash
.node_modules/.bin/live-server
```
で実行すると思われる.  

とりあえず今回は、globalに入れた

## babel

javascriptコンパイラ.
JSXをreactの実行ファイルに変換する. 

### install

ローカルにインストールした

```bash
npm install @babel/core @babel/cli
npm install @babel/preset-env @babel/preset-react
```

### run（コンパイル）

srcフォルダにapp.jsを入れて、publicの方にコンパイル後jsを保存する

```bash
node_modules/.bin/babel src/app.js --out-file=public/scripts/app.js --presets=@babel/env,@babel/react --watch
```

ローカルにインストールしたので`node_modules/.bin`をつけて実行.  

コースでは`--presets=env,react`と言っているが、最新は、上が正解.  

`--watch`で変更の都度、コンパイルする

## webpack

スクラッチで書いたjsやnode_moduleライブラリをバンドルして公開フォルダに置く.  

### install
webpack-cliも必要

```bash
npm install webpack webpack-cli
```

### command

```bash
webpack
# watchも書ける
webpack --watch
```

package.jsonには、scriptの中に`"build": "webpack --watch"`と書く


### config file
webpack.config.jsをルートに保存する

```javascript
const path = require('path');

module.exports = {
  entry: './src/app.js',
  output: {
    path: path.join(__dirname, 'public'),
    filename: 'bundle.js',
  },
  mode: 'development',  // コースではver3で不要だった.  最新は5で、`mode`が必須とのこと
};
```

### babel-loader

このままではJSXが通らないのでbabelをカマす必要がある.  
[babel-loader](https://webpack.js.org/loaders/babel-loader/)をインストールしてconfigに追加する.  

```javascript
const path = require('path');

module.exports = {
  entry: './src/app.js',
  output: {
    path: path.join(__dirname, 'public'),
    filename: 'bundle.js',
  },
  mode: 'development',
  module: {
    rules: [{
      test: /\.js$/,
      exclude: /node_modules/,
      use: {
        loader: 'babel-loader', //ココが必要
        options: {
          presets: [
            '@babel/preset-env','@babel/preset-react'
          ],
          targets: "defaults"
        }
      }
    }]
  }
};
```

`--watch`で起動しているときにconfigファイルを変更したら、webpackを起動し直す

### source map

コースではconfigファイルに
`devtool: 'cheap-module-source-map'`
を追加することで、エラーが発生したときの元ファイルを追えない（実行はbundle.jsで行っているからブラウザが適切か箇所を示せない）事象を回避、とあったが、現バージョンでは、`mode: 'development'`により、devtoolがなくてもコンソールにソースマップで表示される.  
必ずしも必要ではなさそう.  

### dev-server

live-serverの代わりにwebpack-dev-severというものがある（webpack-dev-serverをインストール）.  

configファイルに以下を追加

```javascript
  devServer: {
    static: {
      directory: path.join(__dirname, 'public'),
    },
    compress: true,
    port: 8080,
  },
```

起動は`webpack-dev-server`なのでpackage.jsonに`"dev-server": "webpack-dev-server"`を追加し、`npm run dev-server`で起動する.  

dev-serverはバンドルしたjsを物理ファイルで保持しない.  configファイルに書いた`public`にバンドルファイルを保存せず、メモリで持っている.  
公開する場合は`webpack`でビルドする.  

#### proxy

webpack-dev-serverにはproxyがあり、フロントエンドとバックエンドでURLが異なる場合に同一にできる

```
ブラウザ → localhost:3000 (React Dev Server)
                ↓ 自動転送
           localhost:5000 (Flask API)
```

react-scritpsでは、package.jsonに、proxyを追加する

```json
  "name": "summaria_ui",
  "version": "0.1.0",
  "private": true,
  "proxy": "http://localhost:5000",  // ←追加
  "homepage": "/summaria",
  "dependencies": 
...
```

この場合、`npm start`するときに、環境変数を追加して、`DANGEROUSLY_DISABLE_HOST_CHECK=true npm start`とする必要があるかも。

## JSX

これはNG.  

```javascript
const template = (
  <h1>Indecision App</h1>
  <p>This is some infomation</p>
)
```

一つのタグに入れる必要がある.  

```javascript
const template = (
  <div>
    <h1>Indecision App</h1>
    <p>This is some infomation</p>
  </div>
)
```

空タグでよい.

```javascript
const template = (
  <>
    <h1>Indecision App</h1>
    <p>This is some infomation</p>
  </>
)
```

#### 空タグにmapで繰り返しのkeyを入れる場合はFragmentを使う

```javascript
import React, { Fragment } from 'react';

const textList = [
  {
    no: 1, date: '2023-11-25', content: '初版リリース', 
  },
]

const HistoryBoard = () => {

  return (
    <Grid container spacing={1}>
    {textList.map((item) => (
      <Fragment key={item.no}>   {/* <- ココ */}
        <Grid item xs={12} md={2}>
          <Typography variant='body1' color='textSecondary'>
            {item.date}
          </Typography>
        </Grid>
// 以下省略
```


### 変数を入れる

```javascript
//const app = {... 省略} 
const template = (
<div>
<h1>{app.title}</h1>
<p>{app.subtitle}</p>
</div>
)
```

### 変数は入れ子にでき、undefinedならレンダリングされない

```javascript
const template = (
<div>
<h1>{app.title}</h1>
{app.subtitle && <p>{app.subtitle}</p>}     //...←{}の中に{}を書ける  また、この場合、subtitleがなければ<p>タグ自体レンダリングされない
<p>{app.options && app.options.length > 0 ? "Here are your options": "No options"}</p>
</div>
)
```

### イテレート（繰り返し）

オブジェクトは{}にかけないが、`{<p>hoge</p>}`は書けるので、`Array.map()`と組み合わせて以下のように書ける

```javascript
    <ol>
      {
        app.options.map((option) => <li key={option}>{option}</li>)     {/* ... **繰り返しのときには必ず`key`が必要** 値と同じものを入れておけばよい */}
      }
    </ol>
```

### イベント

↓の`onSubmit`は、Reactがサポートするイベント
[ドキュメント](https://ja.legacy.reactjs.org/docs/events.html)

```javascript
    <form onSubmit={onFormSubmit}>
      <input type="text" name="option" />
      <button>Add Option</button>
    </form>
```

### Dom（タグ）のShow/Hideの標準

onClick部分

```javascript
let displayHello = true
const toggleHello = () => {
  displayHello = !displayHello
  renderForm()
}
```

render部分

```javascript
<button onClick={toggleHello}>{displayHello ? 'Hide Hello' : 'Show Hello'}</button>
{displayHello && (      {/* ...要は、{}でboolean変数とタグまるごと囲う */}
    <p>Hello React</p>
)}
</div>
```


## ReactDOM

`ReactDOM.render`はver18から書き方が変わり使えないようだが、とりあえず今回はこのまま.  

## React Component

React.componentを継承したクラスを作成して、JSXの中に書く.  
class名は先頭文字を大文字にするルール.  

```javascript
class IndecisionApp extends React.Component {
  render() {
    return <div>
      <Header />        //... renderの中にコンポーネントをネストできる
      <Action />
      <Options />
      <AddOption />
    </div>
  }
}

class Header extends React.Component {
  render() {
    return <div>
      <h1>Indecision</h1>
      <h2>Put your life in the hands of a computer</h2>
    </div>
  }
}

ReactDOM.render(<IndecisionApp />, document.getElementById('app'))
```

### props : 親コンポーネントから子コンポーネントへ値を渡す

渡す方: key, value方式で渡す. 
受け取り側: `this.props`で受け取る. 

```javascript
class IndecisionApp extends React.Component {
  render() {
    const title = 'Indecision'
    const subtitle = 'Put your life in the hands of a computer'
    const options = ['thing one', 'thing two', 'thing tree']

    return <div>
      <Header title={title} subtitle={subtitle} />      {/* ...渡し方 */}
      <Action />
      <Options options={options} />
      <AddOption />
    </div>
  }
}

class Header extends React.Component {
  render() {
    return <div>
      <h1>{this.props.title}</h1>       {/* ... 受け取り側 */}
      <h2>{this.props.subtitle}</h2>
    </div>
  }
}
```

#### props.children

`props`ではchildrenを使って、以下のように書ける

```javascript
const Layout = (props) => {
  return (
    <div>
      <p>header</p>
      {props.children}
      <p>footer</p>
    </div>
  )
}

ReactDOM.render((
  <Layout>
    <div>
      <h1>Page Title</h1>
      <p>This is my site.</p>
    </div>
  </Layout>
)
, document.getElementById('app'))
```

### constructor : React.Componentの中のfunctionはconstructorで定義する

propsでファンクションの受け取りの例も示す

```javascript
class Counter extends React.Component {
  constructor(props) {
    super(props)
    this.handlePick = this.handlePick.bind(this)  {/* ←ココ */}
  }
  handlePick() {
    const randomNum = Math.floor(Math.random() * this.state.options.length)
    const option = this.state.options[randomNum]
    alert(option)
  }
  render() {
    const title = 'Indecision'
    const subtitle = 'Put your life in the hands of a computer'

    return <div>
      <Action
        handlePick={this.handlePick}  {/* ファンクションもpropsで渡す */}
      />
    </div>
  }
}

class Action extends React.Component {
  render() {
    return (
      <div>
        <button
          onClick={this.props.handlePick} {/* propsでファンクションを受け取る */}
        >
          What should I do?
        </button>
      </div>
    )
  }
}
```

#### Class Property

ただし、ES6では以下のように書き換え可能. 

`constructor`に書いてきた↓のコードは、、

```javascript
class IndecisionApp extends React.Component {
  constructor(props) {
    super(props)
    this.handleDeleteOptions = this.handleDeleteOptions.bind(this)
    this.handleDeleteOption = this.handleDeleteOption.bind(this)
    this.handlePick = this.handlePick.bind(this)
    this.handleAddOption = this.handleAddOption.bind(this)
    this.state = {
      options: []
    }
  }
  handleDeleteOptions() {
    //...
  }
  handleDeleteOption() {
    //...
  }
```
ES6のclass propertyを使って、以下のように書き換え

```javascript
class IndecisionApp extends React.Component {
  state = {
    options: []
  }
  handleDeleteOptions = () => {   {/* ←アロー関数 */}
    //...
  }
  handleDeleteOption = () =>{
    //...
  }
```
`constructor`を削除して、class propertyに.
関数はアロー関数に書き換える.  



### State : 状態管理

```javascript
class Counter extends React.Component {
  constructor(props) {
    super(props)
    this.handleAddOne = this.handleAddOne.bind(this)
    this.handleMinusOne = this.handleMinusOne.bind(this)
    this.handleReset = this.handleReset.bind(this)
    this.state = {  {/* constructorに変数を書く */}
      count: 0
    }
  }
  handleAddOne() {
    this.setState((prevState) => {
      return {    {/* returnは将来要らなくなる？（この講義中のバージョンでは必要） */}
        count: prevState.count + 1
      }
    })
  }
  handleMinusOne() {
    this.setState((prevState) => {  {/* 引数にstateの現在の状態を受け取れる */}
      return {
        count: prevState.count - 1  {/* prevStateからマイナス1する */}
      }
    })
  }
  handleReset() {
    this.setState(() => {
      return {
        count: 0
      }
    })
  }
  render() {
    return (
      <div>
        <h1>Count: {this.state.count}</h1>
        <button onClick={this.handleAddOne}>+1</button>
        <button onClick={this.handleMinusOne}>-1</button>
        <button onClick={this.handleReset}>reset</button>
      </div>
    )
  }
}
```

### Stateless Functional Components

classにしなくてもfunctionで足りるものはfunctionでrender()した方がコストが低い

```javascript
class IndecisionApp extends React.Component {
  render() {
    const title = 'Indecision'
    const subtitle = 'Put your life in the hands of a computer'
    const options = ['thing one', 'thing two', 'thing tree']

    return <div>
      <Header title={title} subtitle={subtitle} />
//（...省略）
    </div>
  }
}

class Header extends React.Component {
  render() {
    return <div>
      <h1>{this.props.title}</h1>       {/* ... 受け取り側 */}
      <h2>{this.props.subtitle}</h2>
    </div>
  }
}
```
Headerは以下のように書き換える

```javascript
class IndecisionApp extends React.Component {
  render() {
    const title = 'Indecision'
    const subtitle = 'Put your life in the hands of a computer'
    const options = ['thing one', 'thing two', 'thing tree']

    return <div>
      <Header title={title} subtitle={subtitle} />
//（...省略）
    </div>
  }
}

const Header = (props) => { {/* functionにしてpropsを引数にする */}
  return (
    <div>
      <h1>{props.title}</h1>
      <h2>{props.subtitle}</h2>
    </div>
  )
}
```

### Default Props

引数のデフォルトの書き方

```javascript
const Header = (props) => {
  return (
    <div>
      <h1>{props.title}</h1>    {/* propsにtitleがなかったら↓のdefaultPropsが有効になる */}
      {props.subtitle && <h2>{props.subtitle}</h2>}
    </div>
  )
}

Header.defaultProps = {
  title: "Indecision"
}
```

### React Componentsのライフサイクル

Reactにもライフサイクルがある
https://ja.legacy.reactjs.org/docs/react-component.html

```javascript
class IndecisionApp extends React.Component {
  constructor(props) {
    super(props)
    this.handleDeleteOptions = this.handleDeleteOptions.bind(this)
    this.handleDeleteOption = this.handleDeleteOption.bind(this)
    this.handlePick = this.handlePick.bind(this)
    this.handleAddOption = this.handleAddOption.bind(this)
    this.state = {
      options: props.options
    }
  }
  componentDidMount() {   {/* マウント後 */}
    console.log('component did mount');
  }
  componentDidUpdate() {  {/* データ更新後 */}
    console.log('component did update');
  }
  componentWillUnmount() {  {/* 画面終了時 */}
    console.log('component will unmount');
  }
```

### ライフサイクルのDidMountとDidUpdateにデータのロードと書き出しを書く

localStorageを使う

```javascript
//（省略　上と同じ）

  componentDidMount() {
    try{
      const jsonStr = localStorage.getItem('options')
      const options = JSON.parse(jsonStr)    {/* json文字列をlocalStorageから取り出して設定 */}
      if (options) {
        this.setState(() => ({ options: options }))
      }
    } catch (e) {
      // Do nothing at all
    }
  }
  componentDidUpdate(prevProps, prevState) {
    if (prevState.options.length !== this.state.options.length) {
      const jsonStr = JSON.stringify(this.state.options)
      localStorage.setItem('options', jsonStr)   {/* json文字列でlocalStorageに保存 */}
    }
  }
```

## SCSS

このアプリではこのようなツリー構造のファイルを作成した。

styles
├── base
│   ├── _base.scss
│   └── _settings.scss
├── components
│   ├── _add-option.scss
│   ├── _button.scss
│   ├── _container.scss
│   ├── _header.scss
│   ├── _modal.scss
│   ├── _option.scss
│   └── _widget.scss
└── styles.scss

styles.scssはその下のファイルをimportするためのファイル。

```scss
@import './base/settings';
@import './base/base';
@import './components/header';
@import './components/container';
@import './components/button';
@import './components/widget';
@import './components/option';
@import './components/add-option';
@import './components/modal';
```

そしてstyles.scssをapp.jsが参照する。

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

import IndecisionApp from './components/indecisionApp';
import 'normalize.css/normalize.css'
import './styles/styles.scss'   //★ココ

ReactDOM.render(<IndecisionApp/>, document.getElementById('app'))
```

参照下のファイルがアンダースコア始まりになっているのは慣習らしい。
アンダースコア始まりでも、参照は、`@import './components/modal';`でいいらしい。

ファイルを分けることでどこから参照されているcssなのか一目でわかる。

### scssでの変数の使用

- 変数を定義して

```scss
$off-black: #20222b;
// 省略
```
値として使える

```scss
.header {
  background: $off-black;
  color: white;
  margin-bottom: $m-size;
  padding: $m-size 0;
}
```

### cssでブレイクポイントの使い方

`@media (min-width: xxx)`でxxx以上のサイズのときに適用するスタイルを設定する

```scss
.big-button {
  background: $purple;
  border: none;
  border-bottom: .6rem solid darken($color: $purple, $amount: 10%);
  color:white;
  font-weight: bold;
  font-size: $l-size;
  margin-bottom: $m-size;
  padding: $s-size;
  width: 100%;
}

.big-button:disabled {
  opacity: .5;
}

@media (min-width: $desktop-breakpoint) {
  .big-button {
    margin-bottom: $xl-size;
    padding: 2.4rem;
  }
}
```


## React Router Dom

ライブラリをインストール

```bash
npm install react-router-dom
```

### BrowserRouter, Route, RoutesとLink, NavLink

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter, Route, Routes, Link, NavLink} from 'react-router-dom';
import 'normalize.css/normalize.css'
import './styles/styles.scss'

const ExpenseDashboardPage = () => (/* 省略 */)

const AddExpensePage = () => (/* 省略 */)

const EditExpensePage = () => (/* 省略 */)

const HelpPage = () => (/* 省略 */)

const NotFoundPage = () => (
  <div>
    404 - <Link to="/">Go Home</Link>   {/* ...Linkを使うことでクライアント側で遷移 */}
  </div>
)

const Header = () => (
  <header>
    <h1>Expensify</h1>
    <NavLink to="/">Dashboard</NavLink>  {/* ...LinkとNavLinkの違いは、NavLinkの方がパラメータが充実している.  スタイルに`active`が自動で使えたり。 */}
    <NavLink to="/create">Create</NavLink>
    <NavLink to="/edit">Edit</NavLink>
    <NavLink to="/help">Help</NavLink>
  </header>
)

const routes = (
  <BrowserRouter>   {/* ...BrowserRouterとRoutesで囲む */}
    <Header />
    <Routes>
      <Route path='/' Component={ExpenseDashboardPage} />   {/* ...これでクライアント側でページ遷移する（サーバにリクエストが飛ばない） */}
      <Route path='/create' Component={AddExpensePage} />
      <Route path='/edit' Component={EditExpensePage} />
      <Route path='/help' Component={HelpPage} />
      <Route path='*' Component={NotFoundPage} />
    </Routes>
  </BrowserRouter>
)

ReactDOM.render(routes, document.getElementById('app'))
```

```javascript
  devServer: {
    static: {
      directory: path.join(__dirname, 'public'),
    },
    historyApiFallback: true,   {/* ... react-router-domを使うときは必ず必要 */}
    compress: true,
    port: 8080,
  },
```

```css
header a.active {
  font-weight: bold;
}
```
