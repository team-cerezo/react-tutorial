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

次の内容を`src/model.js`に保存する。

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
import { Todo, TodoList } from './model';
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
