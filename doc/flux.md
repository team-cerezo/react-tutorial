# [WIP]Fluxを使う

これまで作ったtodoリストにFluxを適用するのが目標だけど、その前にゼロからFluxを使ったサンプルを作って入門しておこう。

というわけで任意のディレクトリで`todolist`とは別のプロジェクトを作ろう。

```console
create-react-app hello-flux
```

作成されたディレクトリに移動して、

```console
cd hello-flux
```

Fluxを追加しよう。

```console
yarn add flux
```

あ、要らないものは消しておこう。

```console
del /q src\*
```

## カウンターを作る

数字が表示されていて、ボタンをぽちぽち押すとカウントアップするシンプルな例で進めよう。

まずはビューを作ろう。
次のコードを`src/index.js`に書いて`yarn start`で起動しよう。
（小さいコード＆説明がしやすいのでものぐさ発揮して`src/index.js`に書いちゃっているけれど、これを`src/components.js`とか`src/component-structure.js`に分割するのも自習ネタにいいかも）

```js
import React from 'react';
import ReactDOM from 'react-dom';

const Counter = ({ count }) => (<h1>{count}</h1>);

const IncButon = () => (
    <div>
        <button>+1</button>
    </div>
);

const App = () => (
    <div>
        <Counter count={0} />
        <IncButon />
    </div>
);

ReactDOM.render(<App />, document.getElementById('root'));
```

このあとは次の手順で進める。

* アクションのタイプを定義する
* ボタンに`onClick`を追加してアクションを呼ぶ
* アクションを受け取る`Store`を作る
* `Store`をビューに組み込む

## アクションのタイプを定義する

Fluxではアクションのタイプを定数で定義する。

今回は「インクリメントする」というアクションだけ。
（自習のネタとして「デクリメントする」とか「0にリセットする」みたいなタイプを追加するのもいいかも）

`src/action-types.js`というファイルを作って次の内容を書こう。

```js
export default {
    INCREMENT: Symbol()
};
```

`Symbol`はユニークでイミュータブルな値を作れる型で、こういったキー項目を表すのに最適だ。

## ボタンに`onClick`を追加してアクションを呼ぶ

次はボタンからアクションを呼ぶよにしてみよう。

アクションの中身はFluxの`Dispatcher`を呼び出すようにする。
`Dispatcher`というのは何かというと……説明が難しいな。
ええと、アクションを受け取って内部に保持している`Store`に対してディスパッチするものなんだけど、そんな言葉だけで理解出来たら苦労はしないよね。

まあ、とりあえず書きながら理解していこう。

`Dispatcher`はインスタンス化する必要がある。
`src/dispatcher.js`に次のコードを書こう。

```js
import { Dispatcher } from 'flux';

export default new Dispatcher();
```

この`Dispatcher`インスタンスを使ってアクションを渡す（？）起こす（？）発火する（？）わけだ。
（説明の言葉が難しい……あとで言い回しを修正するかも）

`src/actions.js`に次のコードを書こう。

```js
import CounterDispatcher from './dispatcher';
import ActionTypes from './action-types';

export const increment = event => {
    const value = 1;
    CounterDispatcher.dispatch({
        type: ActionTypes.INCREMENT,
        payload: { value }
    });
};
```

何をしているかというと、すごく単純で、`CounterDispatcher`と名付けた`Dispatcher`インスタンスの`dispatch`メソッドを呼び出している。

引数は`type`と`payload`を持っていて、`type`にはアクションのタイプを設定している。
`payload`はさらに`value`というプロパティを持っているが、今回は足しこむ数値をこのように`value`として渡すようにした。
（`payload`にはアクションの付加情報として自由に値を渡せて、何を渡すかはアプリケーションの設計次第だ）

## アクションを受け取る`Store`を作る

次はアクションを受け取る`Store`を作ろう。
`Store`はデータ（Fluxの文脈だと「状態」と呼ばれることが多いと思う）の入れ物だ。

`src/store.js`に次のコードを書こう。

```js
import { ReduceStore } from 'flux/utils';

import CounterDispatcher from './dispatcher';
import ActionTypes from './action-types';

class CountStore extends ReduceStore {
    getInitialState() {
        return 0;
    }
    reduce(state, { type, payload }) {
        switch (type) {
            case ActionTypes.INCREMENT: {
                const { value } = payload;
                return state.count + value;
            }
            default:
                return state;
        }
    }
}

export const countStore = new CountStore(CounterDispatcher);
```

少しずつ見ていこう。

まずクラスとメソッドの宣言に注目しよう。

```js
class CountStore extends ReduceStore {
    getInitialState() {
    }
    reduce(state, { type, payload }) {
    }
}
```

`CountStore`クラスは`ReduceStore`クラスを継承している。
`ReduceStore`はFluxが用意してくれている基底クラスだ。

メソッドは`getInitialState`と`reduce`の2つが定義されている。
これらは`ReduceStore`からオーバーライドしたものだ。

`getInitialState`は`Store`の初期状態を返すメソッドだ。
今回は単に`0`を返している。
つまりカウンターの初期値は`0`だということ。

```js
getInitialState() {
    return 0;
}
```

`reduce`はアクションを受け取ると呼び出されるメソッドで、現在の状態（`state`）とアクション（`{ type, payload }`）を引数に取って、新しい状態を返すメソッドだ。
現在の状態から新しい状態を導き出す処理は、アクションのタイプによって異なる。
アクションのタイプによる処理の振り分けには`switch`を使うのが良い。
今回は`INCREMENT`アクションの時に`payload`から取り出した`value`を現在の状態に足して新しい状態を導き出している。

なお、未知のアクションを受け取った場合は現在の状態をそのまま返すのが普通だ。

```js
reduce(state, { type, payload }) {
    switch (type) {
        case ActionTypes.INCREMENT: {
            const { value } = payload;
            return state + value;
        }
        default:
            return state;
    }
}
```

https://github.com/team-cerezo/react-sample/tree/flux
