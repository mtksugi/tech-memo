---
title: Node.js メモ
---

# node.js

このメモは[Udemy講座](https://www.udemy.com/share/101WGi/)を受講して学んだ記録です。  
作者には感謝します。


## nodeコマンド

```bash
node hoge.js
```
でjsファイルの実行

- inspect
```bash
node inspect hoge.js
```
でコンソールのデバッグ.

なお、`node inspect` を実行し、chromeのアドレスバーに`chrome://inspect`を入れると、Remote Targetの欄にデバッグ中のスクリプトが表示され、接続するとchromeのデバッガが起動する. 通常はvscodeのデバッガを使用していればよいと思われる. 

## nodeの非同期実行の実現方法

> Node.js はスケーラブルなネットワークアプリケーションを構築するために設計された**非同期型**のイベント駆動の JavaScript 環境です。

nodeの特徴はansyncronousを実現していること.  
（javascript自体はシングルスタック）  
javascriptはCall stackを使ってシングルスタックで動く.  
NodeはCallbackが発生すると、Callback queueに溜め込み、メインプログラムが終了後、逐次Callbackを実行する.  

```javascript
console.log('starting.')

setTimeout(() => {
  console.log('2 second passed')
}, 2000)

setTimeout(() => {
  console.log('0 second passed')
}, 0)

console.log('stopped.')
```
上の例では、setTimeout（setTimeoutはjavascriptの関数ではなくnodeのAPI）が発生すると、Callback関数をCallback queueに移し待機させる.  
Callback queueは**メインプログラム終了後に動く**ため、`console.log('stopped')`より前にsetTimeoutの中の`console.log('0 second passed')`が実行されることはない.  
逆に言えばCallback関数を実行させるには、メインプログラムを終了させなければいけない.  

例えば、
```javascript
const geocode = (address) => {
  const data = {
    latitude : 133, longitute: 46
  }
  return data
}

console.log(geocode('kasugai'))
```
この同期プログラムで、`geocode`の中身を非同期で返したい場合、こうなる
```javascript
const geocode = (address, callback) => {	//引数にCallback関数をとり...
  setTimeout(() => {
    const data = {
      latitude : 133, longitute: 46
    }
    callback(data)		//Callbackを実行...
  }, 1000)
}

geocode('kasugai', (data) => {
  console.log(data)		//Callbackの中身を呼び出し側に書く. geocodeで取得したデータの処理など.
})
```
## package.jsonの作り方
```bash
npm init
```
-y で各値をデフォルトで作成
```bash
npm init -y
```

## package.json
- 例
```javascript
{
  "name": "tailwind-trader-api",
  "version": "1.0.0",
  "description": "Tailwind Traders データベースからアイテムを管理する HTTP API",
  "main": "index.js",
  "scripts": {
    "start": "node ./src/index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": ["api", "database"],
  "author": "author",
  "license": "ISC"
}
```
### scripts
scriptsには start, test, build などのアクションを書く  
これらのアクションは、
```bash
npm run <アクション>
```
で実行できる  
startとtestはrunを省略して  
```bash
npm start
```
で実行できる

### dependencies

バージョン更新の書き方のルール  

- x.0.0 または * (アスタリスク)
最も高い "メジャー" バージョンに更新します。
- 1.x.1 または ^ (挿入)  例: ^1.1.1
"マイナー" バージョンのみを更新します。
- 1.1.x または ~ (チルダ) 例:~1.1.1
最新の "パッチ" バージョンに更新します。

### devdependencies
```bash
npm install nodemon --save-dev
```
--save-devでインストールするとdevdependenciesに設定される.  
プロダクションには不要で開発時のみ使うライブラリを指定できる.  

環境構築時、`npm install`することでdevdependenciesに記載されたライブラリを一括インストールできる.  


## 環境変数
- pythonのdjango-environにあたるものに、env-cmdライブラリがある
- こちらはプロダクションはOSの環境変数を使って、開発中にファイルを参照するように使う
- プロジェクト直下に`.env`ファイルを置く
```env
PORT=3000
SENDGRID_API_KEY=xxx
CONNECTION_URL=mongodb://127.0.0.1:27017/task-manager-api
JWT_TOKEN_SECRET=secretKey
```
- package.jsonの`script`に↓のように書く
```json
  "scripts": {
    "start": "node src/index.js",
    "dev": "env-cmd nodemon src/index.js"
  },
```
- ↓このように呼び出せる
```javascript
sendgridMail.setApiKey(process.env.SENDGRID_API_KEY)
```

## eslint
### コンパイル時の未使用変数のチェックをオフ
- vueの場合、直下に`.eslintrc.js`がある
```javascript
module.exports = {
  rules: {
    "no-unused-vars": "off"
  }
}
```

## node API
### HTTP/HTTPs

```javascript
const https = require('https')

const url = `https://api.mapbox.com/geocoding/...`

const request = https.request(url, (response) => {
  let data = ''

  response.on('data', (chunk) => {
    data = data + chunk.toString()
  })

  response.on('end', () => {
    const body = JSON.parse(data)
    console.log(body);
  })
})

request.on('error', (error) => {
  console.log('error', error);
})

request.end()
```


## npmコマンド

### list
引数なしで実行すると大量に出てくるのでdepthで深さを指定する. 0始まり
```bash
npm list --depth=0
```

### install
```bash
npm install
```
でpackage.jsonのdependenciesに記載のライブラリを記載のバージョンルールに従ってインストールする.

- ライブラリ指定の場合
```bash
npm install node-fetch@x.x.x
```
@以下をlatestにすると最新をインストール

- 開発環境にインストール
```bash
npm install jest --save-dev
```
--save-devをつける. package.jsonにはdevDependenciesに追加される

- `--save`は今は要らない。昔のnpmコマンドはpackage.jsonのdependenciesに追加する機能だった模様. 

- `-f` or `--force`で強制インストール.  piniaを使用しているプロジェクトを`npm install`したらエラーになったので、単体で`-f`でインストールしたら治った


### audit force
vulnerabilitiesが表示されたら
```bash
npm audit fix --force
```
を実行する

### outdated
ライブラリのバージョンが古いものを表示する


## nvm
nodeのバージョンを管理する.  

### install
```bash
brew install nvm
```
でインストールする.  

### profileに追加する

```.bash_profile
export NVM_DIR="$HOME/.nvm"
[ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh" 
[ -s "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/opt/homebrew/opt/nvm/etc/bash_completi$
```

### use
```bash
nvm install 18
nvm use 18
```
で指定したバージョンをインストール、使用する.   

### プロジェクトディレクトリで特定のバージョンのnodeを使用する
```.nvmrc
v18.20.6
```
でルートディレクトリに.nvmrcファイルを作成する
```bash
nvm use
```
を実行すると、nvmrcファイルのバージョンのnodeが使用される.  




## M3Macに変えたときにnodeをarm64にするためにおこなったこと

1. あまり記憶がないが、homebrewにnodeが単体で入っていた(nvmで管理していないもの)ので、それをアンイストールした
```bash
brew uninstall node
```
PATHの先頭に`/opt/homebrew/bin:/opt/homebrew/sbin:/`が入っていて、必ずhomebrewのnodeが有効になってしまっていたため。


2. nvmもarm64にした（上のnvmのインストールとbash_profileの編集を参照）
3. node v18をインストールした
```bash
nvm install 18
nvm use 18
```
4. プロジェクトディレクトリで以下を実行
```bash
rm -rf node_modules
rm package-lock.json
npm cache clean --force
npm install  --legacy-peer-deps
```
` --legacy-peer-deps`: nvm7からは厳密にライブラリ間のバージョンを管理するようになったため、このオプションをつけることで、それを緩和する。

## npmライブラリ

### nodemon
```bash
nodemon app.js
```
とすると、app.jsが更新されるたびに実行してくれる.  

#### オプション
-e: 自動更新するファイルの拡張子を指定できる
```bash
nodemon app.js -e js,hbs
```
#### ローカルインストールした場合
ローカルにインストールすると`nodemon`では実行できないので、
package.jsonに`dev: "nodemon src/app.js"`として、
```bash
npm run dev
```
で実行する.  

### chalk
標準出力の色を変えられる

```javascript
const chalk = require('chalk')
console.log(chalk.red.inverse('gmail.com'))
```
### yargs
コマンドラインのパーサー  

- 標準入力は"_"に、--hogeはargvのプロパティになる
ex. node app.js add --buzz="fizz"で実行した場合
```javascript
const yargs = require('yargs')
console.log(yargs.argv)
console.log(yargs.argv.buzz) //→fizz
console.log(yargs.argv._[0]) //→add
```

- helpの表示/コマンドの定義
```javascript
const yargs = require('yargs')
yargs.command({
  command: 'add',
  describe: 'Add a new note',
  handler: function () {
    console.log('Adding a new note.')
  }
}).command({
  command: 'remove',
  describe: 'Remove a note',
  handler: function () {
    console.log('Removing the note.')
  }
}).help().argv
```
- builderを入れるとパラメータのチェックが可能
	→これだと --help　が動かない（→必要になったとき調べる...）
```javascript
yargs.command({
  command: 'add',
  describe: 'Add a new note',
  builder: {
    title: {
      describe: 'Note title',
      demandOption: true,
      type: 'string'
    },
    body: {
      describe: 'Note body',
      demandOption: true,
      type: 'string'
    }
  }).argv
```

### request
今は更新されていなくて、後継はpostman-request  
ex.  
```javascript
request = require('request')

const geocode = (address, callback) => {
  const url = `https://api.mapbox.com/geocoding/v5/mapbox.places/${encodeURIComponent(address)}.json?access_token=pk...&limit=1`
  
  request({url, json: true}, (error, {body}) => {
    if (error) {
      callback('Unable to connect geocoding.', undefined)	//callbackを呼び出し側で使う方法
    } else if (!body.features || body.features.length === 0) {
      callback('Unable to find location. Try another address.', undefined)
    } else {
      features_ = body.features[0]
      callback(undefined, {
        logitude: features_.center[0],
        latitude: features_.center[1],
        location: features_.place_name
      })
    }
  })
}
```

### express
webserverライブラリ. 
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

- index.htmlの表示
	- index.htmlはpublicの下に配置
	- index.jsはsrcの下に配置
```javascript
const express = require('express');
const port = process.env.PORT
const path = require('path');

const app = express()
const publicDirectoryPath = path.join(__dirname, '../public')

app.use(express.static(publicDirectoryPath))	//...publicフォルダの指定

app.get('', (req, res) => {
  res.sendFile('index.html')		//...index.htmlはsendFileで表示する
})

app.listen(port, () => {
  console.log('listen: ', port);
})
```


### handlebars
テンプレートエンジン. expressと一緒に使う場合はプラグイン版のhbs.jsを使う.  

### hbs
expressのhandlebarsプラグイン.  
express + hbs を使った例↓  
```javascript
const express = require('express');
const path = require('path');
const hbs = require('hbs');

const app = express()
const port = 3000

// define paths for Express config
const publicDirectoryPath = path.join(__dirname, '../public')
const viewsPath = path.join(__dirname, '../template/views')
const partialsPath = path.join(__dirname, '../template/partials')

// setup handlebars engine and views location
app.set('view engine', 'hbs')
app.set('views', viewsPath)
hbs.registerPartials(partialsPath)

// setup static directory to serve
app.use(express.static(publicDirectoryPath))

app.get('', (req, res) => {
  res.render('index', {
    title: 'Weather app',
    name: 'Mechael'
  })
})

app.get('*', (req, res) => {
  res.render('404', {
    title: '404 Error Page',
    name: 'Mechael'
  })
})

app.listen(port, () => {
  console.log('listen: ', port);
})
```

```html
<!DOCTYPE html>
<html lang="ja">
<head>
<!-- ... 省略 -->
</head>
<body>
    <div class="main-content">
        {{>header}}
        <p>Use this site to get your weather</p>
    </div>
    {{>footer}}
</body>
</html>
```

```html
<header>
<h1>{{title}}</h1>

<a href="/">Weather</a>
</header>
```

### mongoDB
- CRUDの例
```javascript
const {MongoClient, ObjectID} = require('mongodb');

const connectionURL = 'mongodb://127.0.0.1:27017'
const databaseName = 'task-manager'

const id = new ObjectID()	//ObjectIdを自分で生成する場合

MongoClient.connect(connectionURL, {useNewUrlParser: true}, (error, client) => {
  if (error) {
    console.log('connection error');
  }
  console.log('connection success');

  const db = client.db(databaseName)
	
  //1件追加
  db.collection('users').insertOne(
    {_id: id, name: 'John', age: 48}, (error, result) => {		//idは指定しなくてもよい. id=_idとなる. 
      if (error) {
        return console.log('insert error');
      }
      console.log(result.insertedCount);
  })

  //複数件追加
  db.collection('users').insertMany([
    {name: 'Paul', age: 47},
    {name: 'Jeorge', age: 46},
  ], (error, result) => {
      if (error) {
        return console.log('insert error');
      }
      console.log(result);
  })
  
  //1件索引
  db.collection('users').findOne({name: 'John'}, (error, user) => {
  if (error) {
      return console.log('Unable to find user');
    }
    console.log(user);

  })

  //複数件索引
  db.collection('users').find({name: 'John'}).toArray((error, users) => {
    if (error) {
        return console.log('Unable to find user');
      }
      console.log(users);
  })
  //カウント取得
  db.collection('users').find({name: 'John'}).count((error, count) => {
      console.log(count);
  })

  //更新のPromiseを使った例...↑のinsert, findもPromiseを使って書ける
  db.collection('users').updateOne({
    name: 'Paul'
  },{
    age: 26
  }).then((result) => {		//正常の場合
    console.log(result);
  }).catch((error) => {		//エラーの場合
    console.log(error);
  })
  //削除
  db.collection('users').deleteMany({
    name: {$ne: 'John'}		//"="以外の条件はこのように書く. これは not equalの例. その他のキーワードはhttps://www.mongodb.com/docs/manual/reference/operator/query/
  }).then((result) => {
    console.log(result);
  }).catch((error) => {
    console.log(error);
  })

})
```

### mongoose
mongoDBとのORMで使う.  

#### modelの使用その1
```javascript
const mongoose = require('mongoose');
const validator = require('validator');	//mongooseはバリデーションライブラリを組み合わせて使う

const connectionURL = 'mongodb://127.0.0.1:27017/task-manager-api'

mongoose.connect(connectionURL, {
  useNewUrlParser: true,
  autoIndex: true
})

//modelの定義
const User = mongoose.model('User', {		//model名をUserとするとMongoDBのDocument名は'users'となる
  name:{
    type: String,
    required: true,
    trim: true
  },
  password:{
    type: String,
    required: true,
    trim: true,
    minlength: 7,
    validate(value) {
      if (value.toLowerCase().includes('password')) {
        throw new Error('password cannot contain "password"')
      }
    }
  },
  email:{
    type: String,
    required: true,
    trim: true,
    lowercase:true,
    validate(value) {
      if (!validator.isEmail(value)){	//バリデーションライブラリの使用
        throw new Error('Email is invalid')
      }
    }
  },
  age: {
    type: Number,
    default:0,
    validate(value) {
      if (value < 0) {
        throw new Error('age must be positive number')
      }
    }
  },
  tokens: [{	//リスト形式
    token: {
      type: String,
      required: true
    }
  }],
  avatar: {
    type: Buffer	//バイナリ形式...イメージファイルなどの保存
  }
})

const me = new User({
  name: 'Jeorge  ',
  password: 'jeorgePassWord',
  email: 'Jeorge@yahoo.com   ',
  age: 45
})

me.save().then(() => {	//保存でPromise
  console.log(me);
}).catch((error) => {
  console.log(error);
})
```
#### modelの使用その2
その1で定義したプロパティを`Schema`に入れて、`Schema`に対してファンクションなどを追加定義できる  
```javascript
const mongoose = require('mongoose');
const validator = require('validator');
const bcryptjs = require('bcryptjs');
const jwt = require('jsonwebtoken')

const userSchema = mongoose.Schema({	//Schemaの定義
  name:{
    type: String,
    required: true,
    trim: true
  },
  password:{
	//...省略
  },
  email:{
	//...省略
  },
  age: {
	//...省略
  }
},
{
  timestamps: true		//Schemaの2引数にtimestamps: trueを入れると`createdAt`と`updatedAt`の2項目が自動的に追加される. もちろんドキュメントのcreate/updateでそれぞれの項目が更新される
})

userSchema.methods.generateAuthToken = async function () {	//methodsでインスタンスに紐ついたファンクションを定義する
  const user = this
  const token = jwt.sign({_id: user._id.toString()}, 'secretKey')

  user.tokens = user.tokens.concat({token})
  user.save()
  return token
}

userSchema.statics.findByCredentials = async (email, password) => {	//staticsでオブジェクトに紐ついたファンクションを定義する

  const user = await User.findOne({email})

  if (!user) {
    throw new Error('Unable to login')
  }
  const isMatch = await bcryptjs.compare(password, user.password)

  if (!isMatch) {
    throw new Error('Unable to login')
  }
  return user
}
userSchema.pre('save', async function (next) {		//preで"save"メソッドの前に実行することを定義する. ここではパスワードのhash化を入れている
  const user = this

  if (user.isModified('password')) {
    user.password = await bcryptjs.hash(user.password, 8)
  }
  next()
})

userSchema.pre('remove', async function (next) {	//ユーザ削除時にタスクも削除するcascade処理もpre - removeで入れることができる.  
  const user = this
  await Task.deleteMany({owner: user._id})
  next()
})


const User = mongoose.model('User', userSchema)	//modelにはSchemaを渡す


module.exports = User
```


#### expressを使ったCRUD
```javascript
const express = require('express');
require('./db/mongoose');
const User = require('./models/user')
const Task = require('./models/task')

const app = express()
const port = process.env.PORT || 3000

app.use(express.json())

app.post('/users', async (req, res) => {	//追加
  const user = new User(req.body)

  try {
    await user.save()
    res.status(201).send(user)
  } catch (error) {
    res.status(400).send(error)
  }
})

app.get('/users', async (req, res) => {	//全件取得
  try {
    users = await User.find({}) 
    res.send(users)
  } catch (error) {
    res.status(500).send()
  }
})

app.get('/users/:id', async (req, res) => {	//1件取得
  const _id = req.params.id

  try {
    user = await User.findById(_id)
    if (!user) {
      return res.status(404).send()
    }
    res.send(user)
  } catch (error) {
    res.status(500).send()
  }
})

app.patch('/users/:id', async (req, res) => {	//更新
  const updates = Object.keys(req.body)
  const allowedUpdates = ['name', 'password', 'email', 'age']
  const isValidOperation = updates.every((update) => allowedUpdates.includes(update))	//違うプロパティがセットされていないかチェック. チェックしないと想定外のプロパティがセットできてしまうため.

  if (!isValidOperation) {
    return res.status(400).send({'error': 'Invalid updates'})
  }
  try {
    //const user = await User.findByIdAndUpdate(req.params.id, req.body, {new: true, runValidators: true})	//saveメソッドにミドルウェアをカマしたいときに効かないので、自前で編集する↓
    const user = await User.findById(req.params.id)
    updates.forEach((update) => user[update] = req.body[update])
    await user.save()
        
    if (!user) {
      res.status(404).send()
    }
    res.send(user)
  } catch (e) {
    res.status(400).send()
  }
})

app.delete('/users/:id', async (req, res) => {	//削除
  try {
    const user = await User.findByIdAndDelete(req.params.id)
    if (!user) {
      res.status(404).send()
    }
    res.send(user)
  } catch (e) {
    res.status(500).send()
  }
})
```

#### 外部キー設定
- UserとTaskが1:多の関係
	- Task Model
```javascript
const Task = mongoose.model('Task', {
  description:{
//省略
  },
  completed: {
//省略
  },
  owner: {		//←UserのID
    type: mongoose.Schema.Types.ObjectId,
    required: true,
    ref: 'User'
  }
})
```
  - User Model 
```javascript
const userSchema = mongoose.Schema({
//省略
})

userSchema.virtual('tasks', {
  ref: 'Task', 
  localField: '_id',
  foreignField: 'owner'
})
```

  - 取得の仕方
```javascript
const User = require('./models/user')
const Task = require('./models/task')

const main = async () => {
  const task = await Task.findById('63553a6fa704fc04e8f5079a')
  await task.populate('owner')
  console.log(task.owner);

  const user = await User.findById('635534c2622ec3cc1aa10d45')
  await user.populate('tasks')
  console.log(user.tasks);
}
main()
```
#### フィルターとソートとページネーションとクエリストリング
```javascript
router.get('/tasks', auth, async (req, res) => {

  try {
    const match = {}
    const sort = {}

    if (req.query.completed) {	//req.queryでクエリストリング
      match.completed = req.query.completed === 'true'
    }

    if (req.query.sortBy) {
      const parts = req.query.sortBy.split(':')
      sort[parts[0]] = parts[1] === 'desc' ? -1 : 1
    }
    await req.user.populate({
      path: 'tasks',
      match,		//populate.matchでフィルター条件を書く.  ex: `completed: true`
      options: {	//populate.optionsでページネーションとソート
        limit: parseInt(req.query.limit),		//limitで取得件数
        skip: parseInt(req.query.skip),			//skipで読み飛ばし件数
        sort					//sortでソート条件.  ex: `createdAt: 1`...createdAtの昇順 -1で降順
      }
    })
    res.send(req.user.tasks)
  } catch (error) {
    res.status(500).send(error)
  }
})
```


### Bcryptjs
- ハッシュライブラリ
- ハッシュされたパスワードは毎回変わる. 
ex. 
jeorge@1234→$2a$08$m2sYco.rRCvCD/wP3rYFPuPrNPvOcrvp0qsK0NZtLjVSlhMOLL1sO

```javascript
const bcryptjs = require('bcryptjs');

const hashPass = async () => {
  const password = 'aawweav'
  const hashedPass = await bcryptjs.hash(password, 8)	//第2引数はハッシュ回数. 8が標準らしい

  const isMatch = await bcryptjs.compare(password, hashedPass)
}
```

### Json Web Token
- jsonをtoken化する
```javascript
const jwt = require('jsonwebtoken')

const func = async () => {
  const token = jwt.sign({_id: 'abc123'}, 'aaeebaetakpp')	//第2引数はtoken化するキー
  console.log(token);

  const data = jwt.verify(token, 'aaeebaetakpp')
  console.log(data);
}
```
↑の`token`の中身  
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJfaWQiOiJhYmMxMjMiLCJpYXQiOjE2NjUyMjE5Njl9.4JTv8RBeYpWmN_A3bVOZOqG6n9h7kCxDg9lSOzWaCyA  
"."で区切られた3つの値.  
1つ目（base64エンコードされている）: {"alg":"HS256","typ":"JWT"}
2つ目（base64エンコードされている）: signの第1引数に生成されたタイムスタンプが付与されたjson
3つ目はハッシュ値

### MulterとSharp
- multer: ファイルアップロードに使用する
- sharp: 画像のリサイズに使用する
- この例はformでアップロードされたファイルを受け取り、mongooseに保存する例
```javascript
const multer = require('multer');
const sharp = require('sharp');
const upload = multer({
  // dest: 'avatars',		//ファイルとして保存する場合は保存先を指定する. S3とかもいけるはず.
  limits: {
    fileSize: 1000000	//ファイルサイズ制限
  },
  fileFilter(req, file, cb) {	//拡張子によるファイル種類の制限
    if(!file.originalname.match(/\.(jpg|jpeg|png)$/i)) {
      return cb(new Error('please upload a jpg'))
    }
    cb(undefined, true)
  }
})

router.post('/users/me/avatar', auth, upload.single('avatar'), async (req, res) => {	//ミドルウェアでsingleを指定する
  const buffer = await sharp(req.file.buffer).resize({width: 250, height:250}).png().toBuffer()	//sharpによるリサイズと、png化
  //req.file.bufferは　multerのインストラクタでdestを指定しない場合に使用できる（メモリに保存する）
  req.user.avatar = buffer
  await req.user.save()
  res.send()
}, (error, req, res, next) => {
  res.status(400).send({error: error.message })
})
```
### sendgrid/mail
- [sendgrid](https://sendgrid.com/)を使うとかんたんにemailが送れる.  
- AWS SESよりかんたんかも.
- sendgridのnodeライブラリはsendgrid/mail

```javascript
const sendgridMail = require('@sendgrid/mail');

sendgridMail.setApiKey(process.env.SENDGRID_API_KEY)	

const sendWelcomeEmail = (email, name) => {
  sendgridMail.send({
    to: email,
    from: 'test@mail.com',
    subject: 'Thanks for joining in!',
    text: `Welcome to the app. ${name}. Let me know how you get along with the app.`
  })
}

const sendCancelEmail = (email, name) => {
  sendgridMail.send({
    to: email,
    from: 'test@mail.com',
    subject: 'Sorry to see you go!',
    text: `Goodbye, ${name}. I hope to see you back sometime soon.`
  })
}


module.exports = {sendWelcomeEmail, sendCancelEmail}
```

### Jest
- テストツール

```json
  "scripts": {
    "start": "node src/index.js",
    "dev": "env-cmd nodemon src/index.js",
    "test": "env-cmd -f ./tests/.env jest --watchAll --runInBand"
  },
```
- `env-cmd`でテスト用の環境変数を渡す
- `--watchAll`: nodemonのようなもの. 保存するごとにテストを実行できる
- `--runInBand`: すべて直列で起動する. task-manager appのときの、user-routerとtask-routerをそれぞれ実行する場合などに使用する. 
- /tests ディレクトリに~.test.jsファイルを置くとすべて実行される. （たぶん）

#### testとアサート
```javascript
//特にrequireするものはない
beforeEach(setupDatabase)	//testの実行の前に実行する関数を書ける

test('Should signup a new user', async () => {	//test関数が1つのテストの単位. 第1引数にテストの名称や目的、第2引数にテストの関数
  const user = await User.findById(response.body.user._id)
  expect(user).not.toBeNull()		//expectでアサートする
})
```

#### ライブラリをモックする
- メール送信などでテストのときは実行したくないものは、`__mocks__`ディレクトリを/testsの下につくるとそちらのライブラリを見に行く

- /tests/__mocks__/@sendgrid/mail.jsを中身を空で作る
```javascript
module.exports = {
  setApiKey() {

  },
  send() {

  }
}
```
↓の`sendWelcomeEmail`はモックにできる. 
```javascript
//...
const { sendWelcomeEmail, sendCancelEmail } = require('../emails/account')

router.post('/users', async (req, res) => {
//...
    sendWelcomeEmail(user.email, user.name)
//...
})

```

### SuperTest
- httpのテストモジュール. expressのテストに使う

```javascript
const request = require('supertest');
const express = require('express');
const app = express()

test('Should get profile for user', async () => {	//引数にexpressを渡し、httpメソッド, set, send, attachなどをチェーンできる. 最後にexpectでhttpステータスコードを検証する
  await request(app)
      .get('/users/me')
      .set('Authorization', `Bearer ${userOne.tokens[0].token}`)
      .send()
      .expect(200)
})
```

### JestとSuperTestを使ったテストの例
```javascript
const express = require('express');
require('./db/mongoose');
const userRouter = require('./routers/user')
const taskRouter = require('./routers/task')

const app = express()

app.use(express.json())
app.use(userRouter)
app.use(taskRouter)

module.exports = app
```
- ↑app.jsはアプリで使っているモジュール. テストでも使えるようにモジュール化する

```javascript
// テストデータなどをまとめて書く用
const jwt = require('jsonwebtoken');
const mongoose = require('mongoose');
const User = require('../../src/models/user')
const Task = require('../../src/models/task');
const { MongoGridFSChunkError } = require('mongodb');

const userOneId = new mongoose.Types.ObjectId()
const userOne = {
  _id: userOneId,
  name: 'ringo',
  email: 'ringo@mail.com',
  password: 'pass1234',
  tokens: [{
    token:jwt.sign({_id: userOneId}, process.env.JWT_TOKEN_SECRET)
  }]
}

const userTwoId = new mongoose.Types.ObjectId()
const userTwo = {
//...省略
}

const taskOne = {
  _id: new mongoose.Types.ObjectId(),
  description: 'First task',
  completed: false,
  owner: userOneId
}
const taskTwo = {
//...省略
}
const taskThree = {
//...省略
}

const setupDatabase = async () => {		//... testごとの初期化を書く
  await User.deleteMany()
  await Task.deleteMany()
  await new User(userOne).save()
  await new User(userTwo).save()
  await new Task(taskOne).save()
  await new Task(taskTwo).save()
  await new Task(taskThree).save()
}

module.exports = {setupDatabase, userOne, userOneId, userTwo, userTwoId,
  taskOne, taskTwo, taskThree}
```

```javascript
const request = require('supertest');
const app = require('../src/app');
const User = require('../src/models/user')
const {setupDatabase, userOne, userOneId} = require('./fixtures/db');

beforeEach(setupDatabase)	//...testのたびに初期化する

test('Should signup a new user', async () => {
  const response = await request(app).post('/users').send({
    name: 'paul',
    email: 'paul@mail.com',
    password: 'pass1234'
  }).expect(201)

  // Assert that the database was changed correctly
  const user = await User.findById(response.body.user._id)
  expect(user).not.toBeNull()

  // Assertions about the response
  expect(response.body).toMatchObject({
    user: {
      name: 'paul',
      email: 'paul@mail.com'
    },
    token: user.tokens[0].token
  })
  expect(user.password).not.toBe('pass1234')
})

test('Should signup a new user', async () => {
  const response = await request(app).post('/users/login').send({
    email: userOne.email, password: userOne.password
  }).expect(200)

  const user = await User.findById(response.body.user._id)
  expect(response.body.token).toBe(user.tokens[1].token)
})

test('Should get profile for user', async () => {
  await request(app)
      .get('/users/me')
      .set('Authorization', `Bearer ${userOne.tokens[0].token}`)		//...Bearerの送信
      .send()
      .expect(200)
})

test('Should upload avatar image', async () => {
  await request(app)
      .post('/users/me/avatar')
      .set('Authorization', `Bearer ${userOne.tokens[0].token}`)
      .attach('avatar', 'tests/fixtures/profile-back.jpg')		//...画像の送信
      .expect(200)
    const user = await User.findById(userOne._id)
    expect(user.avatar).toEqual(expect.any(Buffer))
})

```

```javascript
const request = require('supertest');
const app = require('../src/app');
const Task = require('../src/models/task')
const {setupDatabase, userOne, userOneId, taskOne, userTwo, userTwoId} = require('./fixtures/db');

beforeEach(setupDatabase)

test('Should create task for user', async () => {
  const response = await request(app)
      .post('/tasks')
      .set('Authorization', `Bearer ${userOne.tokens[0].token}`)
      .send({
        description: 'From my test'
      })
      .expect(201)
  const task = await Task.findById(response.body._id)
  expect(task).not.toBeNull()
  expect(task.completed).toEqual(false)
})

test('Should fetch owner tasks', async () => {
  const response = await request(app)
    .get('/tasks')
    .set('Authorization', `Bearer ${userOne.tokens[0].token}`)
    .send()
    .expect(200)
  expect(response.body.length).toEqual(2)
})

test('Should not delete other users tasks', async () => {
  const response = await request(app)
    .delete(`/tasks/${taskOne._id}`)
    .set('Authorization', `Bearer ${userTwo.tokens[0].token}`)
    .send()
    .expect(404)
  const task = await Task.findById(taskOne._id)
  expect(task).not.toBeNull()
})
```

### async

-[これ](https://developer.mozilla.org/ja/docs/Learn/Server-side/Express_Nodejs/Displaying_data/flow_control_using_async)を参考
- .parallel()で並列実行、.series()で直列実行、waterfall()で直列実行かつ成功のみ次を処理　などができる
- それぞれ引数にはオブジェクトや関数、そのリストを渡す
- .map(collection, function) collectionを渡しながらfunctionを実行


### socket.io
- サーバからプッシュできるライブラリ
- 通常はクライアントからのリクエストに対してサーバがレスポンスするだけだが、socket.ioを使うとサーバからプッシュできる

#### 単純な例1 
クライアントからカウントアップ→サーバの変数がカウントアップ→全ての接続クライアントが受信

```javascript
const path = require('path');
const http = require('http')
const express = require('express');
const socketio = require('socket.io')

const app = express()
const server = http.createServer(app)	//...httpでexpressをラップ
const io = socketio(server)		//...httpをsocketioする

const port = process.env.PORT || 3000
const publicDirectoryPath = path.join(__dirname, '../public')

app.use(express.static(publicDirectoryPath))

let count = 0

io.on('connection', (socket) => {		// connectionでクライアントと接続
  console.log('New WebSocket connection');

  socket.emit('countUpdated', count)	// emitでクライアントの関数を呼び出し
  
  socket.on('increment', () => {	// socket.onでクライアントから受信
   count++
   // socket.emit('countUpdated', count)	// socket.emitはリクエストのあったクライアントのみに返す
   io.emit('countUpdated', count)		// io.emitは全接続クライアントに返す
    
  })
})

app.get('', (req, res) => {
  res.sendFile('index.html')
})


server.listen(port, () => {
  console.log('listen: ', port);
})
```

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    
    <title>Chat App</title>
</head>
<body>
    <h1>Chat App</h1>
    <div class="main-content">
        <button id="increment">+1</button>
    </div>
    <script src="/socket.io/socket.io.js"></script>	<!-- このファイルは存在しないけど書く -->
    <script src="/js/chat.js"></script>
</body>
</html>
```

- クライアントサイドjs
```javascript
const socket = io()

socket.on('countUpdated', (count) => {	//サーバから呼び出される関数を socket.on で定義
  console.log('The count has updated', count);
})

document.querySelector('#increment').addEventListener('click', () => {
  console.log('clicked');
  socket.emit('increment')	// サーバに送信する関数
})
```

#### 単純な例2
クライアントからメッセージを送信→全クライアントが受信

```javascript
// 途中までは例1と同じ

io.on('connection', (socket) => {
  console.log('New WebSocket connection');

  socket.emit('message', 'Welcome!')
  
  socket.on('sendMessage', (message) => {	//クライアントからのメッセージを受け取り、、
    io.emit('message', message)		// 全クライアントに返す
  })
})
```

```html
<!-- bodyの中のみ記述 -->
 <form>
     <input type="text" placeholder="Message" name="message">
     </input>
     <button>send</button>
 </form>
```

```javascript
const socket = io()

socket.on('message', (message) => {	//サーバから受信
  console.log(message);
})
document.querySelector('form').addEventListener('submit', (e) => {
  e.preventDefault()

  const message = e.target.elements.message.value

  socket.emit('sendMessage', message)	// formで送信
})
```

#### 自分以外の人へ通知と切断通知

```javascript
io.on('connection', (socket) => {
  console.log('New WebSocket connection');

  socket.emit('message', 'Welcome!')
  socket.broadcast.emit('message', 'A new user has joined!')	// broadcastで自分以外のクライアントに通知

  socket.on('sendMessage', (message) => {
    io.emit('message', message)
  })

  socket.on('disconnect', () => {		// disconnectはビルトインメソッドで、ブラウザを閉じたときに発火する
    io.emit('message', 'A user has left!')
  })

})

```

#### callbackの受け取り
クライアントのメッセージ送信→サーバ受信→（サーバ側処理結果）→クライアントが受け取る（callback）

```javascript
  socket.emit('sendMessage', message, (error) => {
    if (error) {	//サーバからのerrorを受け取って、エラーメッセージを表示
      return console.log(error);
    }
    console.log('The message was delivered!');
  })	// 3番目にcallbackファンクションを指定する 
```
```javascript
const  Filter = require('bad-words')	// 禁止文字ライブラリ（英語）
  socket.on('sendMessage', (message, callback) => {	//メッセージとファンクション（callback）を受け取る
    const filter = new Filter()

    if (filter.isProfane(message)) {
      return callback('Profanity is not allowed!')	// 禁止文字があるならエラーメッセージとreturn
    }
    io.emit('message', message)
    callback()
  })

```

#### chat roomへの参加と、そのroomにだけメッセージを通知する

```javascript
  socket.on('join', (options, callback) => {

    const {error, user} = addUser({id: socket.id, ...options})

    if (error) {
      return callback(error)
    }
    
    socket.join(user.room)	// joinで特定のroomに参加させる

    socket.emit('message', generateMessage('Admin', 'Welcome!'))
    socket.broadcast.to(user.room).emit('message', generateMessage('Admin', `${user.username} has joined!`))		// broadcast.to().emitで該当roomにだけ通知する
    io.to(user.room).emit('roomData', {	//io.to().emitも同様
      room: user.room,
      users: getUsersInRoom(user.room)
    })
    callback()
  })
```

### mustache.js
https://github.com/janl/mustache.js/
ライブラリ不要のtemplateシステム

```html
<body>
    <div class="chat">
        <div class="chat__main">
            <div id="messages" class="chat__messages"></div>
        
            <div class="compose">
...
            </div>
        </div>
    </div>

    <script id="message-template" type="text/html">    <!-- scriptタグで囲ってtemplateとなるhtmlを記述する -->
        <div class="message">
            <p>
                <span class="message__name">{{username}}</span>    <!-- 変数は{{ }}で囲って指定できる -->
                <span class="message__meta">{{createdAt}}</span>
            </p>
            <p>{{message}}</p>
        </div>
    </script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mustache.js/3.2.1/mustache.min.js"  crossorigin="anonymous" referrerpolicy="no-referrer"></script>    <!-- cdnのjsを参照するだけ -->
...
</body>
```
 
```javascript
const  $messages = document.querySelector('#messages')
const  messagesTemplate = document.querySelector('#message-template').innerHTML

socket.on('message', (message) => {

  const html = Mustache.render(messagesTemplate, {	// Mustache.render()で、templateタグと変数をオブジェクトで渡す
    username: message.username,
    message: message.text,
    createdAt: moment(message.createdAt).format('h:mm a')
  })
  $messages.insertAdjacentHTML('beforeend', html)	// htmlに挿入するだけ
  autoscroll()
})

```
繰り返しタグも可能
```html
    <script id="sidebar-template" type="text/html">
        <h2 class="room-title">{{room}}</h2>
        <h3 class="list-title">Users</h3>
        <ul class="users">
            {{#users}}		<!-- 繰り返しオブジェクトを{{# }}で囲む -->
                <li>{{username}}</li>  <!-- 要素の中のプロパティを指定 -->
            {{/users}}
        </ul>
    </script>
```
```javascript
socket.on('roomData', ({ room, users }) => {
  const html = Mustache.render(sidebarTemplate, {
    room: room,
    users: users
  })
  $sidebar.innerHTML = html
})
```
### moment.js
https://momentjs.com/
日付フォーマットライブラリ

### qs.js
https://www.npmjs.com/package/qs
クエリストリングをパースする

```javascript
const {username, room} = Qs.parse(location.search, { ignoreQueryPrefix:  true })
```
`http://localhost?username=hoge&room=fuga`から値を取れる

 
### style-loader / css-loader / sass-loader

jsにcssを適用するときに適用するライブラリ.  

こういう意味らしい. 
// Creates `style` nodes from JS strings
"style-loader"
// Translates CSS into CommonJS
"css-loader"
// Compiles Sass to CSS
"sass-loader"

- webpackの定義
```javascript
  module: {
    rules: [{
      test: /\.scss$/,
      use: ["style-loader", "css-loader", "sass-loader"],
    }]
  },
```
- jsにimport
```javascript
import './styles/styles.scss'
```


