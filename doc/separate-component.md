# コンポーネントを分割する

ざっくり書いた画面は`App`という1つのコンポーネントだけで構成されていた。
これを次のように分割したい。

* テキスト入力
* ToDo一覧
* 1つのTodo
* 完了Todoを消すボタン

## まずは何も考えずに分割

テキスト入力部分を分割してみよう。

```js
const InputContent = () => (
    <p>
        <input type="text" placeholder="やることある？" value={content}
            onChange={updateContent} onKeyPress={tryAddTodo} />
    </p>
);

const App = () => (
    <div>
        <h1>やること</h1>
        <InputContent />
        <ul>
            {todoList.list.map(todo => (
                <li key={todo.id}>
                    <label>
                        <input type="checkbox" checked={todo.done}
                            onChange={event => updateStatus(todo.id, event.target.checked)} />
                        <span>#{todo.id} {todo.content}</span>
                    </label>
                </li>
            ))}
        </ul>
        <p>
            <button onClick={clear}>終わったやつ消す</button>
        </p>
    </div>
);
```

`App`と同じように`InputContent`という変数にテキスト入力部を分割した。
`App`側では`div`や`p`といった要素と同じように`InputContent`をタグで追加している。

同じようにToDo一覧とボタンも分割しよう。

```js
const TodoListComponent = () => (
    <ul>
        {todoList.list.map(todo => (
            <li key={todo.id}>
                <label>
                    <input type="checkbox" checked={todo.done}
                        onChange={event => updateStatus(todo.id, event.target.checked)} />
                    <span>#{todo.id} {todo.content}</span>
                </label>
            </li>
        ))}
    </ul>
);

const ClearButton = () => (
    <p>
        <button onClick={clear}>終わったやつ消す</button>
    </p>
);

const App = () => (
    <div>
        <h1>やること</h1>
        <InputContent />
        <TodoListComponent />
        <ClearButton />
    </div>
);
```

`TodoList`クラスが既に存在するのでToDo一覧は`TodoListComponent`という名前にしてある。

## リストの要素を分割

次は1つのToDoを分割する。
これまでのように単純に分割すると`todo`を解決できずエラーになる。

```js
//これはエラーになるコード！
//"'todo' is not defined  no-undef"というエラーが出る！
const TodoComponent = () => (
    <li key={todo.id}>
        <label>
            <input type="checkbox" checked={todo.done}
                onChange={event => updateStatus(todo.id, event.target.checked)} />
            <span>#{todo.id} {todo.content}</span>
        </label>
    </li>
);
```

`todo`は`todoList.list`を`map`しているところで渡さなくてはいけない。
Reactでこのような値の受け渡しをするには`props`が使える。

`TodoComponent`は次のようになる。

```js
const TodoComponent = (props) => {
    const todo = props.todo;
    return (
        <li key={todo.id}>
            <label>
                <input type="checkbox" checked={todo.done}
                    onChange={event => updateStatus(todo.id, event.target.checked)} />
                <span>#{todo.id} {todo.content}</span>
            </label>
        </li>
    );
};
```

これまでJSXで書かれたコンポーネントは引数が0個のアロー関数で表現されていたが、`TodoComponent`は引数`props`を受け取っている。
この引数を経由してコンポーネントの親子間で値を受け渡せる。

`TodoComponent`に`todo`を渡すため、`TodoListComponent`は次のようにする

```js
const TodoListComponent = () => (
    <ul>
        {todoList.list.map(todo => (
            <TodoComponent todo={todo} />
        ))}
    </ul>
);
```

`TodoComponent`に`todo`属性を設定しているが、これが`TodoComponent`側で`props.todo`として取り出せる。

## TodoListComponentからTodoComponentの依存を切り離す

`TodoListComponent`は`todoList.list`の`map`で`TodoComponent`を使ってToDoリストを構築している。
これは`TodoListComponent`が`TodoComponent`に依存していることになるが、この依存は切り離せる。

`TodoListComponent`を次のように変更する。

```js
const TodoListComponent = (props) => (
    <ul>
        {props.children}
    </ul>
);
```

次に`App`を次のように変更する。

```js
const App = () => (
    <div>
        <h1>やること</h1>
        <InputContent />
        <TodoListComponent>
            {todoList.list.map(todo => (
                <TodoComponent todo={todo} />
            ))}
        </TodoListComponent>
        <ClearButton />
    </div>
);
```

これで`TodoListComponent`から`TodoComponent`への依存を切り離せた。
このようにコンポーネント間はできるだけ疎結合にして`App`のようなルートコンポーネントで構造化させる方法もあり、おそらくこのやり方のほうが拡張性、保守性は高いと考えている（まだ実践はしていないので、これまでの経験からくる勘）。

## 表示する値をpropsのみに依存させる

コンポーネント間の依存が解消できたので、次は参照している値に関する依存を解消しよう。

今のコードは`InputContent`が変数`content`、`updateContent`、`tryAddTodo`に依存している。
これはつまり`InputContent`の外側のスコープにそれらの変数が存在しなければいけないということだ。

これも`props`を使用することで解消する。
`InputContent`を次のように変更する。

```js
const InputContent = (props) => {
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
```

それから`App`を次のように変更する。

```js
const App = () => (
    <div>
        <h1>やること</h1>
        <InputContent
            content={content}
            updateContent={updateContent}
            tryAddTodo={tryAddTodo} />
        <TodoListComponent>
            {todoList.list.map(todo => (
                <TodoComponent todo={todo} />
            ))}
        </TodoListComponent>
        <ClearButton />
    </div>
);
```

先ほどのコンポーネント間の依存と同じように、各コンポーネントは外側のスコープに依存せず、構造化を担当する`App`が必要な値を渡すようにした。
こうすることでコンポーネントの独立性が高まり、依存関係を`App`に集約できた。

## ここまでのコード

同じように他のコンポーネントも独立させた。

ここまでのコードを次に示す。

```js
import React from 'react';
import ReactDOM from 'react-dom';

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
        return new TodoList(this.list.filter(todo => todo.done == false));
    }
}

//Hello, world!のときのmessageと同じ理由でletを使用している。
let todoList = TodoList.empty()
    .add(Todo.create('環境構築').setDone(true))
    .add(Todo.create('JavaScriptチュートリアル'))
    .add(Todo.create('Reactチュートリアル'));

//Hello, world!のときのmessageと同じ理由でletを使用している。
let content = '';

const updateContent = event => {
    content = event.target.value;
    render();
};

const tryAddTodo = event => {
    if (content !== '' && event.key === 'Enter') {
        const todo = Todo.create(content);
        todoList = todoList.add(todo);
        content = '';
        render();
    }
};

const updateStatus = (id, done) => {
    todoList = todoList.setDone(id, done);
    render();
};

const clear = event => {
    todoList = todoList.clear();
    render();
};

const render = () => {
    ReactDOM.render(<App />, document.getElementById('root'));
};

const InputContent = (props) => {
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

const TodoComponent = (props) => {
    const todo = props.todo;
    const updateStatus = props.updateStatus;
    return (
        <li key={todo.id}>
            <label>
                <input type="checkbox" checked={todo.done}
                    onChange={event => updateStatus(todo.id, event.target.checked)} />
                <span>#{todo.id} {todo.content}</span>
            </label>
        </li>
    );
};

const TodoListComponent = (props) => (
    <ul>
        {props.children}
    </ul>
);

const ClearButton = (props) => (
    <p>
        <button onClick={props.clear}>終わったやつ消す</button>
    </p>
);

const App = () => (
    <div>
        <h1>やること</h1>
        <InputContent
            content={content}
            updateContent={updateContent}
            tryAddTodo={tryAddTodo} />
        <TodoListComponent>
            {todoList.list.map(todo => (
                <TodoComponent
                    todo={todo}
                    updateStatus={updateStatus} />
            ))}
        </TodoListComponent>
        <ClearButton clear={clear} />
    </div>
);

render();
```
