# Hello, world!

まずは（ありがちだけど）`Hello, world!`をしておこう。

`src`ディレクトリの下に`index.js`を作成しよう（このようなとき、以降は「`src/index.js`を作成しよう」と表現する）。
`src/index.js`に次のコードを書こう。

```js
import React from 'react';
import ReactDOM from 'react-dom';

const App = () => (
    <div>
        <h1>Hello, world!</h1>
    </div>
);

ReactDOM.render(
    <App />,
    document.getElementById('root'));
```

ファイルを保存したら次のコマンドでサーバーを起動しよう。

```console
yarn start
```

Chromeが起動して`Hello, world!`と書かれた画面が表示されたらOK。

もし表示されない場合は次のことをチェックしてみて。

* `App`の右辺が引数が0個のアロー関数になっているか
* `App`のアロー関数本体が妥当なXMLになっているか（終了タグを忘れていないか、ルート要素が複数ないか）
* `node_module`ディレクトリに依存パッケージがインストールされているか（`yarn install`すればインストールされる）

さて、ではこの`Hello, world!`コードを見ていこう。

## import

先頭に次のコードがある。

```js
import React from 'react';
import ReactDOM from 'react-dom';
```

これは`react`と`react-dom`を使うぞ、という宣言だ。
それぞれ`React`と`ReactDOM`という名前を付けている。
[JavaScript(ES2015)](es2015.md)で最後に書いた`require`と同じようなものだと思ってよい。

`react`とか`react-dom`がどこにあるかというと`node_module`ディレクトリの中だ。
`react`も`react-dom`もディレクトリなので、その場合は中にある`index.js`が読み込まれる。

自分で`import`されるファイルを書く機会は後ほど出てくるので、そのときにもう少し話をする。
ともかく、今はこれでReactとReactDOMを使えるようになったと思っておいて。

## App

次、真ん中あたりにあるこのコード。

```js
const App = () => (
    <div>
        <h1>Hello, world!</h1>
    </div>
);
```

このコードを整理するとまず`const`キーワードで変数を宣言している。
変数名は`App`だ。
それから代入の`=`があり、右辺はアロー関数になっている。

アロー関数は引数が0個なので`()`が書かれており、`=>`が続いている。
`=>`の右辺は全体が`(`と`)`で囲まれている。
`(`と`)`の中はXMLになっている。

このXMLはJSXと呼ばれる文法で、JavaScriptに組み込まれている文法ではなくReactが拡張しているものだ。

なぜReactは文法を拡張してまでJSXを取り入れたのか。
実はJSXは`React.createElement`関数呼び出しのシンタックスシュガーになっている。
JSXで定義した`App`のコードは次のコードと等価だ。

```js
const App = () => (
    React.createElement('div', null,
        React.createElement('h1', null, 'Hello, world!')
    )
);
```

JSXを使用したコードと見比べてみてほしい。
どちらがよいだろうか。

これぐらいの規模だと可読性に違いはあまりないかもしれないけれど、もっと巨大になるとJSXのほうが読みやすいと感じるだろう（まあ可読性は主観なので絶対にJSXの方が読みやすいと断定はできないけれども、このチュートリアルではJSXを使う）。

## ReactDOM.render

最後に`ReactDOM.render`となっているコード。

```js
ReactDOM.render(
    <App />,
    document.getElementById('root'));
```

これは`root`というid属性を持つ要素に`App`を描画することを表している。

このようにReactでは画面の構築（JSX）と描画（`ReactDOM.render`）が分離されており、描画方法を変えることでブラウザ以外にも描画できる。
例えばサーバー上でHTMLを出力するための[ReactDOMServer](https://reactjs.org/docs/react-dom-server.html)や自動テストをするための[Test Renderer](https://reactjs.org/docs/test-renderer.html)というものがある。

## ビルドについて

さて、JSXは`React.createElement`関数呼び出しのシンタックスシュガーだと述べたが、これは`yarn start`で起動したサーバーの処理中に変換されている。
変換はwebpackとBabelというものを使用して行われる。

まずwebpackだけど、これはモジュールバンドラと呼ばれる種類のツール。
昔のJavaScriptにはモジュール化の概念（`import`のこと）がなかったけどES2015で導入された。
仕様としては導入されたけどもブラウザがサポートしていないので代替ツールが必要であり、そのためのツールがwebpackだ。
まあ、いろいろと書いたけど「複数のjsファイルを1つのjsファイルにまとめるやつ」だと思っておいて。
で、まとめる過程でまとめる対象のjsファイルを加工できるのだけれど、そこでBabelが登場する。

Babelはコンパイラだ。
コンパイルというと「人間が読める文法で書かれたソースコードをコンピュータが読める機械語で書かれたバイナリへ翻訳する」ことを思い浮かべると思うが、Babelは「モダンな文法で書かれたスマートなjsコードをレガシーな文法で書かれた味わい深いjsコードへ翻訳する」のが主な仕事だ。
そうすることで、モダンな文法をサポートできていないブラウザでも動かせられるjsを、モダンな文法で書ける。

というわけで、JSXで書かれたコードを`React.createElement`を使ったコードに変換することもBabelが行ってくれている。

ビルドに関する説明は一旦この辺りで止めて、より詳しくは別の機会に持ち越す。
コードを書く作業へ戻ろう。

## ボタンを押して表示されるメッセージを変える

今の画面は`Hello, world!`というメッセージが表示されているだけだ。
1つボタンを置いて、そのボタンが押されたらメッセージが`Hello, React!`へと変わるようにしてみよう。

まず`Hello, world!`とハードコーディングしている部分から`world`を変数に切り出そう。

```js
//メッセージを再代入することで変更される想定なのでletにしている
let message = 'world';

const App = () => (
    <div>
        <h1>Hello, {message}!</h1>
    </div>
);
```

JSXでは変数を埋め込んだりjsコードを書く場合、`{`と`}`で囲む。
JavaでいうとJSPで使うEL式（`${foo.bar}`のようなやつ）に近いかもしれない。

次にボタンを設置しよう。

```js
const changeMessage = event => {
    message = 'React';
};

const App = () => (
    <div>
        <h1>Hello, {message}!</h1>
        <button onClick={changeMessage}>ポチっと</button>
    </div>
);
```

ボタンが押されると`click`イベントが発生してイベントハンドラとして設定されている`changeMessage`関数が呼び出される。
`changeMessage`関数では`message`が変更されている。

これでメッセージが変更されるはずだ、と思ってボタンを押しても画面はなにも変わらないと思う。
画面を変えるためには再描画、つまり`ReactDOM.render`をやらないといけない。

というわけで`changeMessage`関数内で`ReactDOM.render`を呼び出すようにしてみる。

```js
const changeMessage = event => {
    message = 'React';
    ReactDOM.render(
        <App />,
        document.getElementById('root'));
};
```

もとから書かれていた`ReactDOM.render`とコードが重複しているのでまとめよう。
ここまででコード全体は次のようになった。

```js
import React from 'react';
import ReactDOM from 'react-dom';

//メッセージを再代入することで変更される想定なのでletにしている
let message = 'world';

const render = () => {
    ReactDOM.render(
        <App />,
        document.getElementById('root'));
};

const changeMessage = event => {
    message = 'React';
    render();
};

const App = () => (
    <div>
        <h1>Hello, {message}!</h1>
        <button onClick={changeMessage}>ポチっと</button>
    </div>
);

render();
```

最後に`render`関数を導入したように今は描画のタイミングを明示的に指定するようなコードを書いているが、これから簡易的なToDoリストを作成していく過程で描画のタイミングはFluxというライブラリに任せるようになる。
今だけは面倒だと思いつつ、`render`を呼び出しておいて。
