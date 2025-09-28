---
title: Vue 3 & Firebase (Udemy) メモ
---


# Vue JS3 & Firebase


このメモは[Udemy講座](https://www.udemy.com/share/1062NM/)を受講して学んだ記録です。  
作者には感謝します。


## Vueの基礎


1. html/headにVueの参照を入れる（CDNで使う方法）


`index.html`

```html
    <script src="https://unpkg.com/vue@3.0.2"></script>
```
@の後ろを"next"にすると常に最新になる


2. /bodyの前にスクリプトを入れる


`index.html`

```html
    <script src="app.js"></script>
```


- app.jsの基本系


`app.js`

```javascript
const app = Vue.createApp({
    data() {
        return {
            title: 'The Final Empire',
            author: 'Brandon',
            age : 45
        }
    }
})


app.mount('#app')
```


3. htmlでは上の例だとid=appの中がVueが有効になる


`index.html`

```html
    <div id="app">
        <p>{{ title }} - {{ author }} - {{ age }} </p>


    </div>
```


## 文法


- v-on:click / @click
app.js側のプロパティにアクセスするにはプロパティを{{}}で囲う
clickイベントなどでVueの中の変数にアクセスする場合は、v-on:をclickの前に書くか、単に@を書く


index.html

```html
        <button v-on:click="age++">age increment</button>
        <div @click="changeTitle">click here </div>
```


- v-if / v-if ...v-else


index.html

```html
            <span v-if="showBooks">Hide Books</span>
            <span v-else>Show Books</span>
```


v-if=の後ろがTrueならその要素を有効、Falseなら無効にする
v-elseは直前にv-ifがあるときに有効


- v-show


index.html

```html
        <div v-show="showBooks">currently show books</div>
```


単に表示/非表示を切り替えるならv-show=を使う
v-ifと同様、後ろがTrueなら表示、Falseなら非表示


v-ifとv-showの違いは、htmlとしては、v-ifは要素の存在自体を定義、
v-showはFalseの場合、display:noneを入れる




- 他のイベント mouseover / mouseleave / dblclick / mousemove  ...methodsプロパティ


index.html

```html
        <div class="box" @mouseover="handleEvent">mouse over</div>
        <div class="box" @mouseleave="handleEvent">mouse leave</div>
        <div class="box" @dblclick="handleEvent">double click</div>
        <div class="box" @mousemove="handleMousemove">position {{x}} -{{y}}</div>
```
app.js

```javascript
    methods:{
        toggleShowBooks(){
            this.showBooks = !this.showBooks
        },
        handleEvent(e){
            this.eventName = e.type
        },
        handleMousemove(e){
            this.x = e.offsetX
            this.y = e.offsetY
        }
    }
```
イベントのJS関数はmethodsプロパティに定義する


- v-for 繰り返し


index.html

```html
            <ul>
                <li v-for="book in books">
                    <h3>{{ book.title }}</h3>
                    <p>{{ book.author }}</p>
                </li>
            </ul>
```
ul / li じゃなくても可能。普通にdivでも可


app.js

```javascript
const app = Vue.createApp({
    data() {
        return {
            books:[
                {title:"book 1", author:"author 1"},
                {title:"book 2", author:"author 2"},
                {title:"book 3", author:"author 3"},
            ]
        }
    }
})
```
- v-for で要素はそのまま渡せる


index.html

```html
            <li v-for="book in books" :class="{ fav:book.fav }" @click="changeFav(book)">
```
changeFavでv-forの要素であるbookオブジェクトをそのまま渡す


app.js

```javascript
        changeFav(book){
            book.fav = !book.fav
        }
```




- 属性のバインド v-bind: または単純に : のみ


index.html

```html
                    <img :src="book.img" :alt="book.title">
```


app.js

```javascript
            books:[
                {title:"book 1", author:"author 1", img:"asset/1.jpg"},
                {title:"book 2", author:"author 2", img:"asset/2.jpg"},
                {title:"book 3", author:"author 3", img:"asset/3.jpg"},
            ]
```


- Dinamic Class
class 指定の動的な設定


index.html

```html
                <li v-for="book in books" :class="{ fav:book.fav }">
```
コロン(:)始まりは v-bind と同じ
class="{ fav:book.fav}" でbook.favがtrueなら、favクラスが有効、falseなら無効になる


app.js

```javascript
            books:[
                {title:"book 1", author:"author 1", img:"asset/1.jpg", fav:true},
                {title:"book 2", author:"author 2", img:"asset/2.jpg", fav:false},
                {title:"book 3", author:"author 3", img:"asset/3.jpg", fav:true},
            ]
```


- computedプロパティ


dataでもmethodsでもない、dataの操作など


app.js

```javascript
const app = Vue.createApp({
    data() {
        return {
            books:[
                {title:"book 1", author:"author 1", img:"asset/1.jpg", fav:true},
                {title:"book 2", author:"author 2", img:"asset/2.jpg", fav:false},
                {title:"book 3", author:"author 3", img:"asset/3.jpg", fav:true},
            ]
        }
    },
    methods:{
...
    },
    computed:{
        filterBooks() {
            return this.books.filter((book) => book.fav)
        }
    }
})
```


index.html

```html
            <li v-for="book in filterBooks" :class="{ fav:book.fav }" @click="changeFav(book)">
```


↑のようにcomputedで定義する関数はどこでも使える
computedの中のdataの操作は即反映される




## Vue CLI


### install


- node.jsをインストールする
https://nodejs.org/ja/download/


- Vue CLI をインストールする

```bash
npm install -g @vue/cli
```


- 任意のフォルダでプロジェクトを作成する

```bash
vue create [プロジェクト名]
```


- VS Codeで開く

```bash
cd [プロジェクトフォルダ]
code .
```


### ローカルサーバ起動

```bash
npm run serve
```


- win8×vue17ではERR_OSSL_EVP_UNSUPPORTEDのエラーになる
`package.json`

```json
  "scripts": {
    "serve": "set NODE_OPTIONS=--openssl-legacy-provider && vue-cli-service serve",
    "build": "set NODE_OPTIONS=--openssl-legacy-provider && vue-cli-service build"
  }
```
↑NODE_OPTIONS=--openssl-legacy-providerを追加する


## Vue プロジェクト


### フォルダ構成

```
node_modules
public
    ├─ favicon.ico
    └─ index.html
src
    ├─ components
    ├─ App.vue
    └─ main.js
```


- public/inex.html がブラウザに表示されるhtml. ただし、

    <div id="app"></div>
のみでコンテンツはない


- app divに入るのは、src/App.vueのtemplateタグ内
`App.vue`

```vue
<template>
...
</template>


<script>
export default {
  name: 'App',
  data() {
    return {
      title : 'My First Vue App'
    }
  }
}
</script>


<style>
...
</style>
```


templateタグ →→ index.htmlのapp divの中
styleタグ →→ index.htmlのhead styleの中
scriptタグ →→ vueのことを書く




### ルールなど


- refsでオブジェクト参照


`App.vue` / template tag

```vue
  <input type="text" ref="name">
  <button @click="handleClick">click me</button>
```
ref=[オブジェクト名]を定義


`App.vue` / script tag

```javascript
  methods :{
    handleClick() {
      this.$refs.name.focus()
    }
  }
```
vue内でthis.$refs.[オブジェクト名]でDOM参照


- componetsのimport


App.vueに全部書くわけにはいかないので、App.vueを分ける

```
components
    └─ Modal.vue
App.vue
```


`App.vue`

```vue
<template>
  <h1>{{ title }}</h1>
  <Modal />         <!-- importしたcomponentsを書く -->
</template>


<script>
import Modal from './components/Modal.vue'          <!-- ...importする -->


export default {
  name: 'App',
  data() {
...
  },
  components:{ Modal },     <!-- importしたcomponentsを書く -->
  methods :{
...
  }
}
</script>
```
`Modal.vue`

```vue
<template>
    <div class="backdrop">
        <div class="modal">
            <p>modal content</p>
        </div>
    </div>
</template>


<style>
    .modal {
...
    }
    .backdrop {
...
    }
</style>
```


- Component Style と Global Style


src/assets/global.cssを追加する
これはアプリ全体に適用される


`main.js`

```javascript
import { createApp } from 'vue'
import App from './App.vue'
import './assets/global.css'    <!-- ...インポートする -->


createApp(App).mount('#app')
```


`Modal.vue`

```vue
<template>
    <div class="backdrop">
        <div class="modal">
            <h1>modal title</h1>
            <p>modal content</p>
        </div>
    </div>
</template>


<style>     <!-- ...style scope と書くこともできるが、すべてのタグにデータが付くためパフォーマンスが悪い -->
    .modal h1 {         <!-- ...このようにコンポーネントの中は クラス指定でスタイルを入れる -->
        color: skyblue;
        border: none;
        padding: 0;
    }
    .modal p {
        font-style: normal;
    }
</style>
```


- 親Vueから子Vueにデータの受け渡し
`App.vue`

```vue
<template>
  <Modal :header="header" :text="text" theme="sale" />  <!-- ...子に渡すデータを書く. ":"コロン付きにすることで、=の後ろが↓のdataであることを示す. コロンなしはModal側に文字列として渡る -->
</template>


<script>
import Modal from './components/Modal.vue'


export default {
  name: 'App',
  data() {
    return {
      title : 'My First Vue App',
      header:"Sign up for the Giveaway!",           <!-- ...データを定義する -->
      text:"Grab your ninja"
    }
  }
}
</script>
```


`Modal.vue`

```vue
<template>
    <div class="backdrop">
        <div class="modal" :class="{ sale: theme === 'sale' }"> # ...渡されたデータはclassに使うことでスタイルを変更することができる. ここでも":"コロン始まりは=の後ろがデータであることを示す
            <h1>{{ header }}</h1>
            <p>{{ text }}</p>
        </div>
    </div>
</template>


<script>
export default {
    props: ['header', 'text', 'theme']  # ...渡されるデータをpropsで定義する
}
</script>
```


- 子から親の関数を呼び出す
例えばモーダル画面が表示されている状態で親の変数を介してモーダルを非表示にするとき


Modal.vue

```vue
<template>
    <div class="backdrop" @click="closeModal">  # ...ここにクリックイベント
...
    </div>
</template>


<script>
export default {
    methods: {
        closeModal() {
            this.$emit('close')     # 上で定義されたクリックイベントの中身は、this.$emit()という組み込み関数. 親を呼べるようにする
        }
    }
}
</script>
```


emitの中で、親の何を呼ぶかは親側で決める


`App.vue`

```vue
<template>
  <h1>{{ title }}</h1>
  <p>Welcome ...</p>
  <div v-if="showModal">
    <Modal :header="header" :text="text" theme="sale" @close="toggleModal" />       # ...ココにModalのemitで定義した"close"を、@closeとして定義. ＝としてmethodを記述
  </div>
  <button @click="toggleModal">open modal</button>
</template>


<script>
import Modal from './components/Modal.vue'


export default {
  name: 'App',
  data() {
...
  },
  components:{ Modal },
  methods :{
    toggleModal() {     # closeで呼び出している関数
      this.showModal = !this.showModal
    }
  }
}
</script>
```


- 細かなクリックイベント
    - click.rightで右クリックのみ

```html
  <button @click.right="toggleModal">open modal</button>
```
- click.altやshiftでAlt + Shift クリックで発火

```html
  <button @click.alt="toggleModal">open modal</button>
```
- click.selfで、子要素の中では発火しなくする. 子要素以外の自分の領域のみで発火

```html
    <div class="backdrop" @click.self="closeModal">
        <div class="modal" :class="{ sale: theme === 'sale' }">
```


- template と slot


    - 名前なしslot
`App.vue`

```vue
<template>
    <Modal :header="header" :text="text" theme="sale" @close="toggleModal" >    # Modalはこのようにタグで書ける.
        <h1> Sign up for the Giveaway </h1>
        <p>Grab your ninja</p>
    </Modal>
</template>
```
`Modal.vue`

```vue
<template>
    <div class="backdrop" @click.self="closeModal">
        <div class="modal" :class="{ sale: theme === 'sale' }">
            <slot></slot>   # Modal側でslotを書けば、上のh1とpがそのまま入る
        </div>
    </div>
</template>
```


- 名前ありslot
`App.vue`

```vue
<template>
    <Modal :header="header" :text="text" theme="sale" @close="toggleModal" >
        <template v-slot:links>     # v-slot:[名前]でtemplateタグを作る
            <a href="#">sigh up</a>
            <a href="#">more info</a>
        </template>
        <h1> Sign up for the Giveaway </h1>
        <p>Grab your ninja</p>
    </Modal>
</template>
```


`Modal.vue`

```vue
<template>
    <div class="backdrop" @click.self="closeModal">
        <div class="modal" :class="{ sale: theme === 'sale' }">
            <div class="actions">
                <slot name="links"></slot>   # 上で定義したlinksを書けばココにaタグが入る
            </div>
        </div>
    </div>
</template>
```


- componentsの再利用


2つのModalを使いたい場合、Modal.vueを2つ作る必要はない  
以下で、Modal.vueは一つで別々のModalのような振る舞いが可能


`App.vue`

```vue
<template>
  <div v-if="showModal">
    <Modal :header="header" :text="text" theme="sale" @close="toggleModal" >
        <h1> Sign up for the Giveaway </h1>
        <p>Grab your ninja</p>
    </Modal>
  </div>


  <div v-if="showModalTwo">     # ...2つ目のdivを追加する. v-ifでは違う変数. close=も違う変数
    <Modal @close="toggleModalTwo" >
        <h1> Sign up the newsletter </h1>
        <p>For updates and promo codes!</p>
    </Modal>
  </div>
  
  <button @click="toggleModal">open modal</button>
  <button @click="toggleModalTwo">open modal</button>       # ...2つ目のModalを起動するボタンを用意
</template>


<script>
import Modal from './components/Modal.vue'


export default {
  name: 'App',
  data() {
    return {
      title : 'My First Vue App',
      showModal: false, 
      showModalTwo: false,      # ...2つ目の変数
    }
  },
  components:{ Modal },
  methods :{
    handleClick() {
      this.$refs.name.focus()
    },
    toggleModal() {
      this.showModal = !this.showModal
    },
    toggleModalTwo() {          # ...表示切替の関数
      this.showModalTwo = !this.showModalTwo
    }
  }
}
</script>
```




- teleportタグ


index.htmlのappではない場所にModalを入れることができる


`App.vue`

```vue
<template>
  <h1>{{ title }}</h1>
  <p>Welcome ...</p>
  <div v-if="showModal">
    <Modal :header="header" :text="text" theme="sale" @close="toggleModal" >
...
    </Modal>
  </div>


  <teleport to=".modals" v-if="showModalTwo">       # ...divではなく teleport to=で行き先のクラス名やidを指定する（idなら#）
    <Modal @close="toggleModalTwo" >
        <h1> Sign up the newsletter </h1>
        <p>For updates and promo codes!</p>
    </Modal>
  </teleport>
  <button @click="toggleModal">open modal</button>
  <button @click="toggleModalTwo">open modal</button>
</template>
```


`index.html`

```html
  <body>
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <div class="modals"></div>      # ココに追加した
    <!-- built files will be auto injected -->
  </body>
```


## Sample


### Reaction Timer


- `App.vue`

```vue
<template>
<h1>Ninja Reaction Timer</h1>
<button @click="start" :disabled="isPlaying">play</button>    # startボタン、isPlayingがTrueならdisabled
<Block v-if="isPlaying" :delay="delay" @end="endGame" />  # isPlayingがTrueなら表示. delayを渡す. Block側のendをemitで受け取ってendGame 
<Results v-if="showResults" :score="score" />   # showResultsがTrueなら表示. scoreを渡す
</template>


<script>
import Block from './components/Block.vue'
import Results from './components/Results.vue'


export default {
  name: 'App',
  components: { Block, Results },
  data() {
    return {
      isPlaying : false,
      delay: null,
      score: null,
      showResults: false
    }
  },
  methods: {
    start() {
      this.delay = 2000 + Math.random() * 5000
      this.isPlaying = true
      this.showResults = false
    },
    endGame(reactionTime) {
        this.score = reactionTime
        this.isPlaying = false
        this.showResults = true
    }
  }
}
</script>


<style>
...
</style>
```


`Block.vue`

```vue
<template>
  <div class="block" v-if="showBlock" @click="stopTimer">click me</div> # showBlockがTrueなら表示. stopTimerイベント発火
</template>


<script>
export default {
    props: ['delay'],   # Appから受け取るパラメータ
    data() {
        return {
            showBlock: false,
            timer: null,
            reactionTime: 0
        }
    },
    mounted() {     # 呼び出されて、表示する前に発生するmoutedイベント
        setTimeout(() => {
            this.showBlock = true
            this.startTimer()   # startTimer呼び出し
        }, this.delay)
    }, 
    methods: {
        startTimer() {        # timer変数は、setInterval()というファンクション
            this.timer = setInterval(() => {
                this.reactionTime += 10
            }, 10)
        },
        stopTimer() {
            clearInterval(this.timer)   # clearIntervalでtimerをクリア
            this.$emit('end', this.reactionTime)
        }
    }
}
</script>


<style>
...
</style>
```


`Results.vue`

```vue
<template>
  <p class="results">Result Time: {{ score }} ms</p>    # scoreの表示
  <p class="rank">{{ rank }}</p>    # rankの表示
</template>


<script>
export default {
    props: ['score'],     # App.vueから受け取るパラメータscore
    data (){
        return {
            rank: null
        }
    },
    mounted() {     # 画面表示する内容をmountedに入れる
        if (this.score < 250) {
            this.rank = 'Excelent'
        } else if (this.score < 400) {
            this.rank = 'Rapid'
        } else {
            this.rank = 'Normal'
        }
    }


}
</script>


<style>
...
</style>
```


### SignupForm


`App.vue`

```vue
<template>
  <SignupForm />
</template>


<script>
import SignupForm from './components/SignupForm.vue'
export default {
  name: 'App',
  components: { SignupForm  }
}
</script>


<style>
...
</style>
```


`SignupForm.vue`

```vue
<template>
  <form @submit.prevent="handleSubmit">   # submit.preventで、本来のsubmitを抑止する
      <label>Emal</label>
      <input type="email" required v-model="email">   # v-model=でdataの変数と紐付け


      <label>Password</label>
      <input type="password" required v-model="password">
      <div v-if="passwordError" class="error">{{ passwordError }}</div>   # バリデーション時のメッセージはこのように書ける


      <label>Role</label>
      <select v-model="role">
          <option value="developer">Web Developer</option>
          <option value="designer">Web Designer</option>
      </select>


      <label>Skills</label>
      <input type="text" v-model="tempSkill" @keyup.alt="addSkill">   # Alt+カンマで下にスキルを表示させるハック
      <div v-for="skill in skills" :key="skill" class="pill">
          <p @click="deleteSkill(skill)">{{ skill }}</p>    # クリックしたら消せるハック
      </div>


      <div class="terms">
          <input type="checkbox" required v-model="terms">
          <label>Accept terms and condition</label>
      </div>


      <div class="submit">
          <button>Create an Account</button>
      </div>




  </form>
</template>


<script>
export default {
    data() {
        return {
            email: '',
            password: '',
            role : 'designer',
            terms: false,
            // names : []
            tempSkill:'',
            skills : [],
            passwordError: ''
        }
    },
    methods: {
        addSkill(e) {
            if (e.key ===',' && this.tempSkill) {
                if (!this.skills.includes(this.tempSkill)) {
                    this.skills.push(this.tempSkill)
                }
                this.tempSkill = ''
            }
        },
        deleteSkill(skill) {
            this.skills = this.skills.filter((item) => {    # 配列のfilterの書き方. 難しい. filterの中に条件のファンクションを書く
                return skill !== item
            })
        },
        handleSubmit() {
            this.passwordError = this.password.length > 5 ? '' : 'パスワードは6文字以上を入力してください'
        }
    }


}
</script>


<style>
...
</style>
```


### Router Basic & Fetch Data


SPAではなく、いくつかのページに遷移するアプリケーションをRouterで作る

```bash
vue create
```
でプロジェクトを作成するときに、[Router]にチェックを入れる


- フォルダ構成

```
router
    └─ index.js
views
    ├─ jobs
    │    ├─ Jobs.vue
    │    └─ JobDetails.vue
    ├─ About.vue
    ├─ Home.vue
    └─ NotFound.vue
App.vue
```




- `index.js`

```javascript
import { createRouter, createWebHistory } from 'vue-router'
import Home from '../views/Home.vue'       # import 定義
import About from '../views/About.vue'
import NotFound from '../views/NotFound.vue'
import Jobs from '../views/jobs/Jobs.vue'
import JobDetails from '../views/jobs/JobDetails.vue'


const routes = [        # path と name と componentを指定. name が App.vueのname に相当
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/about',
    name: 'About',
    component: About
  },
  {
    path: '/jobs',
    name: 'Jobs',
    component: Jobs
  },
  {
    path: '/jobs/:id',
    name: 'JobDetails',
    component: JobDetails,
    props: true         # propsパラメータを使うときに指定する
  },
  {
    path: '/all-jobs',      # リダイレクトの書き方
    redirect: '/jobs'
  },
  {
    path: '/:catchAll(.*)',             # 定義以外のパスが指定されたときの404指定
    name: 'NotFound',
    component: NotFound
  }
]
```


- `App.vue`

```vue
<template>
  <div id="nav">
    <router-link to="/">Home</router-link>          # router-link toでページ指定      
    <router-link :to="{ name :'About' }">About</router-link>        # パスを指定してもいいし、index.jsで指定したname指定でもよい
    <router-link :to="{ name :'Jobs' }">Jobs</router-link>
  </div>
  <button @click="redirect">Redirect</button>
  <button @click="back">Go back</button>
  <button @click="forward">Go forward</button>
  
  <router-view/>        # Routerのときにこれが入る
</template>


<script>
export default {
  methods: {
    redirect() {
      this.$router.push({ name: 'Home' })       # $routerを使ってリダイレクトの指定
    },
    back() {
      this.$router.go(-1)       # $route.goでページ履歴に移動できる
    },
    forward() {
      this.$router.go(1)
    }
  },
}
</script>
```


- `NotFound.vue`

```vue
<template>
  <h2>404</h2>
  <h3>Page not found</h3>
</template>
```


- `Jobs.vue`

```vue
<template>
  <h1>Jobs</h1>
  <div v-for="job in jobs" :key="job.id" class='job'>   # v-forで 繰り返しdivを生成する. :key=はuniqueな値を指定する
      <router-link :to="{ name: 'JobDetails', params:{ id: job.id } }">     # 渡すパラメータを指定
          <h2>{{ job.title }}</h2>
      </router-link>
  </div>
</template>


<script>
export default {
    data() {
        return {        // データがjobsだという指定
            jobs : [
                // {title: 'Ninja Ux Designer', id:1, details:'lorem'},     // 直書きした場合
                // {title: 'Ninja Web Designer', id:2, details:'lorem'},
                // {title: 'Ninja Vue Designer', id:3, details:'lorem'},
            ]
        }
    },
    mounted() {
        fetch(' http://localhost:3000/jobs')        // APIでjsonをとってくる場合の指定
            .then(res => res.json())
            .then(data => this.jobs = data)     # jobsにレスポンスjsonを入れる
            .catch(err => console.log(err.message))
    }
}
</script>
```


- `JobDetails.vue`

```vue
<template>
    <div v-if="job">        # jobがとれたら表示、という意味でv-ifが必要. ないと常に非表示になってしまう
        <h1>Job Detals Page</h1>
        <h2>{{ job.title }} </h2>
        <p>The job id is {{ id }}</p>
    </div>
    <div v-else>
        <p>job details loading ...</p>      # jobが取れるまではメッセージ表示
    </div>
</template>


<script>
export default {
    props: ['id'],      # job.vueから渡ってくるパラメータを定義
    // data() {
    //     return {
    //         id : this.$route.params.id
    //     }
    // }
    data() {
        return {
            job: null       // データのjobを定義
        }
    },
    mounted() {
        fetch(' http://localhost:3000/jobs/' + this.id)     // job.vueから受け取ったidでjobの引き直し
            .then(res => res.json())
            .then(data => this.job = data)
            .catch(err => console.log(err.message))
    }


}
</script>
```


#### json-server


- インストール

```bash
npm install -g json-server
```
-gはグローバルインストール. パスを通すらしい


- dataファイル
このプロジェクト内でテスト的にやるなら
`data/db.json`

```json
{
    "jobs": [
        {"title": "Ninja Ux Designer", "id":1, "details":"lorem"},
        {"title": "Ninja Web Designer", "id":2, "details":"lorem"},
        {"title": "Ninja Vue Designer", "id":3, "details":"lorem"}
    ]
}
```


を作って

```bash
json-server --watch data\db.json
```
とすると、json-serverが起動できる


`http://localhost:3000/jobs`
 が起動する.
 自動的にjsonファイルの"jobs"がslugに入る




上でみたようにvueの中でエンドポイント指定は以下のようにする


```javascript
<script>
export default {
    mounted() {
        fetch(' http://localhost:3000/jobs')        // APIでjsonをとってくる場合の指定
            .then(res => res.json())
            .then(data => this.jobs = data)     # jobsにレスポンスjsonを入れる
            .catch(err => console.log(err.message))
    }
}
</script>
```




### Project Planner


- CRUDのあるフォームと一覧
- router Project


- フォルダ構成

```
components
    ├─ FilterNav.vue
    ├─ Navbar.vue
    └─ SingleProject.vue
router
    └─ index.js
views
    ├─ AddProject.vue
    ├─ EditProject.vue
    ├─ Home.vue
    └─ NotFound.vue
App.vue
```


- `App.vue`

```vue
<template>
  <Navbar />    # ヘッダーのナビゲーションバーのcomponentsで定義する
  <router-view/>    # その下はrouter
</template>


<script>
import Navbar from './components/Navbar.vue'
export default {
  components: { Navbar }
}
</script>
```


- `Navbar.vue`

```vue
<template>
  <nav class="main-nav">
      <router-link :to="{ name: 'Home' }">Projects</router-link>      # HomeとAddの２つのメニュー
      <router-link :to="{ name: 'AddProject' }">Add a new Project</router-link>
  </nav>
</template>


<script>
export default {


}
</script>
```


- `Home.vue`

```vue
<template>
  <div class="home">
    <FilterNav @filterChange="current = $event" :current="current" />   # 2段目のフィルターナビゲーションもcomponents. filterChangeはFilterNavからのemitだが、$eventとする意味が理解できない. all/completed/ongoingの文字列を受け取るだけなのに、なぜ$eventとしなければいけないのか...
    <div v-if="projects.length">
      <div v-for="project in filteredProjects" :key="project.id">   # filteredProjectsはcurrentの値によってprojectをフィルターする
        <SingleProject :project="project" @delete="handleDelete" @complete="handleComplete" />  # 明細もcomponents. よって、Home.vueにはstyle定義がない. delete / completeは、SingleProject コンポーネントからのemit
      </div>
    </div>
  </div>
</template>


<script>
import SingleProject from '../components/SingleProject.vue'
import FilterNav from '../components/FilterNav.vue'


export default {
  name: 'Home',
  components: { SingleProject, FilterNav },
  data() {
    return {
      projects: [],
      current: 'all'
    }
  },
  mounted() {
    fetch('http://localhost:3000/projects')   // mountするときにすべてのprojectを取得する
    .then(res => res.json())
    .then(data => this.projects = data)
    .catch(err => console.log(err.message))
  },
  methods: {
    handleDelete(id) {    # SingleProjectコンポーネントから削除されたらdataをアップデート. completeも同様.
      this.projects = this.projects.filter((project) => project.id !== id)
    },
    handleComplete(id) {
      let p = this.projects.find(project => { return project.id === id })
      p.complete = !p.complete
    }
  },
  computed: {
    filteredProjects() {
      if (this.current === 'completed') {
        return this.projects.filter(project => project.complete)
      }
      if (this.current === 'ongoing') {
        return this.projects.filter(project => !project.complete)
      }
      return this.projects
    }
  }
}
</script>
```


- `FilterNav.vue`

```vue
<template>
    <nav class="filter-nav"> 
        <button @click="updateFilter('all')" :class="{ active: current === 'all' }">View All</button>   # clickされたらemit発動. classはcurrentの値によって表示を変える
        <button @click="updateFilter('completed')" :class="{ active: current === 'completed' }">Completed</button>
        <button @click="updateFilter('ongoing')" :class="{ active: current === 'ongoing' }">Ongoing</button>
    </nav>
</template>


<script>
export default {
    props: ['current'], 
    methods: {
        updateFilter(by) {
            this.$emit('filterChange', by)
        }
    }
}
</script>
```




- `SingleProject.vue`

```vue
<template>
  <div class="project" :class="{ complete : project .complete }"> # completeの値によってclassを変更. 表示を変える
      <div class="actions" >
          <h3 @click="changeToggle">{{ project.title }}</h3>    # clickイベントで詳細の表示/非表示を切替
          <div class="icons">
            <router-link :to="{ name: 'EditProject',  params : { id: project.id } }">   # editは、別ページに遷移するのでrouter-link/spanという構成. EditProjectにproject.idを渡す
                <span class="material-icons">edit</span>              # spanはgoogleのmaterial iconsを使用
            </router-link>
            <span class="material-icons" @click="deleteProject">delete</span>          # deleteでdataを更新
            <span class="material-icons tick" @click="toggleComplete">done</span>      # doneでcompleteを更新        
          </div>
      </div>
      <div v-if="showDetail" class="details"> # h3 clickイベントのchangeToggleで表示/非表示を切替
          <p>{{ project.details }}</p>
      </div>
  </div>
</template>


<script>
export default {
    props: ['project'],
    data() {
        return {
            showDetail: false,
            url:'http://localhost:3000/projects/' + this.project.id
        }
    },
    methods: {
        changeToggle() {
            this.showDetail = !this.showDetail
        },
        deleteProject() {
            fetch(this.url, {method : 'DELETE'})    # json.serverはmethod:'DELETE'で削除できる. 同様に'PATCH'で更新
                .then(() => this.$emit('delete', this.project.id))    # 削除したらHome.vue側で表示を変えるためemit
                .catch(err => console.log(err.message))
        },
        toggleComplete() {
            fetch(this.url, {
                method : 'PATCH', 
                headers : { 'Content-Type': 'application/json'},
                body : JSON.stringify({ complete : !this.project.complete })
            }).then(() => {
                this.$emit('complete', this.project.id)
            }).catch(err => consloe.log(err.message))
        }
    }
}
</script>
```


- `AddProject.vue`

```vue
<template>
  <form @submit.prevent="handleSubmit">   # 追加フォーム. submit.preventでformのクリックイベントを独自関数にする
      <label>Title:</label>
      <input type="text" v-model="title" required>
      <label>Details:</label>
      <textarea v-model="details" required></textarea>
      <button>Add Project</button>
  </form>
</template>


<script>
export default {
    data() {
        return {
            title: '',
            details: '',
            url:'http://localhost:3000/projects/' 
        }
    },
    methods: {
        handleSubmit() {
            let project = {
                title: this.title,
                details: this.details,
                complete : false
            }
            fetch(this.url, {   # json.serverで追加はmethod:'POST'
                method : 'POST', 
                headers : { 'Content-Type': 'application/json'},
                body : JSON.stringify(project )
            })
            .then(() => {
                this.$router.push('/')
            }).catch(err => consloe.log(err.message))
        }
    }
}
</script>
```
EditProject.vueもAddProjectと同様なので省略


- `index.html`
googleマテリアルアイコンは以下。

```html
  <head>
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons"
      rel="stylesheet">
  </head>
```




### dojo-blog


- setupの使い方＝composition API
  これまでのscriptタグの中の、data, methods, computedを書いているやり方は、options APIという
  setupにまとめて書くやり方をcomposition APIという 
- composablesでdata get の使い方
- SpinnerでCSSでサークルの使い方


- `Home.vue`

```vue
<template>
  <div class="home">
    <h1>Home</h1>
    <div v-if="error">{{ error }}</div>
    <div v-if="posts.length" class="layout">
      <PostList v-if="showPosts" :posts="posts"/>
      <TagCloud :posts="posts" />
    </div>
    <div v-else>
      <Spinner />
    </div>
  </div>
</template>


<script>
import PostList from '../components/PostList.vue'
import getPosts from '../composables/getPosts'
import { ref } from 'vue'   # ...変数に固定値を入れるときにrefを使う
import Spinner from '../components/Spinner.vue'
import TagCloud from '../components/TagCloud.vue'


export default {
  name: 'Home',
  components: {PostList, Spinner, TagCloud},
  setup() {   # ...setup()の中に変数、computedの関数などを書く


    const {posts , error, load} = getPosts()    # data getの関数をjsに外だししていて、その中でデータ取得を行っている


    load()
    const showPosts = ref(true)   # ...変数に固定値を入れるときにrefを使う
    return { posts, showPosts, error}   # ...returnを忘れない
  }
}
</script>
```


- `Tags.vue`

```vue
<template>
  <div class="home">
    <h1>Tags</h1>
    <div v-if="error">{{ error }}</div>
    <div v-if="posts.length" class="layout">
      <PostList v-if="showPosts" :posts="post_tags"/>
      <TagCloud :posts="posts" />
    </div>
    <div v-else>
      <Spinner />
    </div>
  </div>
</template>


<script>
import PostList from '../components/PostList.vue'
import getPosts from '../composables/getPosts'
import { computed, ref } from 'vue'     # ...computedも参照が必要
import Spinner from '../components/Spinner.vue'
import { useRoute } from 'vue-router' # ...このvueでは/tags/tagというurlパラメータがあるのでそれの取得のためのuseRoute
import TagCloud from '../components/TagCloud.vue'


export default {
  name: 'Tags',
  components: {PostList, Spinner, TagCloud},
  setup() {
    const route = useRoute()    # ...useRoute


    const {posts , error, load} = getPosts()


    load()


    const post_tags = computed(() => {    # ...関数はcomputed()の中に書く
      return posts.value.filter((post) => post.tags.includes(route.params.tag))   # useRoute.params.tagでurlパラメータを取得
    })
    
    const showPosts = ref(true)
    return { posts, post_tags, showPosts, error}
  }
}
</script>
```


- `getPosts.js`

```javascript
import { ref } from 'vue'   # ...html側に返す変数はrefが必要


const getPosts = () => {
    const posts = ref([])
    const error = ref(null)


    const load = async ()  => {
      try {
        await new Promise(resolve => {    # ...1秒ウェイトしている
          setTimeout(resolve, 1000)
        })


        let data = await fetch('http://localhost:3000/posts')   # ...awaitで同期してfetch
        if (!data.ok) {
          throw Error('no data available')
        }
        posts.value = await data.json()   # ...dataのjson化
      }
      catch (err) {
          error.value = err.message
      }
    }


    return { posts, error, load}

}


}

export default getPosts   #...export defaultが必要
```


- `PostList.vue`

```vue
<template>
  <div class="post-list">
      <div v-for="post in posts" :key="post.id">
          <SinglePost :post="post" />
      </div>
  </div>
</template>


<script>
import { onMounted,onUnmounted, onUpdated } from '@vue/runtime-core'
import SinglePost from '../components/SinglePost.vue'


export default {
    props: ['posts'],
    components: {SinglePost},
    setup(props) {
        onMounted(() => console.log('component mouted'))    # ...onMounted/onUnmounted/onUpdateイベントを拾える
        onUnmounted(() => console.log('component onUnmounted'))
        onUpdated(() => console.log('component onUpdated'))
        
    }
}
</script>
```
- `Spinner.vue`

```vue
<template>
  <div class="spin"></div>
</template>


<style>
    .spin {
        display: block;
        width: 40px;
        height: 40px;
        margin: 30px auto;
        border: 3px solid transparent;
        border-radius: 50%;
        border-top-color: #ff8800;
        animation: spin 1s ease infinite;
    }


    @keyframes spin {
        to { transform: rotateZ(360deg);}
    }
</style>
```


### dojo-blog firebase


#### firebase


- npm インストール

```bash
npm install firebase
```


- connect setting
  - `src/firebase/config.js`にfirebaseのプロジェクトの設定のSDKの設定と構成のnpmの部分をコピーして貼り付ける

```javascript
import firebase from 'firebase/app'
import 'firebase/firestore'


const firebaseConfig = {    # ...ココが接続文字列. 貼り付ける部分
    apiKey: "REDACTED_API_KEY",
    authDomain: "udemy-vue-firebase-sites-76f19.firebaseapp.com",
    projectId: "udemy-vue-firebase-sites-76f19",
    storageBucket: "udemy-vue-firebase-sites-76f19.appspot.com",
    messagingSenderId: "850060333893",
    appId: "1:850060333893:web:ff9b97e51485c634f3242c"
};


firebase.initializeApp(firebaseConfig)


const projectFirestore = firebase.firestore()


export {projectFirestore}
```


#### dojo-blogからfirebaseにして変更した箇所


- `getPost.js`
  - firebaseでの複数レコード取得

```javascript
import { ref } from 'vue'
import { projectFirestore  } from '../firebase/config'    # ...import


const getPosts = () => {
    const posts = ref([])
    const error = ref(null)


    const load = async ()  => {
      try {
        const res = await projectFirestore.collection('posts')  # ...postsのget
          .orderBy('createAt', 'desc')    # ...orderByの第2引数がasc/desc
          .get()


        posts.value = res.docs.map(doc => {   # ...map関数でres.docsをすべて展開
          return { ...doc.data(), id:doc.id}    # ..."..."は続くオブジェクトのリスト展開を表す.  doc.dataにはidが含まれていないので、元のpostsの定義に合わせてid＝firebase側のid（pk）をセットする
        })
      }
      catch (err) {
          error.value = err.message
      }
    }


    return { posts, error, load}

}


}

export default getPosts
```


- `getPost.js`
  - firebaseの1件取得

```javascript
import { ref } from 'vue'
import { projectFirestore } from '../firebase/config'


const getPost = (id) => {
    const post = ref(null)
    const error = ref(null)


    const load = async ()  => {
      try {


        await new Promise(resolve => {
          setTimeout(resolve, 1000)
        })


        let res = await projectFirestore.collection('posts').doc(id).get()    # ... get()メソッド
        if (!res.exists) {
          throw Error('That post does not exists')
        }


        post.value = {...res.data(), id: res.id}


      }
      catch (err) {
          error.value = err.message
      }
    }


    return { post, error, load}

}


}

export default getPost
```


- `Create.vue`

```vue
<template>
  ...
</template>


<script>
import { ref } from 'vue'
import { useRouter} from 'vue-router'
import { projectFirestore, timestamp } from '../firebase/config'


export default {
    setup() {
        const title = ref('')
        const body = ref('')
        const tag = ref('')
        const tags = ref([])


        const router = useRouter()


        const handleKeydown = () => {
            if(!tags.value.includes(tag.value)) {
                tag.value = tag.value.replace(/\s/, '')
                tags.value.push(tag.value)
            }
            tag.value = ''
        }


        const handleSubmit = async () => {
            const post = {
                title : title.value,
                body : body.value,
                tags : tags.value,
                createAt : timestamp()    # ...項目を定義するだけで初期値は現在日時が設定される
            }


            let res = await projectFirestore.collection('posts').add(post)    # ...json-serverのときから変えたのはココだけ. addでfirebase側にオブジェクト定義がなくても入る


            router.push({ name : 'Home'})
        }


        return {title, body, tag, handleKeydown, tags, handleSubmit}
    }
}
</script>


<style>
```


- `Details.vue`

```vue
<template>
  ...
</template>


<script>
import getPost from '../composables/getPost'
import Spinner from '../components/Spinner.vue'
import { useRoute, useRouter } from 'vue-router'
import { projectFirestore } from '../firebase/config'


export default {
    props:['id'],
    components:{ Spinner },
    setup(props) {
        const route = useRoute()
        const router = useRouter()
        // const {post , error, load} = getPost(props.id)
        const {post , error, load} = getPost(route.params.id)


        load()


        const handleClick = async () => {
            await projectFirestore.collection('posts')    # firebaseのdelete
                .doc(props.id)
                .delete()
            router.push({ name : 'Home' })
        }
        return {post, error, handleClick}
    }
}
</script>


<style>
```


#### Realtime反映


- onSnapshotでリアルタイムにデータの変更をリッスンできる例


- `RealTime.vue`

```vue
<template>
  <h1>Real Time data</h1>
    <div v-for="post in posts" :key="post.id">
            <h3>{{ post.title }}</h3>
    </div>
</template>


<script>
import { ref } from 'vue'
import { projectFirestore } from '../firebase/config'
export default {
    setup() {
        const posts = ref([])


        projectFirestore.collection('posts')
            .orderBy('createAt', 'desc')
            .onSnapshot((snap) => {     # ...onSnapshotで監視の開始みたいなこと
                let docs = snap.docs.map(doc => {   # 変更があったときのコールバックを指定する. ココではデータを取得して返す例
                    return {...doc.data(), id: doc.id }
                })
                posts.value = docs
            })


        return {posts}
    }
}
</script>


<style>
...
</style>
```


### Live-Chat


#### ログイン/サインアップの実装


- Welcome.vue

```vue
<template>
  <div class="welcome container">
    <p>Welcome</p>
    <div v-if="showLogin">    # ...1枚のvueでログインフォームとサインアップフォームの切り替え
      <h2>Login</h2>
      <LoginForm @login="enterCaht" />    # ...どちらも次はチャットルームページ
      <p>No account yet? <span @click="showLogin = false">Signup</span> instead</p>
    </div>
    <div v-else>
      <h2>Sign up</h2>
      <SignupForm @signup="enterCaht" />
      <p>Already registered? <span @click="showLogin = true">Login</span> instead</p>
    </div>
  </div>
</template>


<script>
import SignupForm from '../components/SignupForm.vue'
import LoginForm from '../components/LoginForm.vue'
import { ref } from 'vue'
import { useRouter } from 'vue-router';
export default {
  components: { SignupForm, LoginForm },
  setup() {
    const showLogin = ref(true)
    const router = useRouter()


    const enterCaht = () => {
        router.push({ name: 'Chatroom'})    # chatroomへのリダイレクト
    }
    return { showLogin, enterCaht }
  }
}
</script>
```


- LoginForm.vue

```vue
<template>
  <form @submit.prevent="handleSubmit">
      <input type="email" required placeholder="email" v-model="email">
      <input type="password" required placeholder="password" v-model="password">
      <div class="error">{{ error }}</div>
      <button>Log in</button>
  </form>
</template>


<script>
import { ref } from 'vue'
import useLogin from '../composables/useLogin';
export default {
    setup(props, context) {
        const email = ref('')
        const password = ref('')


        const { error, login } = useLogin()


        const handleSubmit = async () => {
            await login(email.value, password.value)    # useLogin.jsを呼び出すだけ
            if (!error.value){
                context.emit('login')
            }
        }


        return { email , password, handleSubmit, error }
    }
}
</script>
```
- useLogin.js

```javascript
import { ref } from 'vue'
import { projectAuth } from '../firebase/config'    # configの中でfirebase.auth()をexport


const error = ref(null)


const login = async (email, password) => {
    error.value = null


    try {
        const res = await projectAuth.signInWithEmailAndPassword(email, password)   # ...firebaseでメールアドレス/パスワードのログイン認証
        error.value = null
        console.log(res);
        return res
    } catch (err) {
        console.log(err.message);
        error.value = 'Incorrect login credentials'
    }

}


}

const useLogin = () => {


    return {error, login}
}


export default useLogin
```


- SignupForm.vue

```vue
<template>
  <form @submit.prevent="handleSubmit">
      <input type="text" required placeholder="display name" v-model="displayName">
      <input type="email" required placeholder="email" v-model="email">
      <input type="password" required placeholder="password" v-model="password">
      <div class="error">{{ error }}</div>
      <button>Sign up</button>
  </form>
</template>


<script>
import { ref } from 'vue'
import useSignup from '../composables/useSignup.js';


export default {
    setup(props, context) {
        const { error, signup } = useSignup()


        const displayName = ref('')
        const email = ref('')
        const password = ref('')


        const handleSubmit = async () => {
            await signup(email.value, password.value, displayName.value)    # ...こっちもuseSignupを呼ぶだけ
            if (!error.value){
                context.emit('signup')
            }
        }


        return { displayName, email , password, handleSubmit, error }
    }
}
</script>
```


- useSignup.js

```javascript
import { ref } from 'vue'
import { projectAuth } from '../firebase/config'


const error = ref(null)


const signup = async (email, password, displayName) => {
    error.value = null


    try {
        const res = await projectAuth.createUserWithEmailAndPassword(email, password)   # ...メールアドレス/パスワードでユーザー作成
        if (!res){
            throw new Error('Could not complete the signup')
        }
        await res.user.updateProfile( { displayName })  # ...ユーザー作成後にdisplyNameを更新
        error.value = null


        return res
    } catch (err) {
        console.log(err.message)
        error.value = err.message
    }
}


const useSignup = () => {


    return {error, signup}
}


export default useSignup
```


- Navbar.vue

```vue
<template>
  <nav v-if="user">
      <div>
          <p>name : {{ user.displayName }}</p>
          <p class="email">logged in as {{ user.email }}</p>
      </div>
      <button @click="handleClick">Logout</button>
  </nav>
</template>


<script>
import useLogout from '../composables/useLogout.js';
import getUser from '../composables/getUser.js';


export default {
    setup(props, context) {
        const { logout, error} = useLogout()
        const { user } = getUser()


        const handleClick = async () => {
            await logout()    # ...ログアウトはuseLogout.jsを呼ぶだけ
            if (! error.value) {
                // console.log('user logged out');
                context.emit('logout')    # ...このemitはこのコースのチャレンジで自分が考えた案. Chatroom.vueの中のwatchがあれば要らない
            }
        }


        return { handleClick, user }
    }
}
</script>


<style>
    nav {
        padding: 20px;
        border-bottom: 1px solid #eee;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }
    nav p {
        margin: 2px auto;
        font-size: 16px;
        color: #444;
    }
    nav p.email {
        font-size: 14px;
        color: #999;
    }


</style>
```


- router/index.js

```javascript
import { createRouter, createWebHistory } from 'vue-router'
import Welcome from '../views/Welcome.vue'
import Chatroom from '../views/Chatroom.vue'
import { projectAuth } from '../firebase/config';


const requireAuth = (to, from, next) => {   # ...下のbeforeEnterで、ログインが必要なページに定義する関数.　nextでリダイレクト先を指定. 
  let user = projectAuth.currentUser
  if (!user) {
    next({name: 'Welcome'})
  } else {
    next()
  }
}
const requireNoAuth = (to, from, next) => {   # ...こちらはログインが要らない（ログイン済みなら認証ページをスキップさせるために使用）
  let user = projectAuth.currentUser
  if (user) {
    next({name: 'Chatroom'})
  } else {
    next()
  }
}


const routes = [
  {
    path:'/',
    name: 'Welcome',
    component: Welcome,
    beforeEnter: requireNoAuth
  },
  {
    path:'/chatroom',
    name: 'Chatroom',
    component: Chatroom,
    beforeEnter: requireAuth
  }
]


const router = createRouter({
  history: createWebHistory(process.env.BASE_URL),
  routes
})


export default router
```
- main.js

```javascript
import { projectAuth } from './firebase/config';


let app     # ...上記のままでは画面がリフレッシュされるたびにログアウトしてしまうので、onAuthStateChangedで
appをマウントするよう変更する


projectAuth.onAuthStateChanged( () => {
    if (!app) {
        app = createApp(App).use(router).mount('#app')
    }
})
```

#### リアルタイムチャットの実装

- Chatroom.vue

```vue
<template>
    <div class="container">
      <Navbar @logout="welcome" />
      <ChatWindow />
      <NewChatForm />
    </div>
</template>


<script>
import Navbar from '../components/Navbar.vue';
import NewChatForm from '../components/NewChatForm.vue';
import ChatWindow from '../components/ChatWindow.vue';
import { useRouter } from 'vue-router';
import getUser from '../composables/getUser';
import { watch } from '@vue/runtime-core';


export default {
    components: { Navbar, NewChatForm, ChatWindow },
    setup() {
        const { user } = getUser()
        const router = useRouter()


        watch(user, () => {   # ...userがログイン状態か監視して、いなくなったらサインアップページに飛ばす
            if (!user.value) {
                console.log('watch');
                router.push({ name: 'Welcome'})
            }
        })


        const welcome = () => {   # ...ログアウトボタンからのemit。watchがあれば不要
            console.log('emit');
            router.push({ name: 'Welcome'})
        }
        return { welcome }
    }
}
</script>
```

- NewChatForm.vue

```vue
<template>
  <form>
      <textarea
        placeholder="Type a message and hit enter to send ..."
        v-model="message"
        @keypress.enter.prevent="handleSubmit"
      ></textarea>
      <div class="error">{{ error }}</div>
  </form>
</template>


<script>
import { ref } from 'vue';
import getUser from '../composables/getUser';
import useCollection from '../composables/useCollection';
import { timestamp } from '../firebase/config';
export default {
    setup() {
        const message = ref('')
        const { user } = getUser()
        const { error, addDoc} = useCollection('messages')

        const handleSubmit = async () => {
            const chat = {
                name : user.value.displayName,
                message : message.value,
                createAt : timestamp()    # ...タイムスタンプを使うことでfirebase側で自動でタイムスタンプを設定
            }
            
            await addDoc(chat)    # ...useCollection.jsの中のaddDocを呼んでメッセージを追加する
            if (!error.value) {
                message.value = ''
            }
        }
        return { message,handleSubmit, error}
    }
}
</script>
```


- useCollection.js

```javascript
import { ref } from 'vue'
import { projectAuth, projectFirestore } from '../firebase/config'

const userCollection  = (collection) => {
    const error = ref(null)

    const addDoc = async (doc) => {
        error.value = null
        
        try {
            await projectFirestore.collection(collection).add(doc)    # ...データの追加
        } catch (err) {
            console.log(err.message);
            error.value = 'could not send the message'
        }
    }
    return {error, addDoc}
}
export default userCollection
```


- ChatWindow.vue

```vue
<template>
  <div class="chat-window">
      <div v-if="error">{{ error }} </div>
      <div v-if="documents" class="messages" ref="messages">
          <div v-for="doc in formattedDocuments" :key="doc.id" class="single">
              <span class="created-at">{{ doc.createAt }}</span>
              <span class="name">{{ doc.name }}</span>
              <span class="message">{{ doc.message }}</span>
          </div>
      </div>
  </div>
</template>


<script>
import { computed, onUpdated, ref } from 'vue'
import { formatDistanceToNow } from 'date-fns';   # ... SNSでありがちの N分前、N日前の表示にするライブラリ
import getCollection from '../composables/getCollection'
export default {
    setup() {
        const { documents, error } = getCollection('messages')    # getCollectionの中ですべてのmessagesを取得

        const formattedDocuments = computed(() => {   #...日付形式の変換
            if (documents.value) {
                return documents.value.map(doc => {
                    let time = formatDistanceToNow(doc.createAt.toDate())   # ... formatDistanceToNowがdate-fnsの関数
                    return { ...doc, createAt: time}    # createAtを編集後で上書き
                })
            }
        })

        const messages = ref(null)

        onUpdated(() => {
            messages.value.scrollTop = messages.value.scrollHeight
        })
        return { error, documents, formattedDocuments, messages }
    }
}
</script>
```


- `getCollection.js`

```javascript
import { ref, watchEffect } from 'vue'
import { projectFirestore } from '../firebase/config'


const getCollection  = (collection) => {
    const documents = ref(null)
    const error = ref(null)


    let collectionRef = projectFirestore.collection(collection)
        .orderBy('createAt')
    
    const unsub = collectionRef.onSnapshot((snap) => {    # ...これがリアルタイムでchatを更新するためにsnapshotを取って、変更があったらdocumentsを変える関数
    // collectionRef.onSnapshot((snap) => {
            console.log('snapshot');
        let results = []
        snap.docs.forEach(doc =>{
            doc.data().createAt && results.push({ ...doc.data(), id:doc.id })   # createAtを入れることで、サーバ側でタイムスタンプが振られたデータのみを入れている？
        })
        documents.value = results
        error.value = null
    }, (err) => {
        console.log(err.message)
        documents.value = null
        error.value = 'could not fetch data'
    })

    watchEffect((onInvalidate) => {   # ...これが何度もサーバ側にアクセスに行かないための仕組みらしいが、いまいちよくわからない
        onInvalidate(() => unsub())
    })
    return { documents, error }
}

export default getCollection
```


### firebase deploy


firebase-tools のインストール

```bash
npm install -g firebase-tools
```


```bash
firebase login
firebase init
```
↑initのときにpublicフォルダに書くか？とのところで[dist]を指定する


```bash
npm run build
```

```bash
firebase deploy
```


firestoreの書き込み/読み込み制御
`firestore.rules`

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /massages/{messageId} {
      allow read, write: if request.auth != null;
    }
  }
}
```
firestore.rulesだけ反映する場合は、

```bash
firebase deploy --only firestore:rules
```




`firebase.json`

```json
{ 
  "firestore" : {     # ...ココ追加する
      "rules": "firestore.rules",
      "indexes": "firestore.indexes.json"
  },
  "hosting": {
    "public": "dist",
    "ignore": [
      "firebase.json",
      "**/.*",
      "**/node_modules/**"
    ],
    "rewrites": [
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  }
}
```


- API Keyがfirebase/confg.jsに書かれていて、buildするときにjsにまとめられているから、そのまま公開されている件については、google developerのAPI利用で API Restrictionsを呼び元ドメインを制御するだけでいいらしい




### Playlist App


- Login / Signupなど前回同様のものは省略


- `App.vue`
  - AppにNavbarを入れて、どの画面でもNavbarが表示される

```vue
<template>
  <Navbar />
  <div class="content">
    <router-view/>
  </div>
</template>


<script>
import Navbar from './components/Navbar.vue'


export default {
  components: { Navbar }
}
</script>
```


- `Navbar.vue`
  - NavbarはCreatePlaylist / UserPlaylists / Signup / Login / Logoutを表示

```vue
<template>
    <div class="navbar">
        <nav>
            <img src="@/assets/ninja.png" >
            <h1><router-link :to="{ name : 'Home'}">Moso Ninjas</router-link></h1>
            <div class="links">
                <div v-if="user">
                    <router-link class="btn" :to="{ name: 'CreatePlaylist'}">Create Playlist</router-link>
                    <router-link class="btn" :to="{ name: 'UserPlaylists'}">My Playlist</router-link>
                    <span>Hi here, {{ user.displayName }}</span>
                    <button @click="handleClick">Logout</button>
                </div>
                <div v-else>
                    <router-link class="btn" :to="{ name: 'Signup'}">Signup</router-link>
                    <router-link class="btn" :to="{ name: 'Login'}">Log in</router-link>
                </div>
            </div>
        </nav>
    </div>
</template>


<script>
import useLogout from '@/composables/useLogout.js';
import getUser from '@/composables/getUser.js';
import { useRouter } from 'vue-router';


export default {
    setup() {
        const router = useRouter()
        const {error, logout, isPending} = useLogout()
        const {user} = getUser()


        const handleClick = async () => {
            await logout()
            if (!error.value) {
                router.push({ name: 'Login'})
            }
        }


        return {handleClick, user}
    }
}


</script>
```


- `Home.vue`
  - Home.vueでListView. Listviewは、UserPlaylistsでも使い回す

```vue
<template>
  <div class="home">
    <div v-if="!error">
      <div v-if="documents">
        <ListView :playlists="documents" />
      </div>
    </div>
    <div v-else class="error">Could not fetch playlists.</div>
  </div>
</template>


<script>
import ListView from '@/components/ListView';
import getCollection from '@/composables/getCollection';
export default {
  components: { ListView },
  setup() {
    const { documents, error} = getCollection('playlists')


    return { documents, error}
  }
}
</script>
```


- `PlaylistDetails.vue`

```vue
<template>
  <div v-if="error" class="error">{{ error }}</div>
  <div v-if="playlist" class="playlist-details">
      <div class="playlist-info">
        <div class="cover">
          <img :src="playlist.coverUrl" alt="">
        </div>
        <h2>{{ playlist.title }}</h2>
        <p class="username">Created by {{ playlist.userName}}</p>
        <p class="description">{{ playlist.description }}</p>
        <div v-if="ownership">    # ...自分のリストのみ削除ボタンを表示
          <button @click="handleDelete" v-if="!isPending">Delete Playlist</button>
          <button v-if="isPending" disabled>Deleting...</button>    # ...実行中の表示変更
        </div>
      </div>


      <div class="song-list">
        <div v-if="!playlist.songs.length">No songs have been added to this playlist yet.</div>
        <div class="single-song" v-for="song in playlist.songs" :key="song.id">
            <div class="details">
              <h3>{{ song.title }}</h3>
              <p>{{ song.artist }}</p>
            </div>
            <button v-if="ownership" @click="handleDeleteSong(song)">delete</button>
        </div>
        <AddSong v-if="ownership" :playlist="playlist"/>    # songの追加
      </div>
  </div>
</template>


<script>
import AddSong from '@/components/AddSong.vue';
import useStorage from '@/composables/useStorage';
import getDocument from '@/composables/getDocument';
import getUser from '@/composables/getUser';
import useDocument from '@/composables/useDocument';
import { computed } from 'vue';
import { useRouter } from 'vue-router';


export default {
    props: ['id'],
    components: { AddSong},
    setup(props) {
        const { error, document: playlist } = getDocument('playlists', props.id)
        const { user } = getUser()
        const { deleteImage } = useStorage()
        const { error: useDocument_error, deleteDoc, updateDoc, isPending } = useDocument('playlists', props.id)
        const router = useRouter()


        const ownership = computed(() => {      # ... playlist.value.userId == user.value.uidで自分のplaylistか判断
          return playlist.value && user.value && playlist.value.userId == user.value.uid
        })


        const handleDelete = async () => {
          await deleteImage(playlist.value.filePath)    # ...削除時は、画像を削除
          await deleteDoc()
          router.push({ name : 'Home' })
          if(useDocument_error.value) {
            console.log(useDocument_error.value);
          }
        }


        const handleDeleteSong = async (song) => {
          const updateSongs = playlist.value.songs.filter((item) => {   # ...songs削除時はfilterを使って簡易にコーディング
            return item.id != song.id
          })
          await updateDoc({   # ...list型はlistごとアップデートする
            songs : updateSongs
          })
        }


        return { error, playlist, ownership, handleDelete, isPending, handleDeleteSong}
    }
}
</script>
```


- `AddSong.vue`

```vue
<template>
  <div class="add-song">
    <button v-if="!showForm" @click="showForm = true">Add Songs</button>
    <form @submit.prevent="handleSubmit" v-if="showForm">
      <h4>Add a New Song</h4>
      <input type="text" placeholder="Song title" required v-model="title">
      <input type="text" placeholder="Artist" required v-model="artist">
      <button>Add</button>
    </form>
  </div>
</template>


<script>
import { ref } from 'vue';
import useDocument from '@/composables/useDocument';


export default {
  props: ['playlist'],
  setup(props) {
    const title = ref('')
    const artist = ref('')
    const showForm = ref(false)
    const { updateDoc } = useDocument('playlists', props.playlist.id)


    const handleSubmit = async () => {
      const newSong = {
        title : title.value,
        artist : artist.value,
        id : Math.floor(Math.random() * 1000000)    # ...songsにはidが必要. リストを追加・削除する必要があるため。このidの振り方は適当. プロダクトにする場合はライブラリを使うとか...
      }
      await updateDoc({   # ...playlistに持っているsongsはlist型で更新する
        songs : [...props.playlist.songs, newSong]
      })
      title.value = ''
      artist.value = ''
      showForm.value = false
    }


    return {title, artist, showForm, handleSubmit}


  }


}
</script>
```


- `CreatePlaylist.vue`

```vue
<template>
    <form @submit.prevent="handleSubmit">
        <h4>Create New Playlist</h4>
        <input type="text" required placeholder="Playlist title" v-model="title">
        <textarea type="text" required placeholder="Playlist description ..." v-model="description"></textarea>
        <label for="">Upload plalist cover image</label>
        <input type="file" @change="handleChange">    # ...ファイルチェックのためのchangeイベントを定義
        <div class="error">{{ fileError }} </div>
        <div class="error"></div>
        <button v-if="!isPending">Create</button>
        <button v-else disabled>saving...</button>
    </form>
</template>


<script>
import {ref} from 'vue';
import useStorage from '@/composables/useStorage';
import useCollection from '@/composables/useCollection';
import getUser from '@/composables/getUser';
import { timestamp } from '@/firebase/config';
import { useRouter } from 'vue-router';


export default {
    setup() {
        const { filePath, url, uploadImage } = useStorage()
        const { error, addDoc, isPending } = useCollection('playlists')
        const { user } = getUser()
        const router = useRouter()


        const title = ref("")
        const description = ref("")
        const file = ref(null)
        const fileError = ref(null)


        const handleSubmit = async () => {
            if (file.value) {
                await uploadImage(file.value)   # ...まずはファイルをアップロード
                const doc = {
                    title: title.value,
                    description: description.value,
                    userId: user.value.uid,
                    userName: user.value.displayName,
                    coverUrl: url.value,
                    filePath: filePath.value,   # ...アップロードしたファイルのpathを設定
                    songs: [],
                    createdAt: timestamp()
                }
                const res = await addDoc(doc)
                if(!error.value){
                    console.log('created');
                    router.push({name: 'PlaylistDetails', params : {id: res.id}})   # ...追加後、playlistdetailsに遷移
                }
            }
        }


        const types = ['image/png', 'image/jpeg']
        const handleChange = (e) => {     # ...ファイルがアップロードされて、png/jpegのみ許すので、そのときにfileを保存
            const selected = e.target.files[0]
            console.log(selected);


            if (selected && types.includes(selected.type)) {
                file.value = selected
                fileError.value = null
            } else {
                file.value = null
                fileError.value = 'Please select an image file (png or jpg)'
            }
        }


        return {title, description, handleSubmit, handleChange,fileError, isPending}
    }
}
</script>
```


- `getCollection.js`

```javascript
import { ref, watchEffect } from 'vue'
import { projectFirestore } from '../firebase/config'


const getCollection  = (collection, query) => {
    const documents = ref(null)
    const error = ref(null)


    let collectionRef = projectFirestore.collection(collection)
        .orderBy('createdAt')
    
    if (query) {    # ...HomeとUserPlaylistsでListviewを使いまわすためにqueryを受け取り、データを制御
        collectionRef = collectionRef.where(...query)
    }


    const unsub = collectionRef.onSnapshot((snap) => {
    // collectionRef.onSnapshot((snap) => {
            console.log('snapshot');
        let results = []
        snap.docs.forEach(doc =>{
            doc.data().createdAt && results.push({ ...doc.data(), id:doc.id })
        })
        documents.value = results
        error.value = null
    }, (err) => {
        console.log(err.message)
        documents.value = null
        error.value = 'could not fetch data'
    })


    watchEffect((onInvalidate) => {
        onInvalidate(() => unsub())
    })
    return { documents, error }
}


export default getCollection
```


#### firebase のインデックス作成


- 上記で実行をかけると初回時に、コンソールログにインデックス作成リンクが発生する。それをクリックして、firestoreにインデックスを入れる必要がある. 上の例では、document('playlist').userIdへのインデックス




- `useCollection.js`
  - ドキュメントの追加

```javascript
import { ref } from 'vue'
import { projectFirestore } from '../firebase/config'


const userCollection  = (collection) => {
    const error = ref(null)
    const isPending = ref(false)    # ...実行中を示すためのisPending


    const addDoc = async (doc) => {
        error.value = null
        isPending.value = true
        
        try {
            const res = await projectFirestore.collection(collection).add(doc)
            isPending.value = false
            return res
        } catch (err) {
            console.log(err.message);
            error.value = 'could not send the message'
            isPending.value = false
        }
    }


    return {error, addDoc, isPending}
}


export default userCollection
```
- `useDocument.js`
  - ドキュメントの削除と更新
    - useCollectionでドキュメントの追加、useDocumentで更新・削除に分けているのは、追加はprojectFirestore.collection().addに対し、更新・削除は、projectFirestore.collection().doc().delete(),update()だからだと思われる

```javascript
import { ref } from 'vue'
import { projectFirestore } from '../firebase/config'


const useDocument  = (collection, id) => {
    const error = ref(null)
    const isPending = ref(false)


    const docRef = projectFirestore.collection(collection).doc(id)


    const deleteDoc = async () => {
        error.value = null
        isPending.value = true
        
        try {
            const res = await docRef.delete()
            isPending.value = false
            return res
        } catch (err) {
            console.log(err.message);
            error.value = 'could not delete the document'
            isPending.value = false
        }
    }


    const updateDoc = async (updates) => {
        error.value = null
        isPending.value = true
        
        try {
            const res = await docRef.update(updates)
            isPending.value = false
            return res
        } catch (err) {
            console.log(err.message);
            error.value = 'could not update the document'
            isPending.value = false
        }
    }


    return { error, deleteDoc, updateDoc, isPending}
}


export default useDocument
```


- `useStorage.js`

```javascript
import { projectStorage } from '../firebase/config'
import { ref } from 'vue';
import getUser from './getUser';


const { user } = getUser()


const useStorage = () => {
    const error = ref(null)
    const url = ref(null)
    const filePath = ref(null)


    const uploadImage = async (file) => {
        // console.log(user.value.uid, file.name);
        filePath.value = `covers/${user.value.uid}/${file.name}`
        const storageRef = projectStorage.ref(filePath.value)


        try {
            const res = await storageRef.put(file)      # ...putしてその後、urlが返却されるので、それを保持. プロダクト時は、ファイル名はdjangoのように自動で可変にならず、既存ファイルがあれば上書きされてしまうので自前で可変にする仕組みを入れる必要がある？
            url.value = await res.ref.getDownloadURL()
        } catch (err) {
            console.log(err.message);
            error.value = err.message
        }
    }


    const deleteImage = async (path) => {
        const storageRef = projectStorage.ref(path)   # ...deleteはパスで削除するだけ


        try {
            await storageRef.delete()
        } catch (err) {
            console.log(err.message);
            error.value = err.message
        }
    }


    return {url, error, filePath, uploadImage, deleteImage}
}


export default useStorage
```


- `router/index.js`

```javascript
import { createRouter, createWebHistory } from 'vue-router'
import Home from '../views/Home.vue'
import Login from '../views/auth/Login.vue'
import Signup from '../views/auth/Signup.vue'
import CreatePlaylist from '../views/playlists/CreatePlaylist.vue'
import PlaylistDetails from '../views/playlists/PlaylistDetails.vue'
import UserPlaylists from '../views/playlists/UserPlaylists.vue'


import { projectAuth} from '../firebase/config';


const requireAuth = (to, from , next) => {
  let user = projectAuth.currentUser
  if (!user) {
    next({ name : 'Login'})
  } else {
    next()
  }
}


const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home,
    beforeEnter: requireAuth    # トップページの / にこれを入れることで、Homeはログイン後のメインページにして、未ログインでアクセスが来たらログインページにリダイレクト、という形にしている
  },
  {
    path: '/login',
    name: 'Login',
    component: Login
  },
  {
    path: '/signup',
    name: 'Signup',
    component: Signup
  },
  {
    path: '/playlists/create',
    name: 'CreatePlaylist',
    component: CreatePlaylist,
    beforeEnter : requireAuth
  },
  {
    path: '/playlists/:id',
    name: 'PlaylistDetails',
    component: PlaylistDetails,
    beforeEnter : requireAuth,
    props: true
  },
  {
    path: '/playlists/user',
    name: 'UserPlaylists',
    component: UserPlaylists,
    beforeEnter : requireAuth,
  }


]
```




### Firebase9以降の場合


- Firebase9以降ではライブラリ呼び出しが可能になった. 8以前と呼び方が異なる


- `config.js`

```javascript
import { initializeApp } from "firebase/app";       // firebase9
import { getFirestore } from 'firebase/firestore'   // firebase9
import { getAuth } from 'firebase/auth'
// import 'firebase/storage'


const firebaseConfig = {
  apiKey: "REDACTED_API_KEY",
  authDomain: "fb9-reading-list-cec80.firebaseapp.com",
  projectId: "fb9-reading-list-cec80",
  storageBucket: "fb9-reading-list-cec80.appspot.com",
  messagingSenderId: "331711620246",
  appId: "1:331711620246:web:ae3ef6bc915f50e3598eb7"
};


initializeApp(firebaseConfig);  // firebase9


const db = getFirestore()   // firebase9
const auth = getAuth()   // firebase9


export { db, auth }
```


- getCollection.js

```
import { ref, watchEffect } from 'vue'


import { db }from '../firebase/config';
import { collection, onSnapshot, query, where }from 'firebase/firestore';   # ...←このようにcollectionなどのライブラリをimportする


const getCollection  = (c, q) => {
    const documents = ref(null)
    const error = ref(null)


    let collectionRef = collection(db, c)   # ...ライブラリの第一引数にconfigで定義したオブジェクトを指定する
    
    if (q) {
        collectionRef = query(collectionRef, where(...q))
    }
    
    const unsub = onSnapshot(collectionRef, snap => {
        let results = []
        snap.docs.forEach(doc =>{
            results.push({ ...doc.data(), id:doc.id })
        })
        documents.value = results
        error.value = null
    }, (err) => {
        console.log(err.message)
        documents.value = null
        error.value = 'could not fetch data'
    })


    watchEffect((onInvalidate) => {
        onInvalidate(() => unsub())
    })
    return { documents, error }
}


export default getCollection
```

### vueのeslint
#### コンパイル時の未使用変数のチェックをオフ
- vueの場合、直下に`.eslintrc.js`がある

```javascript
module.exports = {
  rules: {
    "no-unused-vars": "off"
  }
}
```
