# 第5回Vue.js勉強会

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

この本では、僕が今まで紹介してきた文法の知識で`markdownエディタ`を作ることができます。

全部のコードを書くのは時間的に厳しいので、今までの復習を兼ねて、使われているコードの紹介をしていきます。

##### 今までの会の復習

~

以上でコードの紹介、復習は終了です。
形になってきたら,buildしてdeployしてみましょう。
VueRouterも使われていますのでGitHubで復習してみてください。
Vuexは使われてませんが,Googleのユーザー情報など登録しとくと便利だと思います。(僕は勉強のために導入しました [僕のアプリ(開発中)](https://kusa-markdown.firebaseapp.com/#/)(開発中)と[コード](https://github.com/kusaoisii/vuemarkdown)を載せておきます)   

簡単なマテリアルデザインの紹介

〜〜〜


以上で、この会を終了します。参加ありがとうございました。
