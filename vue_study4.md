# 第4回Vue.js勉強会

## 今日の目標「Vuex,Vue Routerについて少し理解しよう」

```
1. Vuexについて
2. Vue Routerについて
```
###### Vuexとは？

`Vuex`とは、データと状態に関する全てを一元管理する「状態管理」のための拡張ライブラリです。     
第2回の勉強会で紹介したコンポーネントの使い方では`props`とカスタムイベントを使った親子間の通信、他にもイベントバスを使った親子間ではないコンポーネント間での通信も可能です。シンプルなアプリケーションでは、この２つのデータ管理の手法で十分可能です。    
しかし、コンポーネントが構造化したり、アプリケーションの規模が大きくなりコンポーネントの構造が複雑になると、この方法では管理しきれないことが予想されます。  

確かに、`props`と`$emit`して親と孫の関係以上のコンポーネント同士の通信はしんどそう.....

```
＜親と曽曽孫の関係＞

コンポーネントA <-> コンポーネントB <-> コンポーネントC <-> コンポーネントD <-> コンポーネントE.....

```

ネットではバケツリレーに例えられていました...

そこで、この解決策となるのが`Vuex`です。
さらに、全てのコンポーネントから１つのデータを参照するため整合性も保たれるといったメリットもあります。

今までのデータのやり取りのイメージは`props`、`$emit`を使った、限られたコンポーネント間でのやり取りしかできなかったイメージですが、`Vuex`を使うことでアプリケーション全体まで状態の管理の対象になりました。

Vue CLIをプロジェクトを作った方はVuexのダウンロードもしてみましょう。
(VuexではES2015を利用しているため、対応してないブラウザをサポートする場合,`babel-polyfill`も)


```
$npm install vuex babel-polyfill
```

準備として..   

`src/store.js`
```js
import 'babel-profill'
import Vue from 'vue'
import Vuex from 'vuex'

//プラグインとして登録
Vue.use(Vuex)
```

では早速、解説していきます。

Vuexを使ってカウンタの数値を増やすだけのシンプルなストア構造をみてみましょう。

コードを簡単に説明します。

Vuexは状態を管理するための「ストア」を作成します。ストアはアプリケーション内に作った「仮想のデータベース」のようなものです。まずは、`src/`直下に`store.js`ファイルを作成し、`Vuex.Store`コンストラクタを使ってストアのインスタンスを作成します。
`state`はコンポーネントの`data`プロパティ、`mutations`は`methods`に機能が似ていますね。

`src/store.js`

```js
//import 'babel-polyfill'
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)

// ストアを作成
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    // カウントアップするミューテーションを登録
    increment(state) {
      state.count++
    }
  }
})
export default store
```
`main.js`ではstoreで作ったデータへのアクセスしてます。まず`store.js`ファイルをインポートして、`store.state.count`の形でアクセスできます。  
また、`store`インスタンスで登録されている`mutations`は`store.commit`で実行できます。

`src/main.js`
```js
//store.jsをインポート
//@はデフォルトで登録されている`src`ディレクトリのエイリアス
import store from '@/store.js'

console.log(store.state.count) // -> 0
// incrementをコミットする
store.commit('increment')
// もう一度アクセスしてみるとカウントが増えている
console.log(store.state.count) // -> 1

```
実際に書いた人は.....                 
`npm run dev`を実行してconsoleを開いてみてください。

![実行結果](https://i.imgur.com/P9sO0oT.png)

Vuex内のインスタンス参照方法

Vuexのインスタンスは、Vue.js本体のように`this`を使用しないようです。
よって、必要なプロパティや、メソッドは、引数として渡されます。

```js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment(state) {
      state.count++
    }
    //アロー関数(function式を省略できるやつ)を使うと...
    decrement : state => {state.count-- }
  }
})
```
アロー関数についての記事=> [URL](https://qiita.com/may88seiji/items/4a49c7c78b55d75d693b)


コンポーネントとVuexの各機能を図にしたものです。  
一方通行の線が１本しかないため実は意外と単純なのです。   
`コンポーネント`からステートを更新したいとなった時、その間にある`アクション`と`ミューテーション`を使うことがわかります。

![図](https://coinbaby8.com/wp-content/uploads/2018/11/vuex.png)

[図の引用](https://coinbaby8.com/nuxt-vuex-beginner.html)

実際にコンポーネントと一緒に使う前に、各機能について紹介します。

ステート(state)

ステートは、ストアで管理している状態そのものであり、コンポーネントで登場した`data`に似てます。ステートはミューテーションでしか変更することはできません。

```js
const store = new Vuex.Store({
  state: {
    message: 'メッセージ'
  }
})
```
呼び出し方はこんな感じ
```js
store.state.count
```

ゲッター(getter)

ゲッターは、ステートを取得するための算出データです。  
引数が欲しい時はメソッド関数で返します。
`return`で値が返されてますね。
ゲッターを介さず、ステートにアクセスすることもできますが、使う側が混乱しないように`getters` に統一することが進められているようです。


```js
const store = new Vuex.Store({
  state: {
    count: 0,
    list: [
      { id: 1, name: 'りんご', price: 100 },
      { id: 2, name: 'ばなな', price: 200 },
      { id: 3, name: 'いちご', price: 300 }
    ]
  },
  getters: {
    // 単純にステートを返す
    count(state) {
      return state.count
    },
    // リストの各要素の price プロパティの中から最大数値を返す
    max(state) {
      return state.list.reduce((a, b) => {
        return a > b.price ? a : b.price
      }, 0)
    }
  }
})
```
呼び出し方
```js
store.getters.count
store.getters.max
```

ミューテーション(mutations)

ステートを変更できる唯一のメゾッドであり、コンポーネントで言う所の`methods`です。
引数としての情報は
1,state:ステート
2,payload:コミットからの引数

```js
const store = new Vuex.Store({
  // ...
  mutations: {
    mutationType(state, payload) {
      state.count = payload
    }
  }
})
```

コミット(commit)   
登録されているミューテーションを呼びだす機能を持っています。
```js
store.commit('mutationType', payload)
```

この他にも`action`や、これを呼びだす`dispatch`というものもあります。


[(アクションについての公式ドキュメント)](https://vuex.vuejs.org/ja/guide/actions.html)

実際のコンポーネントでストアの使用

まずは`main.js`に登録します。これで、どこからでも`store.js`で作ったストアが使えます。

`src/main.js`
```js
//....
import store from './store.js'
//.....
new Vue({
  ///.....
  store,
  ///.....
  })
```
以下の`store.js`では特に難しいことはやってません。
先ほど、説明しなかった`actions`も`commit`でミューテーションを呼びだすことで更新の処理をしているだけです。    

`src/store.js`
```js
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)

const store = new Vuex.Store({
  state: {
    message: '初期メッセージ'
  },
  getters: {
    // messageを使用するゲッター
    message(state) {
      return state.message
    }
  },
  mutations: {
    // メッセージを変更するミューテーション
    setMessage(state, payload) {
      state.message = payload.message
    }
  },
  actions: { // メッセージの更新処理
    doUpdate({commit}, message) {
      commit('setMessage', {message})
    }
  }
})
export default store
```
メッセージの算出プロパティと、その下に編集フォーム用の「EditForm」コンポーネントのカスタムだぐを記述しています。
`src/App.vue`
```js
<template>
  <div class="app">
    <h1>{{ message }}</h1>
    <EditForm/>
  </div>
</template>
<script>
import store from './store'
// 子コンポーネントを読み込む
import EditForm from './components/EditForm'
export default {
  name: 'app',
  components: {
    EditForm
  },
  computed: {
    // ローカルの message とストアの message を同期
    message() {
      return store.getters.message
    }
  }
}
</script>
```
ステートはミューテーション以外から書き換えていけないというルールがあるので、`v-model`を使うと自動的に値を書き和えようとするため、エラーになってしまいます。そこで、算出プロパティにセッターを使うと`v-model`が使えます。   

`src/components/EditForm.vue`
```js
<template>
  <div class="edit-form">
    <input v-model="message">
  </div>
</template>

<script>
import store from '../store'
export default {
  name: 'EditForm',
  computed: {
    message: {
      get() { return store.getters.message },
      set(value) { store.dispatch('doUpdate', value) }
    }
  },
  methods: {
    doUpdate(event) {
      // input の値を持ってディスパッチ
      store.dispatch('doUpdate', event.target.value)
    }
  }
}
</script>
```

[ここでいじってみて下さい](https://kusa-vuex.firebaseapp.com/)

実行結果
![](https://i.imgur.com/zEbsoUL.png)

こんな感じでVuexは利用されてます。    
勉強段階での規模のアプリケーションでの状態管理のVuexの導入は面倒に感じますよね。。。。    
ただ、規模が大きくなるにつれて必要性が大きくなるので、規模が大きくなりそうなアプリケーション開発初期にVuexの導入を検討してください。（途中からの導入は色々と大変らしい....）


###### Vue Routerについて

ルーティングとは...
URLをディレクトリ構造のようにして、画面を遷移させること？

神垣さん.;.



よく聞くSPAとは...    
シングルページアプリケーションとは、単一のウェブページをしようして、要素の内容のみをを置き換えて画面を遷移させるアプリケーションのこと。

そこで、今回紹介するのがVue router   
このVue RouterはコンポーネントとURLを紐ずけてコンテンツを作成してくれます。

インストール
```
#最新版のインストール
$npm install vue-router
#今回紹介するバージョン
$npm install vue-router@3.0.1
```

`src/router.js`
```js
import Vue from 'vue'
import VueRouter from 'vue-router'
//プラグインとして登録
Vue.use(VueRouter)
```
インストールすると,次のコンポーネントが使用可能になります。    

`<router-view>`:|ルートとマッチしたコンポーネントを描写する    

`<router-link>`:|ルートのリンクを作成

実際のrouterの使い方

フォルダの構造について特に決まりはないですが、ルートと直接、結びつけるコンポーネントは`views`や`pages`といった名前のフォルダにまとめる。

次の２つのSFCはテンプレートだけのシンプルなコンポーネントです。

`src/views/Home.vue`
```html
<template>
  <div class="home">
    <h1>Home</h1>
  </div>
</template>
```
`src/views/Product.vue`
```html
<template>
  <div class="product">
    <h1>商品情報</h1>
  </div>
</template>
```
ルーターの設定ファイル`route.js`を作成し`Home`と`Products`のコンポーネントを読み込みパスとマッピングします。    
コードを見ていきましょう。

`src/router.js`
```js
import Vue from 'vue'
import VueRouter from 'vue-router'
// ルート用のコンポーネントを読み込む
import Home from '@/views/Home'
import Product from '@/views/Product'
// Vuexと同様で最初にプラグインとして登録
Vue.use(VueRouter)
// VueRouterインスタンスを生成する
const router = new VueRouter({
  // URLのパスと紐づくコンポーネントをマッピング
  routes: [
    {
      path: '/',
      //importしたコンポーネントとパスを紐ずけ
      component: Home
    },
    {
      path: '/product',
      //importしたコンポーネントとパスを紐ずけ
      component: Product
    }
  ]
})
// 生成したVueRouterインスタンスをエクスポート
export default router
```

`src/main.js`

```js
import router from './router.js'
new Vue({
  el: '#app',
  router, // アプリケーションに登録
  render: h => h(App)
})
```

`src/App.vue`
```html
<template>
  <div id="app">
    <nav>
      <router-link to="/">Home</router-link>
      <router-link to="/product">商品情報</router-link>
    </nav>
    <!-- ここにパスと一致したコンポーネントが埋め込まれる -->
    <router-view />
  </div>
</template>
```

[ちょっといじってみて下さい](https://study-router.firebaseapp.com)

URLに注目してください、さっき指定したpassになってますね。
![home](https://i.imgur.com/KGTfSTf.png)

そして商品状況のリンクをクリックすると...     
こちらも先ほど指定したpassになってますね

![pro](https://i.imgur.com/hQ4loX7.png)

こんな感じでVue Routerを使っていきます。細かい説明ができなかったのですが、大体の使い方はわかったと思います.     

次は簡単なアプリケーションの作成の紹介をしていきます。
次は最後の会です。少しはVueへの関心、理解が深まったでしょうか？
来週もよろしくお願いします。
