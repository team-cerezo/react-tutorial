# Reactチュートリアル

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
