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

ボタンから、このアクションを呼び出すようにしておこう。
`src/index.js`に次の`import`を追加して、

```js
import { increment } from './actions';
```

`IncButton`に`onClick`を足そう。

```js
const IncButon = () => (
    <div>
        <button onClick={increment}>+1</button>
    </div>
);
```

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

これで`Store`が出来た。

## `Store`をビューに組み込む

最後に`Store`をビューに組み込もう。
この手順を終えれば、`Store`に変更があればビューに通知されて勝手に`render`してくれるようになる。

`src/index.js`に次の`import`を足して、

```js
import { Container } from 'flux/utils';
import { countStore } from './store';
```

`App`で`Counter`に`0`を固定で渡していたところを`count`を渡すように変えて、

```js
const App = ({ count }) => (
    <div>
        <Counter count={count} />
        <IncButon />
    </div>
);
```

`App`が`Store`の変更を受け取るようにして、

```js
const getStores = () => [countStore];

const calculateState = () => {
    return { count: countStore.getState() };
};

const AppContainer = Container.createFunctional(App, getStores, calculateState)
```

`App`の代わりに`AppContainer`を`render`するように変更する。

```js
ReactDOM.render(<AppContainer />, document.getElementById('root'));
```

色々なことを一気にやりすぎた。
少しずつ見ていこう。

肝になるのは`App`が`Store`の変更を受け取るようにしたところだ。
`Container.createFunctional`にビュー（`App`）と、状態が変更されたときにビューに通知をする対象となる`Store`を返す関数（`getStores`）と、ビューが使用する`Store`を返す関数（`calculateState`）を渡して新たなビューを作成している（`AppContainer`）。

`getStores`は`Store`の配列を返すだけのアロー関数。
ここで返された`Store`の状態が変更されたとき、ビューに通知されてビューは`render`を呼び出すようになる。
つまり、この関数が自動で`render`を呼び出す仕組みの一旦を担っている。

`calculateState`はビューが使用する状態を返すアロー関数。
今回は`count`という名前で`countStore`が持つ状態を参照できるようにしている。
ここで設定された状態は`Container.createFunctional`に渡されるビュー（ここでは`App`）の`props`（つまりアロー関数の引数）として渡される。

最後に`src/index.js`の全体を掲載しておく。

```js
import React from 'react';
import ReactDOM from 'react-dom';
import { increment } from './actions';
import { Container } from 'flux/utils';
import { countStore } from './store';

const Counter = ({ count }) => (<h1>{count}</h1>);

const IncButon = () => (
    <div>
        <button onClick={increment}>+1</button>
    </div>
);

const App = ({ count }) => (
    <div>
        <Counter count={count} />
        <IncButon />
    </div>
);

const getStores = () => [countStore];

const calculateState = () => {
    return { count: countStore.getState() };
};

const AppContainer = Container.createFunctional(App, getStores, calculateState)

ReactDOM.render(<AppContainer />, document.getElementById('root'));
```

## カウンター完成＆課題

以上の手順をこなせばボタンを押すとカウントアップするものが出来たと思う。

ここでいくつか課題を出そう。

* 押すと値をカウントダウン（デクリメント）するボタンを追加してみよう
* 押すと値を`0`にリセットするボタンを追加してみよう
* どのタイプのアクションが呼び出されたのかを持つ`Store`を追加して、画面に履歴表示してみよう
* `todolist`にFluxを組み込んでみよう

なお、`todolist`のサンプルコードにはFluxを組み込んだものが既にあるので参考にしてみてね。

https://github.com/team-cerezo/react-sample/tree/flux
