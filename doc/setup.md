# 環境構築

このチュートリアルで使うコマンドやおすすめするエディタなどを紹介する。

URIを記載しているだけのものはブラウザでURIを開いたらダウンロードとインストールの方法が書いているはず。
コマンドを記載しているものはそのコマンドを実行するとインストールされる。

## React

Reactから`create-react-app`というコマンドが提供されており、これを使うと簡単にReactアプリケーションの雛形を準備できる。

`create-react-app`はnpmのパッケージとして提供されているのでnpmをインストールする。
また、`create-react-app`で作られたプロジェクトはyarnを使うのでyarnもインストールする。

あっ、npmは企業ネットワークなどプロキシがある環境だとプロキシを越える設定をしなくてはならない。
[proxy環境下でのnpm config設定](https://qiita.com/tenten0213/items/7ca15ce8b54acc3b5719)を参考にして設定する。
もしくは環境変数`http_proxy`と`https_proxy`にプロキシのURIを設定してもよい（私はそうしている）。

`create-react-app`は`npm install`でインストールする。
プロジェクトを作るためのコマンドとして使うので`-g`オプションをつけてグローバルな環境にインストールする。

npm

* https://nodejs.org/ja/download/

yarn

* https://yarnpkg.com/ja/docs/install

create-react-app

```console
npm install -g create-react-app
```

## Chrome拡張

このチュートリアルではChromeで画面を確認しながら開発することを想定している。
Reactアプリケーション開発のサポートをしてくれるChrome拡張があるのでインストールするとよい。

React Developer Tools

* https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi

## エディタ

このチュートリアルでは特定のエディタに限定することはないが、Visual Studio Codeをおすすめする。

コード補完や定義へのジャンプ、シンボルの検索など便利な機能が使える。

Visual Studio Code

* https://code.visualstudio.com/

