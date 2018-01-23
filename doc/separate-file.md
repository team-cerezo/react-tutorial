# ファイルも分割する

コンポーネントを分割したので、次はファイルも分割しよう。
ここでは次のような単位で分割する。

* モデル（`Todo`クラス、`TodoList`クラス）
* データ（`content`、`todoList`）
* アクション（`updateContent`, `tryAddTodo`, `updateStatus`, `clear`）
* 独立したコンポーネント（`InputContent`, `TodoComponent`, `TodoListComponent`, `ClearButton`）
* 構造化するコンポーネント（`App`）
* 描画関数（`render`）

## モデルを分割する

`Todo`クラスも`TodoList`クラスも他の単位への依存がない（つまり、モデルはデータやアクション・コンポーネントへ依存していない、ということ）のでモデルをファイルに分割するのは簡単だ。

次の内容を`src/models.js`に保存する。

```js
class Todo {
    constructor(id, content, done) {
        this.id = id;
        this.content = content;
        this.done = done;
    }
    setDone(done) {
        return new Todo(this.id, this.content, done);
    }
    static idGenerator = 0;
    static create(content) {
        return new Todo(++Todo.idGenerator, content, false);
    }
}

class TodoList {
    constructor(list) {
        this.list = list;
    }
    static empty() {
        return new TodoList([]);
    }
    add(todo) {
        return new TodoList([todo, ...this.list]);
    }
    setDone(id, done) {
        return new TodoList(this.list.map(todo => {
            if (todo.id === id) {
                return todo.setDone(done);
            }
            return todo;
        }));
    }
    clear() {
        return new TodoList(this.list.filter(todo => todo.done === false));
    }
}

export { Todo, TodoList };
```

クラス定義は`src/index.js`からそのまま持ってきた。

追加されているコードは最終行だ。

```js
export { Todo, TodoList };
```

これは`Todo`クラスと`TodoList`クラスを他のファイルからも使えるようにするためのコードだ。

`src/index.js`からは`Todo`クラスと`TodoList`クラスを定義していたコードがなくなり、代わりに`import`が追加される。

```js
import { Todo, TodoList } from './models';
```

## データを分割する

データの分割は少しだけ面倒だ。

次の内容を`src/data.js`に保存する。

```js
import { Todo, TodoList } from './model';

let todoList = TodoList.empty()
    .add(Todo.create('環境構築').setDone(true))
    .add(Todo.create('JavaScriptチュートリアル'))
    .add(Todo.create('Reactチュートリアル'));

let content = '';

const data = { todoList, content };

export default data;
```

データは`Todo`クラスと`TodoList`クラスに依存しているので`src/model.js`を`import`している。
それから`todoList`と`content`を1つのオブジェクトにまとめている。

ちなみに、オブジェクトリテラルはキーと値のペアを`,`で区切って設定するのだけど、キーを省略すると値が代入されている変数の名前をキーとみなしてくれる。
つまり次のコードは、

```js
const data = { todoList, content };
```

次のコードと等価だ。

```js
const data = { todoList: todoList, content: content };
```

この`data`を`export`している。

```js
export default data;
```

変数`content`と変数`todoList`を参照していた箇所は`data.`を付けるように修正する。

### exportとexport default

`data`の`export`には`Todo`クラスと`TodoList`クラスの`export`のときにはなかった`default`というキーワードが付いている。

`default`が付いているほうをデフォルトエクスポート、`default`が付いていないほうを名前付きエクスポートと呼ぶ。

デフォルトエクスポートは1ファイルにつき1つの値のみ`export`できる。
`import`するときはファイルを指定すればよい。

```js
export default 'hello world';

import Greeting from './hello';
```

名前付きエクスポートは1ファイルにつき複数の値を`export`できる。
`import`するときは名前を指定しなければいけない。

```js
const foo = 'FOO';
const bar = 12345;
export { foo, bar };
//宣言と同時にexportできる
export const baz = [1, 2, 3];

import { foo, bar, baz } from './foobar';
```

## アクションを分割する

アクションは再描画のために`render`関数を呼び出す。
つまり`render`関数を渡さないといけない。

次の内容を`src/actions.js`へ保存する。

```js
import { Todo } from './models';
import data from './data';

let render;

export const updateContent = event => {
    data.content = event.target.value;
    render();
};

export const tryAddTodo = event => {
    if (data.content !== '' && event.key === 'Enter') {
        const todo = Todo.create(data.content);
        data.todoList = data.todoList.add(todo);
        data.content = '';
        render();
    }
};

export const updateStatus = (id, done) => {
    data.todoList = data.todoList.setDone(id, done);
    render();
};

export const clear = event => {
    data.todoList = data.todoList.clear();
    render();
};

export default (h) => {
  render = h;
};
```

デフォルトエクスポートしている関数が`render`関数を設定するためのものだ。

アクションを使う側は次のように`import`した後に、

```js
import initRender, { updateContent, tryAddTodo, updateStatus, clear } from './actions';
```

`render`関数を渡す必要がある。

```js
initRender(render);
```

### 独立したコンポーネントを分割する

ファイル分割もかなり進んできた。
次は独立したコンポーネント（つまり`App`以外のもの）を分割する。

次の内容を`src/components.js`へ保存する。

```js
import React from 'react';

export const InputContent = (props) => {
    const content = props.content;
    const updateContent = props.updateContent;
    const tryAddTodo = props.tryAddTodo;
    return (
        <p>
            <input type="text" placeholder="やることある？" value={content}
                onChange={updateContent} onKeyPress={tryAddTodo} />
        </p>
    );
};

export const TodoComponent = (props) => {
    const todo = props.todo;
    const updateStatus = props.updateStatus;
    return (
        <li>
            <label>
                <input type="checkbox" checked={todo.done}
                    onChange={event => updateStatus(todo.id, event.target.checked)} />
                <span>#{todo.id} {todo.content}</span>
            </label>
        </li>
    );
};

export const TodoListComponent = (props) => (
    <ul>
        {props.children}
    </ul>
);

export const ClearButton = (props) => (
    <p>
        <button onClick={props.clear}>終わったやつ消す</button>
    </p>
);
```

## 構造化するコンポーネントを分割する

独立したコンポーネントを分割できたら次は構造化するコンポーネントだ。

次の内容を`src/component-structure.js`へ保存する。

```js
import React from 'react';

import data from './data';
import { updateContent, tryAddTodo, updateStatus, clear } from './actions';
import { InputContent, TodoListComponent, TodoComponent, ClearButton } from './components';

const App = () => (
    <div>
        <h1>やること</h1>
        <InputContent
            content={data.content}
            updateContent={updateContent}
            tryAddTodo={tryAddTodo} />
        <TodoListComponent>
            {data.todoList.list.map(todo => (
                <TodoComponent
                    key={todo.id}
                    todo={todo}
                    updateStatus={updateStatus} />
            ))}
        </TodoListComponent>
        <ClearButton clear={clear} />
    </div>
);

export default App;
```

## 描画関数

最後に描画関数まわりを整えよう。

次の内容を`src/index.js`へ保存する。

```js
import React from 'react';
import ReactDOM from 'react-dom';

import initRender from './actions';
import App from './component-structure';

const render = () => {
    ReactDOM.render(<App />, document.getElementById('root'));
};

initRender(render);

render();
```

https://github.com/team-cerezo/react-sample/tree/files
