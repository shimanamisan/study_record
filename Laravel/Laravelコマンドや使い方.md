###### tags: `Larabel`

# Laravel コマンドや使い方

# 00.Larabel 基礎や処理フロー

1. Composer とは PHP 用のパッケージ管理ソフト。Laravel をインストールするにはまずこれが必要
2. symfony というフレームワークをもとに作られている
3. マイグレーションとは、データベースへの変更をフレームワーク上でファイルとして管理する機能

# Laravel の処理フロー

1. URL にアクセスした際に、POST メソッドや GET メソッド等のアクセスなのか判断し、コントローラーのアクションを呼び出す（ルーティング処理）
2. コントローラーのアクションには、どういうデータを DB から取ってるくのか等の処理が書かれており、取り出したデータを View（画面描画処理）へ渡す
3. 渡されたデータをもとに、ブレードと呼ばれるテンプレートファイルを使用して画面を描画していく
4. DB へのアクセスはモデルを通して行う。モデルでは ORM を利用してテーブル同士のリレーションを張って便利に利用できる

## ディレクトリ構造

- 全部覚える必要はないが、開発でよく使うディレクトリはおさえておく
- app
  - /Http
    - /Controllers：MVC でいう C（コントローラー）ファイルの置き場
- /config ：アプリケーションの各種設定ファイル置き場
- /database：マイグレーションと初期値設定（シーディング）のファイル置き場
  - /factorys
    - /migrations
- /public：アセット（画像、JavaScript、CSS など）ファイルの置き場。ここのファイルが公開ファイルになる
- /resources：ビューやアセットの元ファイル（LESS、SASS、JavaScript）を置く場所
  - /views：MVC でいう V（ビュー）のファイルを置く場所。テンプレートファイルなどが配置されている
- /routes
  - /web：ルーティング用の設定ファイル
- /storage：コンパイルされた Blade テンプレート、ファイルベースのセッション、ファイルキャッシュなど、フレームワークにより生成されるファイルが保存される
  - /logs：ログファイルの置き場所

### 補足

- resource フォルダの中のものがコンパイルされて public フォルダのものが実際のアプリで使われる

## XAMMP でコマンドラインからデータベースを作成する

- 環境変数に`C:\xampp\mysql\bin`のパスを通しておく
- mysql コマンドでログインできる（XAMMP はデフォルトだと ユーザ名:root、pass は ' ' 空文字）
- コマンドの文末にはセミコロンを忘れない。忘れると命令がずっと実行されている状態になる -> こんなやつずっと出てる

## デバッグ処理

- Laravel ではエラーや例外処理は既に設定済みのため、DB 接続する際にわざわざ書いていた try,catch も必要ない。
- アプリケーションエラー発生時にユーザーにどの程度の情報を表示させるかは、`config/app.php` 設定ファイルの `debug 設定オプション` で決定する。
- デフォルト状態でこの設定オプションは、`.env` ファイルで指定される `APP_DEBUG 環境変数`の値を反映する。
- **本番 UP 時には必ず APP_DEBUG は false にすること！！**

## テーブル設計

- ユーザー名：String 型はフレームワークの方で管理する型で、実際 Mysql では Varchar 型でカラムを作る？
- email_verified_at は Laravel の Email 認証の機能を使う際に必要なカラム（今回は使わない）
- password は Laravel のデフォルトの機能では、60 文字以上は必要になる（カラムの文字数を変えるときは注意が必要。基本 255 文字で OK）
- 今回は問題数は 10 問と固定だが、本来はイケてない設計。後から追加するには、ドリルと問題を別テーブルにして ID で紐付けると良い？
- 参考：[Laravel でプログラミング学習用タイピングサービスを作ろう！ - HackMD](https://hackmd.io/@72rg5ZuITwqI78OrJnVIOg/SJGofaJ3V)

# bigincrement と increment の違い

- `bigincrement`は`increment`とよりも割り振りできる id の範囲が広いが、それだけ容量を食う。インデックスを作成するときも、範囲が広いせいで少し遅い
- 特に不具合がなければ`increment`のほうが良さそう

# モデル、コレクションの違い

# カラム追加用のマイグレーションファイルを作成する

- カラムを追加するマイグレーションファイルを作成する
- 今回は、**TaskApp の Crad テーブルに priority カラムを追加する**
- 既存のテーブルに変更を加える場合は、**`--create`オプションではなく`--table`オプションを使ってレーブル名を指定する**

```shell
php artisan make:migration add_priority_to_cards --table=cards
```

## ファイルの中身

```php
<?php

    public function up()
    {
        Schema::table('cards', function (Blueprint $table) {
            $table->integer('priority')->nullable()->comment('ソート機能用');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::table('cards', function (Blueprint $table) {
            $table->dropColumn('priority');
        });
    }

```
