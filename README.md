# Reactチュートリアル

シンプルなToDo管理アプリケーションを作りながらReactを学ぶチュートリアル。

## チュートリアルの進め方

ドキュメントを読みながらコードを書く。
コードのサンプルは https://github.com/team-cerezo/react-sample にある。

ドキュメントは後述するようにGitBookで見られるようにしている。

GitHubで`.md`ファイルを直接見てもよい。
目次は[SUMMARY.md](./doc/SUMMARY.md)。

## このチュートリアルの開発について

### ドキュメントをGitBookで見る

ドキュメントはGitBookで見られる。

まずGitBookをインストールする。

```console
npm install -g gitbook-cli
```

次に`doc`ディレクトリに移動してから`gitbook serve`を実行する。

```
cd doc
gitbook serve
```

http://localhost:4000/ を開くとドキュメントが見られる。

### 文章を校正する

textlintで文章の校正ができるように設定している。
`package.json`にscriptを書いているので次のyarnコマンドでtextlintを実施できる。

```console
yarn textlint
```
