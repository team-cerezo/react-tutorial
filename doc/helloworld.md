# Hello, world!

まずは（ありがちだけど）Hello, world!をしておこう。

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

Chromeが起動して「Hello, world!」と書かれた画面が表示されたらOK。

もし表示されない場合は次のことをチェックしてみて。

* `App`の右辺が引数が0個のアロー関数になっているか
* `App`のアロー関数本体が妥当なXMLになっているか（終了タグを忘れていないか、ルート要素が複数ないか）
* `node_module`ディレクトリに依存パッケージがインストールされているか（`yarn install`すればインストールされる）

さて、ではこのHello, world!コードを見ていこう。

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
実はJSXは`React.createElement`メソッド呼び出しのシンタックスシュガーになっている。
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

これぐらいの規模だと可読性に違いはあまりないかもしれないけれど、もっと巨大になるとJSXのほうが読みやすいと感じるだろう（まあ可読性は主観なので断定はできないけれども、このチュートリアルではJSXを使う）。
