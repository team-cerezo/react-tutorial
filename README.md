# [WIP]Reactチュートリアル

シンプルなToDo管理アプリケーションを作りながらReactを学ぶチュートリアル。

## チュートリアルの進め方

ドキュメントを読みながらコードを書く。
コードのサンプルは https://github.com/team-cerezo/react-sample にあるので適宜参照してほしい。

ドキュメントは後述するようにGitBookで見られるようにしているけれど、GitHubで`.md`ファイルを直接見ても大丈夫。
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

## 利用に際して

当ドキュメントの無断転載及び複製等の行為はご遠慮ください。
（プルリクエストのためのforkは除く）

