# 環境構築

このチュートリアルで使うコマンドやおすすめするエディタなどを紹介する。

URIを記載しているだけのものはブラウザでURIを開いたらダウンロードとインストールの方法が書いているはず。
コマンドを記載しているものはそのコマンドを実行するとインストールされる。

## React

Reactから`create-react-app`というコマンドが提供されており、これを使うと簡単にReactアプリケーションの雛形を準備できる。

`create-react-app`はnpmのパッケージとして提供されているのでnpmをインストールする。
"LTS推奨版"の"Windows Installer"を選んでダウンロードしてインストールする。
インストーラのファイル名がnpmじゃなくてnodeとなっているけれど大丈夫、npmはNode.jsに付属している。

**npmのインストール**

* https://nodejs.org/ja/download/

また、`create-react-app`で作られたプロジェクトはyarnを使うのでyarnもインストールする。
インストーラをダウンロードしてインストールする。

**yarnのインストール**

* https://yarnpkg.com/ja/docs/install

あっ、npmは企業ネットワークなどプロキシがある環境だとプロキシを越える設定をしなくてはならない。
[proxy環境下でのnpm config設定](https://qiita.com/tenten0213/items/7ca15ce8b54acc3b5719)を参考にして設定するとよい。
もしくは環境変数`http_proxy`と`https_proxy`にプロキシのURIを設定してもよい（私はそうしている）。

そもそも「npmとかyarnってなんなんだ」という話だけど、npmはNode.jsに付属しているパッケージマネージャ、つまりライブラリの依存関係を解決してくれるもので、Javaに例えるとMavenのようなもの。
それからyarnはnpmレジストリを使用するnpmとは別のパッケージマネージャで、yarnとnpmの関係はJavaに例えるとMavenとGradleの関係に近い。

さて、最後に`create-react-app`をインストールしよう。
`create-react-app`は`npm install`でインストールする。
プロジェクトを作るためのコマンドとして使うので`-g`オプションをつけてグローバルな環境にインストールする。

**create-react-appのインストール**

```console
npm install -g create-react-app
```

## Chrome

このチュートリアルではChromeで画面を確認しながら開発することを想定している。

もしChromeがインストールされていなければインストールしておこう。

**Chromeのインストール**

* https://www.google.co.jp/chrome/browser/desktop/index.html

## Chrome拡張

Reactアプリケーション開発のサポートをしてくれるChrome拡張があるのでインストールしておこう。

**React Developer Toolsのインストール**

* https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi

## エディタ

このチュートリアルでは特定のエディタに限定することはないけれど、Visual Studio Codeをおすすめ。

コード補完や定義へのジャンプ、シンボルの検索など便利な機能が使える。

**Visual Studio Codeのインストール**

* https://code.visualstudio.com/

