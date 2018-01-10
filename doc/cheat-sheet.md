# チートシート

コマンドやショートカットの一覧。

チュートリアルで使うものから使ってはいないけど便利なものを掲載。

## コンソール

### アプリケーションの作成

```console
create-react-app todolist
```

`todolist`はアプリケーションの名前。

### 開発サーバー起動

```console
yarn start
```

http://localhost:3000/ でサーバーが起動する。

### ビルド

```console
yarn build
```

`./build`に出力される。

### 依存ライブラリの準備

```console
yarn install
```

`./node_modules`に必要なパッケージがインストールされる。

プロジェクトを`git clone`した直後や`git clean -xdf .`でカレントディレクトリ以下を綺麗にしたあとなどに`yarn install`をすることがある。

## Visual Studio Code

### 定義へ移動

```
F12
```

### ファイルを検索して移動する

```
ctrl + P
```

### ファイルのシンボルを検索して移動する

```
ctrl + shift + O
```

別タブで開いているファイルも含めて検索する場合は次のショートカット。

```
ctrl + T
```

### 複数のファイルの中身を検索する

```
ctrl + shift + F
```

### ターミナルを開く・閉じる

```
ctrl + @
```

### コマンドを実行する

```
ctrl + shift + P
```

### Markdownをプレビューする

```
ctrl + shift + v
```

## Git

### 検索する

`foo`という文字列を検索する。

```console
git grep foo
```

大文字小文字を無視する。

```console
git grep -i foo
```

正規表現で検索する。

```console
git grep -e "fo*ba[rz]"
```

`foo`と`bar`を含む行を検索する（論理積）。

```console
git grep -e foo --and -e bar
```

`foo`または`bar`を含む行を検索する（論理和）。

```console
git grep -e foo --or -e bar
```

### Gitで管理しているファイルを一覧する

```console
git ls-files
```
