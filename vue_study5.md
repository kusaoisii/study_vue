# 第4回Vue.js勉強会

## 「今まで紹介したVue.jsの知識でwebアプリに挑戦」


###### フロントの知識がついたらwebアプリが作れる
フロントの知識がつくと、何か作りたくなります。    
ただ、サーバーサイドの知識がないからハードルが高い....      
と感じる人もいるかもしれません。    
そこで、頭がいい人が作ってくれた`サーバーレス`という仕組みを使って、簡単にサーバにあなたが作ったアプリを挙げられます。

サーバーレスとは簡単に説明すると、サーバーインフラのリソースを気にしなくても、サーバーサイドの処理ができる環境を利用することで、あなたはサーバー構築で考慮していた部分を考えなくて済むという話です。      
なんて素晴らしい技術なんでしょう...      
今回はgoogleさんが提供してくれている、サーバーレス環境`firebase`を利用します。

###### いきなりVue-cliを使ってデプロイまでしちゃう

[Firebse](https://firebase.google.com/)のアカウントを作り、下の画面の「スタートガイド」を押して,「プロジェクトを追加」を押しプロジェクトの追加に進みます。

![](https://i.imgur.com/HPjPR2E.png)

これで`Firebase`の初期設定は完了です。

`vue-cli`を導入していきます。コマンドを以下の通り

```
//vue-cliのインストール
$ npm install -g vue-cli
$ vue -h

//自分の作業ディレクトリに移動
$ mkdir vuestudy
$ cd vuestydy

//テンプレのダウンロード
//vueappはプロジェクト名(お好きにどうぞ)
$ vue init webpack-simple vueapp
```
聞かれる質問では最後の`Use sass?`をYesにし、それ以外は`enter`。  
無事ダウンロードできたら。
```
//プロジェクトのディレクトリに移動
$ cd vueapp
$ npm install
$ npm run dev
```

![](https://i.imgur.com/6b8Rf5W.png)

これで、皆さんはVueでwebサイトを表示することができます。       
次に、このサイトを`firebase`を使いデプロイしちゃいましょう。

まずは`/index.html`の`<script>`に自分のfirebaseの`config`コードを追加してください。

`config`コードは以下のようにコピーしてください。

![](https://i.imgur.com/qIkukjp.png)

`/index.html`
```html
<!DOCTYPE html>
<html>
 <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <title>vue_study</title>
  </head>
  <body>
    <div id="app"></div>
    <!-- ここに自分のfirebaseのconfigコードを追加-->
    <script src="https://www.gstatic.com/firebasejs/5.7.0/firebase.js"></script>
    <script>
    var config = {
      apiKey: "AIzaSyAQBs6l3AWfOAy7nuSdE90hzlLLu-3NcCI",
      authDomain: "studyvue-9e931.firebaseapp.com",
      databaseURL: "https://studyvue-9e931.firebaseio.com",
      projectId: "studyvue-9e931",
      storageBucket: "studyvue-9e931.appspot.com",
      messagingSenderId: "666786987262"
    };
    firebase.initializeApp(config);
  </script>
</body>
</html>
```
これらのタグはwebサイト上でFirebaseのデータを扱ったり、SNSログインするための命令を実行するための定義と設定をしています。このコードはユーザーに公開されてしまいますが、権限の管理は違う箇所でおこなれるので、公開されても問題はありません。

サイトをホスティングするには、npmを使用して Firebase コマンドラインツールをインストールする必要があります。

```
$ npm install -g firebase-tools
```
そして`init`作業
```
$ firebase init
```
1. 最初の質問では`hosting`をスペースで選択して、enter.   
2. 次の質問では作成したプロジェクトを選んでください。(作成したプロジェクトが表示されない方は、一番したの`new ~`を選択)     
3. `What do you want to use as public directory?`と表示され、公開に利用するディレクトリ名の入力が求められるため`dist`と入力してください。    
4. そして、次に公開する`index.html`だけかどうかを聞かれるので、そのまま`enter`(No)で進めてください。
5. (先ほど,`2`で`new ~`を選んだ人は以下のコマンドを実行し,
  次の質問には`staging`を選択)

```
$ firebase use --add
```

![](https://i.imgur.com/cHzXsc2.png)

次に、テンプレートのままだとパスが開発用の状態なので、ホスティングしてもらうための準備として、次の2つのファイルを変更します。  

`/webpack.config.js`
```
8: publicPath: '/',
~~~~~~
78: devSerber: {
79:  contentsBase:'dist', //追加
80:  htstoryApiFallback: true,
~~~~~
100: sourceMap: false,
```
次は,mvコマンドで`/index.html`を`/dist/index.html`へ移動し、`/dist/index.html`を変更。(元の`/dist/index.html`は消去してください。)

`/dist/index.html`
```
21: </script>
22: <script src="./build.js"></script>
23: </body>
```
これでめんどくさい設定は終わりです。   
そして、やっと`build`して`deploy`をします。    

```
$ npm run build
```
成功したら、ついに`deploy`です。

```
$ firebase deploy
```
成功するとURLが表示されると思うのでアクセスすると、先ほどのローカルでのサイトがちゃんと表示されています。

開発の手順を理解したところで、ソースコードを書いていく作業に移ります。
一から作るのは大変だと思うので、今回参考にしたのが山本先輩が技術書店にて買ってきてくれた「[Vue.jsとFirebaseで作るミニWebサービス](https://nextpublishing.jp/book/9884.html)」という本です。

こんな感じのものが出来上がります。

![](https://i.imgur.com/T1MEFvi.png)


この本では、僕が今まで紹介してきた文法の知識で`markdownエディタ`を作ることができます。

ローカルでいいからのアプリ作りたいって人は、記事見つけてきたので、この記事を参考に参考にしてtodoアプリを作ってみてください。
url=>[url](https://qiita.com/moonglows76/items/358ef3cd1566c38ece3a)

全部のコードを書くのは時間的に厳しいので、今までの復習を兼ねて、使われているコードの紹介をしていきます。

##### 今までの会の復習

これから紹介するのは、このアプリのエディタのコンポーネントで使われているードになります

このURLからみてみてください。＝＞　[GitHub](https://github.com/nabettu/mymarkdown/blob/master/src/components/Editor.vue)

このコードではエディタ部分のSFCが書かれています。

例えば、よく使われる,ボタンのハンドリング部分.
Vue.jsでは`v-on`を使ってイベントを発火させます。`@`というのはこの省略形です。今回はこの`v-on`でmethods内で定義した`logout`メソッドをハンドリングしています。

```js
~
5:  <button @click="logout">ログアウト</button>
~
~
59:  methods: {
60:     logout: function() {
         //ログアウト
61:       firebase.auth().signOut();
62:     },
~
~

```
簡単なコードの方がわかりやすいので..
`v-on`、`methods`を使ったコードの紹介をしときます。
>[「jsfiddleで実行」](https://jsfiddle.net/kusaoisii/f3x4u5L0/4/)

```html
<div id="app">
  <!-- countプロパティを表示する -->
  <p>{{ count }}回クリックしたよ！ </p>
  <!-- このボタンをクリックするとincrementメソッドが呼び出される -->
  <button v-on:click="increment">カウントを増やす</button>
  <!-- v-onは"@"と書きえ、省略することができます。 -->
</div>
```
```js
new Vue({
  el: '#app',
  data: {
    count: 0
  },
  methods: {
    // ボタンをクリックしたときのハンドラ
    increment: function () {
      this.count += 1 // 処理は再代入するだけでOK！
    }
  }
})
```
実行結果
![実行結果](https://i.imgur.com/4Vd5sHe.png)

次はサイドの保存してあるmarkdownのリスト表示の一部分のコード。   
分けて説明していきます。


```js
~
8:  <div class="memoList" v-for="(memo, index) in memos" :key="index" @click="selectMemo(index)" :data-selected="index == selectedIndex">
~
```
まずはfor文の説明です。
Vue.jsではforは`v-for`を使います。
dataプロパティで定義された`mamos`を回して、`(memo,index)`の`memo`には要素、`index`にはインデックスが格納されます。    
また、使うデータを保持するデータプロパティ(`data()`)ではreturnでオブジェクトとして返す形になることも忘れないでください。


```js
~
8:   ~ v-for="(memo, index) in memos" ~
~
27: data() {
28:    return {
29:      memos: [
30:        {
31:          markdown: ""
32:        }
33:      ],
34:      selectedIndex: 0
35:    };
36:  },
~
```

ここでは`v-bind`が出てきました。
`:data-selected="index == selectedIndex"`ではDOM要素(data-selected)に`index == selectedIndex`の結果をデータバインディングすることで、データの変更をするたびに、バインドされたDOM要素が更新されます。
ここでは、この`data-selected`によって背景の色が変わるよう`css`側で設定されてます。(css(scss)の説明は省きます)
```js
8: ~ :data-selected="index == selectedIndex" ~
```


`v-bind`は`<input>`はテキスト入力欄によく使われえる要素なので覚えときましょう。    
>[「jsfiddleで実行」](https://jsfiddle.net/kusaoisii/ca0v2e67/3/)  

```html
<input v-bind:value="message" v-on:change="handleInput">
<pre>{{ message }}</pre>
```

```js
new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue.js',
  },
  methods: {
    handleInput: function (event) {
      // 代入前に何か処理を行う…
      this.message = event.target.value
    }
  }
})
```
`handleInput`では入力データを`message`に代入しています。
実行結果
![実行結果](https://i.imgur.com/rU3sZzN.png)

次はVueでの条件分岐の仕方は`v-if`を使います。   
条件=trueだった場合のみ、この要素が表示,実行されます。

```js
12: ~ v-if="memos.length > 1" ~
```
例えば、以下では`v-if`は`show`が`true`の時しかp要素を表示しません。

>[「jsfiddleで実行](https://jsfiddle.net/kusaoisii/rz2fac6y/)

```html
<button v-on:click="show=!show">切り替え</button>
<p v-if="show">Hello Vue.js!</p>
```
```js
var app = new Vue({
  el: '#app',
  data: {
    show: true
  }
})
```
実行結果
![実行結果](https://i.imgur.com/o89wF6e.png)

他にも、親子間でのコンポーネント(ここでは親がsrc/views/Top.vue)でデータをや受け渡しに使う方法の一つ`props`を使っています.`props`は親コンポーネントから子コンポーネントにデータを渡す時に子コンポーネント側で受け取る時`props`を利用します。

```
26:  props: ["user"],
```

![親子関係](https://1.bp.blogspot.com/-3Sdz6RsRJ-M/WA6zeqjGszI/AAAAAAAAEZg/sHy0PjcR7_AziwqMh3f2wU09t4dSQj54gCLcB/s400/props-events.png)


以上でコードの紹介、復習は終了です。
もし作る気分になったら,GitHubのソースコードを見よう見まねで作ってみてください。   

作るとこんな感じになる

![](https://i.imgur.com/T1MEFvi.png)
形になってきたら,buildしてdeployしてみましょう。
VueRouterも使われていますのでGitHubで復習してみてください。
Vuexは使われてませんが,Googleのユーザー情報など登録しとくと便利だと思います。(僕は勉強のために導入しました [僕のダサダサアプリ(開発中)](https://kusa-markdown.firebaseapp.com/#/)と[コード](https://github.com/kusaoisii/vuemarkdown)を載せておきます)   

そしてちょっと先の話をすると。。。
ちょっと形になってきたら、デザインも気になると思うので`Elemnt UI`や`vuetify`などのデザインフレームワークが提供してくれている、コンポーネントなども使ってみてください。多分デザのバイトでは`element ui`が結構使われています。      
(僕のアプリでは`vuetify`を使っていますが....)

こんな感じで、element uiがいい感じのものを提供してくれています。

![](https://i.imgur.com/cU0pjEO.png)


[elemet uiのURL](https://element.eleme.io/#/en-US)      
[vuetifyのURL](https://vuetifyjs.com/ja/)

##### Vueを復習したい人向けての話

先ほど使った`vue-cli`の環境はディレクトリ構造がちょっと
難しいので、ただ文法をマスターしたい人はCDNを<script>で持ってくる環境をお勧めします。環境構築は必要ないので楽です。
[こんな感じ](https://jsfiddle.net/kusaoisii/f3x4u5L0/4/)
ただ,vue.jsの本命的なコンポーネントファイル`.vue`はVue-cliを導入しないといけないので、コンポーネントを学んだあと`.vue`ファイルを触れたくなった時,`vue-cli`の導入してもいいと思います。


以上で、この会を終了します。参加ありがとうございました。
