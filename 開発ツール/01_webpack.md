# Webpack の練習

# 目次

1. [環境構築](#環境構築)
2. [webpackのインストール](#webpackのインストール)
3. [現時点でのpackage.jsonの確認](#現時点でのpackage.jsonの確認)

# 環境構築

### github でリポジトリを作成したらローカルにクローンファイルをダウンロードする

- 任意ディレクトリで下記のコマンドを実行
- 今回は`D:\webpack\`を実行環境とした

```shell
git clone https://github.com/shimanamisan/webpack-practice.git

# webpack-practiceディレクトリができるので移動
cd webpack-practice
```

```shell
# 現在のブランチを確認
git branch
* master # 現在はmasterブランチという意味
```

```shell
# webpack-sub-branchというブランチを切った
git checkout -b webpack-sub-branch

# 現在のブランチを確認
git branch
  master
* webpack-sub-branch # ブランチが移動していることが分かる
```

# webpackのインストール

```shell
# 各種モジュールのバージョンを確認していく(2020/02/09現在)
npm info webpack

webpack@4.41.5 | MIT | deps: 23 | versions: 632
Packs CommonJs/AMD modules for the browser. Allows to split your codebase into multiple bundles, which can be loaded on demand. Support loaders to preprocess files, i.e. json, jsx, es7, css, less, ... and your custom stuff.
https://github.com/webpack/webpack

bin: webpack

dist
.tarball: https://registry.npmjs.org/webpack/-/webpack-4.41.5.tgz
.shasum: 3210f1886bce5310e62bb97204d18c263341b77c
.integrity: sha512-wp0Co4vpyumnp3KlkmpM5LWuzvZYayDwM2n17EHFr4qxBBbRokC7DJawPJC7TfSFZ9HZ6GsdH40EBj4UV0nmpw==
.unpackedSize: 1.5 MB

dependencies:
@webassemblyjs/ast: 1.8.5                   enhanced-resolve: ^4.1.0                    neo-async: ^2.6.1
@webassemblyjs/helper-module-context: 1.8.5 eslint-scope: ^4.0.3                        node-libs-browser: ^2.2.1
@webassemblyjs/wasm-edit: 1.8.5             json-parse-better-errors: ^1.0.2            schema-utils: ^1.0.0
@webassemblyjs/wasm-parser: 1.8.5           loader-runner: ^2.4.0                       tapable: ^1.1.3
acorn: ^6.2.1                               loader-utils: ^1.2.3                        terser-webpack-plugin: ^1.4.3
ajv-keywords: ^3.4.1                        memory-fs: ^0.4.1                           watchpack: ^1.6.0
ajv: ^6.10.2                                micromatch: ^3.1.10                         webpack-sources: ^1.4.1
chrome-trace-event: ^1.0.2                  mkdirp: ^0.5.1

maintainers:
- jhnns <mail@johannesewald.de>
- sokra <tobias.koppers@googlemail.com>
- thelarkinn <sean.larkin@cuw.edu>

dist-tags:
latest: 4.41.5       legacy: 1.15.0       next: 5.0.0-beta.13  webpack-2: 2.7.0     webpack-3: 3.12.0

published a month ago by sokra <tobias.koppers@googlemail.com>

# latest: 4.41.5 このバージョンが最新版。上の方のwebpack@4.41.5をコピー

npm install -D webpack@4.41.5


# webpack-cliをインストールする(コマンドラインでwebpackを使用できるようにするパッケージ)
npm info webpack-cli
# webpack-cli@3.3.10

npm install -D webpack-cli@3.3.10
```

# 現時点でのpackage.jsonの確認

```shell
D:\webpack\webpack-practice> cat package.json
{
  "name": "webpack-practice",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "repository": {
    "type": "git",
    "url": "git+https://github.com/shimanamisan/webpack-practice.git"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "bugs": {
    "url": "https://github.com/shimanamisan/webpack-practice/issues"
  },
  "homepage": "https://github.com/shimanamisan/webpack-practice#readme",
  "devDependencies": {
    "webpack": "^4.41.5",
    "webpack-cli": "^3.3.10"
  }
}
```

# バンドル対象のファイルを作成

```shell
#
new-item index.html

#
mkdir src

#
new-item src/index.js
```

# ファイルを編集してく

```html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>webpack practice</title>
  </head>
  <body>
    <h1>webpack practice</h1>
  </body>
</html>
```

# ライブサーバーをインストールする

```shell
npm info live-server

npm install -D live-server@1.2.1
```

# ライブサーバを起動

```shell
.\node_modules\.bin\live-server
```

# 相対パスでなくともコマンドを探索して実行できる機能がある

```shell
# 以降はこれで実行していく
npx live-server
```

# lodash の CDN を読み込む

- [UNPKG](https://www.unpkg.com/)から CDN を読み込む
- URL の記述にルールがあるようだ → `unpkg.com/:package@:version/:file`
- https://www.unpkg.com/lodash と打ち込むと最新バージョンにリダイレクトされる。今回はミニファイされたファイルを使う
- こっちヘッドタグに読み込ませる：https://www.unpkg.com/lodash@4.17.15/lodash.min.js

# 内容をコミットする

```shell
# コミット時のコメントも指定
git commit -m '最初のミットです'

# リモートリポジトリへプッシュ
git push -u origin HEAD

# masterブランチへ一回戻る
git checkout -

# ブランチの変更をマージする
git merge --no-ff -
git: 'marge' is not a git command. See 'git --help'.

The most similar command is
        merge

# リモートにプッシュ
git push origin HEAD
```

# 今回の作業用にブランチを切る

- 作業フォルダを移動したので、そこへ移動する。`D:\programming\webpack\webpack-practice`
- 実行環境は`PowerSehll`

```shell
# 作業フォルダに移動
cd D:\programming\webpack\webpack-practice

#
git checkout -b getting-started-after-webpack

# バンドルしたファイルの出力先を作る
mkdir dist

# index.htmlをdist配下に移動する
git mv .\index.html dist/

#
git status
On branch getting-started-after-webpack
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
  renamed:    index.html -> dist/index.html

#
npm info lodash
lodash@4.17.15

# lodashはディベロップメント環境だけでなく、プロダクション環境でも使うので、--save オプションを付ける
npm install --save lodash@4.17.15
```

## webpack を実行する

- webpack は npm と同様に node_module 内に有るので、`npx`を頭につけて実行する

```shell
npx webpack
```

## webpack.config.js ファイルを作成する

- オプションなどの詳細はドキュメントに書かれている（https://webpack.js.org/configuration/#options）

```shell
# powershellでもtouchコマンド使える
# トップレベルのディレクトリでコンフィグファイルを作成する
touch webpack.config.js
```

- `モジュール化したwebpackの機能`を使用するために、`export`する必要がある

```js
// webpack.config.js

// 公式ドキュメントにも有るように、Node.jsにあるパスモジュールを読み込んでいる
// must be an absolute path (use the Node.js path module)
// https://webpack.js.org/configuration/#options
const path = require("path");

// resolve()は、絶対パスを生成するメソッド
// webpack.config.jsファイルが存在している現在のディレクトリ名と、distディレクトリを連結して絶対パスに変換している
const outputPath = path.resolve(__dirname, "dist");
console.log("これがoutputPathです：" + outputPath);

// モジュールにオブジェクトを設定する
module.exports = {
  // エントリーポイントの設定：モジュールをバンドルするための対象物を設定している
  entry: "./src/index.js",
  output: {
    // 出力するファイル名を設定
    filename: "main.js",
    // 絶対パスを指定
    path: outputPath,
  },
};
```

## ファイルが別の場所やファイル名だった場合は明示的にコンフィグファイルを指定する

- `コンフィグオプション`をつけることで、該当のコンフィグファイルを読み取ってバンドルする

```shell
npx webpack --config webpack.config.js
```

- モードオプションが設定されていないと出るワーニング
- `production（本番環境）`か`development（開発環境）`かモードを指定する

```shell
WARNING in configuration
The 'mode' option has not been set, webpack will fallback to 'production' for this value. Set
'mode' option to 'development' or 'production' to enable defaults for each environment.
You can also set it to 'none' to disable any default behavior. Learn more:
https://webpack.js.org/configuration/mode/
```

```shell
## モードオプションを指定したバンドル
npx webpack --mode development
```

## ここまでをコミットしていく

```shell
# すべてをコミットしていく
git add .

# stはstatusのエイリアス（短縮版）：windows版では使えなかった？
git status

# バンドルされているファイルはライブラリも含み、バンドル毎に変化するのでコミットに含めない
git reset dist/main.js

# .gitignoreファイルに dist/main.js を記載して監視するファイルからから除外する
git add .

# 変更点をレビュー
git diff --cached

# コミットする
git commit -m 'implement getting-next-webpack'
```

- ここでのコミット時にエラー
- ユーザーが不明らしい、リモートリポジトリへ登録しているユーザー名とメールアドレスを記載

```shell
*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.

fatal: unable to auto-detect email address (got 'root@UX21A-PC.(none)')
```

```shell
git config --global user.email "mikan.sup.all@gmail.com"
git config --global user.name "shimanamisan"
```

- 再び同じコミットをすると成功した、引き続きリモートへプッシュしていく

```shell
# リモートにプッシュ
git push -u origin HEAD

# プッシュできたらマスターブランチに戻る
git checkout -

# --no-ffというオプションを付けることで意図的にfast-forwardを行わないコミットをすることが出来る
git merge --no-ff - -m 'marge branch into master'

# 再びリモートへプッシュ
git push origin HEAD
```

# webpack-dev-server をインストールする

```shell
# ブランチを切る
git checkout -b webpack-dev-server

# バージョンの確認
npm info webpack-dev-server

# インストール実施
npm install webpack-dev-server@3.10.3

# サーバを立ち上げる（こっちはURLをコピペする必要がある）
npx webpack-dev-server

# 自動でデフォルトブラウザが起動するオプション
npx webpack-dev-server --open
```

## dist ディレクトリをサーバのドキュメントルートに設定する

- `devServer`という設定を追加する

```js
devServer: {
  contentBase: outputPath; // distディレクトリまでのフルパス
}
```

```js
// 追加後
module.exports = {
  // エントリーポイントの設定：モジュールをバンドルするための対象物を設定している
  entry: "./src/index.js",
  output: {
    // 出力するファイル名を設定
    filename: "main.js",
    path: outputPath,
  },
  devServer: {
    contentBase: outputPath, // distディレクトリまでのフルパス
  },
};
```

- `webpack`を導入しているなら大体は dev サーバも入っていることが多い

## package.json にスクリプトを追加する

```json
...
"scripts": {
	"start": "webpack-dev-serve --open"
},
```

## ここまでをコミットしていく

```shell
git add .

# 変更点をレビュー
git diff --cached

# コミットする
git commit -m 'install webpack-dev-server'

# コミットをリモートにプッシュ
git push -u origin HEAD

# マスターブランチに戻る
git cheackout -

# --no--ff をつけることで意図的にfast-fowardを行わないコミットをすることができる
git merge --no-ff - -m 'merge branch into master'

# 最後にリモートへプッシュ
git push origin HEAD
```

# 開発環境と本番環境の違い

- 開発環境：**Development**
- 本番環境：**Production**

# モジュールについて

- 他の JS で関数などをまとめたファイルをモジュールとして読み込ませるには、作成した関数の前に`export`をつけるだけ

```js
export function Nijou(num) {
  return num ** 2;
}
```

## export default とは？

- `export default`を指定しない場合は、読み込み側で何を読み込むのか**具体的に何をインポートするのか、インポート側で指定する必要がある**

```js
// utilities.js
// defaultでエクスポートされる
// どうやって読み込むのか？？
export default class Lion {
  static say() {
    return "がお～～";
  }
}

...
// index.js
// 読み込み側
import Lion from "./utilities";
```

# ローダー

- Webpack では JavaScript のファイル以外を JavaScript として扱うことを可能にする

## インポートした style.css を、console.log で見てみる

- css に記述した内容が JavaScript のオブジェクトとして扱われているのが確認できる

```js
// 配列になっている
Array(1)
0: Array(3)
0: "./src/style.css"
1: ".bg-body{
↵    background-color: #8dd6f9;
↵}"
2: ""
length: 3
__proto__: Array(0)
i: ƒ (modules, mediaQuery, dedupe)
toString: ƒ toString() // toStringメソッドを持っている

// console.log(style.toString())
.bg-body{``
    background-color: #8dd6f9;
}
```

- `style.css`を HTML に適応していくローダーが必要
- `style-loader`というローダーをインストールして、`webpack.config.js`に追加していく

## ローダーはチェーン接続すると、逆から読み込まれていく

- [公式のリファレンス](https://webpack.js.org/concepts/loaders/#loader-features)より、**A chain is executed in reverse order.**と書かれている
- ローダーの適応する順番を間違えるとエラーになる
- もし、`css-loader、style-loader`の順番で読みこんでいると、

```js
// webpack.config.js
...
// 抜粋
    rules: [
      {
        // 読み込んだローダーをどういったファイルを登録するのか、正規表現で記述する
        test: /\.css$/, // .cssというファイルに使用するローダーと関連付ける
        // 読み込むローダーを指定
        use: [
            "style-loader",
            "css-loader",
        ],
      },
    ],
  },

...
```

## url-loader を読み込む

- `url-loader`をインストールする
```js
...

    {
        // 拡張子の大文字も許容するように最後尾に i を加える
        // jpegとjpgの様にeがあるかないかを許容するのに、jpe?gという形式にする
        test: /\.(jpe?g|png|svg|gif|ico)$/i,
        use: ['url-loader']
    }
...
```

## index.js
```js
// test_imgオブジェクトで画像を読み込む
import test_img from "./img/01.jpg"

// 中略
...

// イメージオブジェクトを生成
const image = new Image();
image.src = test_img;
document.body.appendChild(image);
```

#### このやり方だと、`imgタグ`のソース属性に画像ファイルが`base64`でエンコーディングされているのでデータ量が多すぎる
![](/img/webpack-pwactice/webpack-practice_01.png)
- `file-loader`をつかうとよい。image ファイルを別ファイルとして扱うことが出来る

## file-loader の使い方
### url-loader 内で options を指定すると読み込める
- 他のローダーと読み込み方が違うので注意
```js
{
    // 拡張子の大文字も許容するように最後尾に i を加える
    // jpegとjpgの様にeがあるかないかを許容するのに、jpe?gという形式にする
    test: /\.(jpe?g|png|svg|gif|ico)$/i,
    loader: "url-loader",
    // optionsを指定することで、file-loaderを有効化することが出来る
    options: {
      // 指定のサイズを超過すると、画像が[name]で指定されたファイルに書き換わり独立する
      // あたかもimagesディレクトリ配下に存在しているかのように、別ファイルとして出力できる（Developerツールでも確認済み）
      limit: 1024, // サイズはKB
      name: "./images/[name].[ext]",
    },
},
```
#### ファイルの出力結果
![](/img/webpack-pwactice/webpack-practice_02.png)


# ここまでのソースコード一覧
```js
// index.js

import _ from "lodash";

// jsの拡張子は省略可能というルールがある
// import { NAME, Nijou } from "./utilities";

// utilities.jsをモジュールとして暫定的に読み込むやり方
// import * as utilities from "./utilities";

// 名前を変更して機能させるやりかた
// import { NAME as NAME_HAMU } from "./utilities";
// console.log(NAME_HAMU);

import Tigger from "./utilities"; // utilities.jsファイルを読み込む

// import style from "./style.css" 読み込むだけなら書き方は短縮できる
import "./style.css"; // jsファイルではないので拡張子までつける！
// style.cssがstyleオブジェクトという形で読み込まれる
// styleオブジェクトをコンソールログでみると、toString()というメソッドを持っている
// console.log(style.toString())

import "./style.scss"; // jsファイルではないので拡張子までつける！

// test_imgオブジェクトで画像を読み込む
import test_img from "./img/01.jpg"

// div要素を生成する関数を作成
function component() {
  const element = document.createElement("div");
  const array = ["Hello", "webpack", "!", Tigger.say()];
  element.innerHTML = _.join(array, " ");
  return element;
}

// bodyタグの子要素として、divタグを生成する関数を呼ぶ
document.body.appendChild(component());
// js側からクラスを追加する
document.body.classList.add('bg-body');

// イメージオブジェクトを生成
const image = new Image();
image.src = test_img;
document.body.appendChild(image);
```

```js
// utilities.js
export function Nijou(num) {
  return num ** 2;
}

export const NAME = "しまなみさん";

// defaultでエクスポートされる
// どうやって読み込むのか？？
export default class Lion {
  static say() {
    return "がお～～";
  }
}
```
```js
// webpack.config.js
// webpack.config.js

// 公式ドキュメントにも有るように、Node.jsにあるパスモジュールを読み込んでいる
// must be an absolute path (use the Node.js path module)
// https://webpack.js.org/configuration/#options
const path = require("path");

// resolve()は、絶対パスを生成するメソッド
// webpack.config.jsファイルが存在している現在のディレクトリ名と、distディレクトリを連結して絶対パスに変換している
const outputPath = path.resolve(__dirname, "dist");
console.log("これがoutputPathです：" + outputPath);

// モジュールにオブジェクトを設定する
module.exports = {
  // エントリーポイントの設定：モジュールをバンドルするための対象物を設定している
  entry: "./src/index.js",
  output: {
    // 出力するファイル名を設定
    filename: "main.js",
    // 絶対パスを指定
    path: outputPath,
  },
  // loaderを登録していく
  module: {
    // ruleは配列で登録する
    rules: [
      {
        // 読み込んだローダーをどういったファイルを登録するのか、正規表現で記述する
        test: /\.css$/, // .cssというファイルに使用するローダーと関連付ける
        // 読み込むローダーを指定
        use: ["style-loader", "css-loader"],
      },
      {
        // 読み込んだローダーをどういったファイルを登録するのか、正規表現で記述する
        test: /\.scss$/, // .cssというファイルに使用するローダーと関連付ける
        // sass-loader:scssファイルを読み込むローダー
        // ローダーは配列内の最後の要素から順番に読み込まれる
        use: ["style-loader", "css-loader", "sass-loader"],
      },
      {
        // 拡張子の大文字も許容するように最後尾に i を加える
        // jpegとjpgの様にeがあるかないかを許容するのに、jpe?gという形式にする
        test: /\.(jpe?g|png|svg|gif|ico)$/i,
        loader: "url-loader",
        // optionsを指定することで、file-loaderを有効化することが出来る
        options: {
          // 指定のサイズを超過すると、画像が[name]で指定されたファイルに書き換わり独立する
          // あたかもimagesディレクトリ配下に存在しているかのように、別ファイルとして出力できる（Developerツールでも確認済み）
          limit: 1024, // サイズはKB
          name: "./images/[name].[ext]",
        },
      },
      // babel-loaderを使用するための設定
      {
        test: /\.m?js$/, // トランスパイルの対象の拡張子を指定
        exclude: /node_modules/, // node_module内のファイルはトランスパイルの対象とはしない。自分でコーディングしたファイルを対象とするため
        use: {
          loader: "babel-loader",
          options: {
            presets: ["@babel/preset-env"],
          },
        },
      },
    ],
  },
  devServer: {
    contentBase: outputPath,
  },
};
```