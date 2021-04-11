###### tags: `Larabel`

# Laravel コマンド関係

## ルート情報の一覧

```shell
php artisan route:list
+--------+----------+----------+------+---------+--------------+
| Domain | Method   | URI      | Name | Action  | Middleware   |
+--------+----------+----------+------+---------+--------------+
|        | GET|HEAD | /        |      | Closure | web          |
|        | GET|HEAD | api/user |      | Closure | api,auth:api |
+--------+----------+----------+------+---------+--------------+
```

## バージョン確認

```shell
php artisan --version
```

## ビルドインサーバの起動

```shell
php artisan serve
```

## マイグレーション関連

```shell
# マイグレーションの実行
php artisan migrate

# ロールバックの実行
php artisan migrate:rollback
```

## シーダーファイルの作成

```shell
php artisan make:seeder [シーダーファイル名]
```

## フォームリクエストの作成

```shell
php artisan make:request [リクエストファイル名]
```

## コントローラーの作成

```shell
php artisan make:controller [コントローラー名]
```

## モデルの作成

```shell
php artisan make:model [モデル名]
```

## テーブルの作成

- テーブル名の命名規則は何を作成するテーブルなのか分かりやすいものにしておく
- 例）`create_users_table`

```shell
php artisan make:migration [テーブル名]
```

## バッチ処理行うプログラムの作成

```shell
php artisan make:command [コマンド名]
```

## バッチ処理の実行

```shell
php artisan command:[コマンド名]

## 例では最後に引数を指定している）
php artisan command:getcoin week
```

## tinker コマンド

- ターミナル上から DB の情報が取得できているかテストできるコマンド

```shell
php artisan tinker
```

- 使い方

```shell
# 名前空間を指定してモデルを使えるようにする
use App\Coin;

# モデルを使用してDBからデータを取得
Coin::all();
=> Illuminate\Database\Eloquent\Collection {#4081
     all: [
       App\Coin {#4082
         id: 1,
         coin_name: "ビットコイン",
         max_price: "0",
         low_price: "0",
         created_at: "2020-06-26 13:17:46",
         updated_at: "2020-06-26 13:17:46",
       },
       App\Coin {#4083
         id: 2,
         coin_name: "イーサリアム",
         max_price: "0",
         low_price: "0",
         created_at: "2020-06-26 13:17:46",
         updated_at: "2020-06-26 13:17:46",
       },
       App\Coin {#4084
         id: 3,
         coin_name: "イーサリアムクラシック",
         max_price: "0",
         low_price: "0",
         created_at: "2020-06-26 13:17:46",
         updated_at: "2020-06-26 13:17:46",
       },
       App\Coin {#4085
         id: 4,
         coin_name: "リスク",
         max_price: "0",
         low_price: "0",
         created_at: "2020-06-26 13:17:46",
         updated_at: "2020-06-26 13:17:46",
       },
       App\Coin {#4086
         id: 5,
         coin_name: "ファクトム",
         max_price: "0",
         low_price: "0",
         created_at: "2020-06-26 13:17:46",
         updated_at: "2020-06-26 13:17:46",
       },
       App\Coin {#4087
         id: 6,
         coin_name: "リップル",
         max_price: "0",
         low_price: "0",
         created_at: "2020-06-26 13:17:46",
         updated_at: "2020-06-26 13:17:46",
       },
       App\Coin {#4088
         id: 7,
         coin_name: "ネム",
         max_price: "0",
         low_price: "0",
         created_at: "2020-06-26 13:17:46",
         updated_at: "2020-06-26 13:17:46",
       },
       App\Coin {#4089
         id: 8,
         coin_name: "ライトコイン",
         max_price: "0",
         low_price: "0",
         created_at: "2020-06-26 13:17:46",
         updated_at: "2020-06-26 13:17:46",
       },
       App\Coin {#4090
         id: 9,
         coin_name: "ビットコインキャッシュ",
         max_price: "0",
         low_price: "0",
         created_at: "2020-06-26 13:17:46",
         updated_at: "2020-06-26 13:17:46",
       },
       App\Coin {#4091
         id: 10,
         coin_name: "モナコイン",
         max_price: "0",
         low_price: "0",
         created_at: "2020-06-26 13:17:46",
         updated_at: "2020-06-26 13:17:46",
       },
       App\Coin {#4092
         id: 11,
         coin_name: "ステラルーメン",
         max_price: "0",
         low_price: "0",
         created_at: "2020-06-26 13:17:46",
         updated_at: "2020-06-26 13:17:46",
       },
       App\Coin {#4093
         id: 12,
         coin_name: "クアンタム",
         max_price: "0",
         low_price: "0",
         created_at: "2020-06-26 13:17:46",
         updated_at: "2020-06-26 13:17:46",
       },
     ],
   }

# リレーションを設定したテーブルからデータを取得してくる
Coin::find(1)->trends()-get();
=> Illuminate\Database\Eloquent\Collection {#4054
     all: [
       App\Trend {#4055
         id: 1,
         coin_id: 1,
         hour: "1210",
         day: null,
         week: null,
         created_at: "2020-06-26 15:33:31",
         updated_at: "2020-06-26 15:33:31",
       },
     ],
   }
```
