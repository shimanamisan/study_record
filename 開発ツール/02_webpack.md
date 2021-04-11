# プラグイン編

## babel-loader や html-webpack-plugin を使ってみよう

- `@babel/core`：JavaScript を**[トランスパイル](https://qiita.com/Sekky0905/items/4515203428d9715fde5f)**するためのプラグイン
- `babel-loader` ：babel のローダー

```shell
npm install -D babel-loader @babel/core
```

## @babel/preset-env をインストールする

- `ES2015`以降の**シンタックス**を変換するためにインストールする

```shell
npm install -D @babel/preset-env
```

## @babel/preset-react をインストールする

- `React`では**JSX**という`React独自の文法`が存在しているため、`React`を使用する際の必須パッケージになっている
- `Reactの文法に特化したパッケージ`

```shell
npm install -D @babel/preset-react
```

## .babelrc ファイルを作成して、設定を記述する

```js
// .babelrc
{
    "presets": ["@babel/preset-env"]
}
```

- この設定ファイルに上記の内容を記述することで、`preset-env`が有効になる

## @babel/preset-react を有効にする

- `.babelrc`に以下のように追記する
- これで`JSX文法`がコンパイル出来るようになる

```js
{
    "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

## webpack.config.js に設定を記述する

- [公式サイトのステップ3](https://babeljs.io/setup#installation)のオブジェクトの内容をそのまま追加する

```js
// 中略
...
{
    test: /\.m?js$/, // 対象の拡張子を指定
    exclude: /node_modules/, // node_module内のファイルはトランスパイルの対象とはしない。自分でコーディングしたファイルを対象とするため
    use: {
        loader: "babel-loader", // babel-loaderでトランスパイルを実行する
        options: {
        presets: ['@babel/preset-env']
        }
    }
}
...
```

## react、react-domをインストールする
```shell
## オプションを付けづにdependenciesにインストールする
npm install react react-dom
```
- `react`：ライフサイクルフックなど、アプリケーションの根幹の機能
-  `react-dom`：DOMを生成する？

## html-loader、html-webpack-pluginをインストールする
- `html-loader`：`React`による**UIを埋め込むためのHTMLの雛形を利用するために必要なローダー**
- `html-webpack-plugin`：HTMLを生成するためのプラグイン
```shell
html-loader html-webpack-plugin
```

## CSSファイルを別ファイルにする
- `mini-css-extract-plugin`を利用してCSSファイルを別ファイルに出力する
```shell
npm install -D mini-css-extract-plugin
```
- 現状では`style-loader`によって、**styleタグにスタイリングが適応されている**
- `style-loader`は削除して、`mini-css-extract-plugin`を新しくインスタンスとして追加していく

### 設定方法
```js
// webpack.config.js

...
// CSSファイルを別ファイルへ出力するためのプラグイン
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

...
// 中略

{
    // 読み込んだローダーをどういったファイルを登録するのか、正規表現で記述する
    test: /\.(sc|c)ss$/, // scssもしくはcssというファイルに使用するローダーと関連付ける（正規表現に変更）
    // sass-loader:scssファイルを読み込むローダー
    // ローダーは配列内の最後の要素から順番に読み込まれる
    use: [
        MiniCssExtractPlugin.loader, // style-loaderから変更
        "css-loader",
        "sass-loader"
    ],
},

...
// 中略

// プラグインを利用するための設定
    plugins: [
    // プラグインのインスタンスを生成
    new HtmlWebpackPlugin({
        // 雛形ファイルを指定する
        template: "./src/index.html", // 雛形ファイルが配置されている場所
        filename: "./index.html", // 出力するファイル名
    }),

    // 今回追加したもの
    new MiniCssExtractPlugin({
        // [name]：デフォルトでmainという名前が使用される
        // [hash]：バンドル時にユニークな名前がつけられる
        filename: "[name].[hash].css"
    })
],
```

## uglifyjs-webpack-pluginでうっかり残してしまったconsole.logを削除する
- [uglifyjs-webpack-pluginのGitHub](https://github.com/webpack-contrib/uglifyjs-webpack-plugin)より、JavaScriptを圧縮するには[uglify-js](https://github.com/mishoo/UglifyJS)を使用するとある
```js
// webpack.config.js

...
// 中略

  // 最適化（webpack4から導入された）
  optimization: {
    // optimizationの設定の中のminimizerという設定にUglifyJsPluginインスタンスを渡す
    minimizer: [
      new UglifyJsPlugin({
        uglifyOptions: {
          compress: {
            drop_console: true
          },
        },
      }),
    ],
  },
```

## CSSの圧縮に特化したプラグインを導入する
- `optimize-css-assets-webpack-plugin`をインストールする
```js
npm install -D optimize-css-assets-webpack-plugin
```
- 設定は[公式ページのドキュメント▶Plugin](https://webpack.js.org/plugins/mini-css-extract-plugin/#minimizing-for-production)を参考にする

```js
// webpack.config.js

// CSSのファイルを圧縮するプラグイン
const OptimizeCssAssetsPlugin = require("optimize-css-assets-webpack-plugin");

...
// 中略

// 最適化（webpack4から導入された）
optimization: {
    // optimizationの設定の中のminimizerという設定にUglifyJsPluginインスタンスを渡す
    minimizer: [
        new UglifyJsPlugin({
        uglifyOptions: {
            compress: {
            drop_console: true
            },
        },
        }),
        new OptimizeCssAssetsPlugin({}),
    ],
},
```

## .gitignoreファイルにdist配下を含める
- 毎回ビルドすれば`dist`配下のファイルの内容は変化し出力されるのでリポジトリに含めないようにする

## ソースマップを作成して、エラーの追跡を出来るようにする
- エラーが発生した際のリンクをクリックすると、**エラーが発生しているソースの場所**がわかる


## eslintを導入してJavaScriptの静的解析をリアルタイムに実行する
### パッケージをインストールしていく
- `eslint`と`eslint-loader`をインストールする
```shell
npm install -D eslint eslint-loader
```
### eslintの初期化を行う
```shell
npx eslint --init
```
```shell
# その際に色々聞かれる


# .eslintrc.jsonが生成されるので編集していく
```

### .eslintrc.json
```json
{
    "env": {
        "browser": true,
        "es2021": true
    },
    "extends": [
        "plugin:react/recommended",
        "airbnb"
    ],
    "parserOptions": {
        "ecmaFeatures": {
            "jsx": true
        },
        "ecmaVersion": 12,
        "sourceType": "module"
    },
    "plugins": [
        "react"
    ],
    "rules": {
        // ファイル名にjsとjsxの拡張子を許容する
        "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx"] }]
    }
} 
```

## 構文テスト
```shell
# ソースファイルを指定
npx eslint ./src/index.js

# webpack.config.jsもチェックできる
npx eslint webpack.config.js

# --fix オプションをつけると、エラー箇所を自動で修正してくれる
# クォーテーションやコロン忘れなどがよくエラーになっている（読み込む構文ルールにもよる）
npx eslint --fix webpack.config.js
```