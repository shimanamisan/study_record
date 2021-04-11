###### tags: `Larabel`

# Laravel 関連エラーと解決方法

# ロリポップサーバではデフォルトのインデックス用文字列長を明示的に設定する

[公式ページ](https://readouble.com/laravel/6.x/ja/migrations.html#creating-tables)には、以下のように記述されています。インデックス長と M

> > Laravel はデータベース中への「絵文字」保存をサポートするため、デフォルトで`utf8mb4`文字セットを使っています。バージョン 5.7.7 より古い MySQL や、バージョン 10.2.2 より古い MariaDB を使用している場合、マイグレーションにより生成されるデフォルトのインデックス用文字列長を明示的に設定する必要があります。`AppServiceProvider`中で`Schema::defaultStringLength`を呼び出してください。

```php

// App\Providers\AppServiceProvider.php

/**
    * Bootstrap any application services.
    *
    * @return void
    */
public function boot()
{
    // マイグレーションにより生成されるデフォルトのインデックス用文字列長を
    // 明示的に設定する必要がる（MySQLは5.7未満で必要な設定）
    Schema::defaultStringLength(191);
}
```

# 桁数の多い数値がフロント側で切り取られる

# Method Illuminate\Database\Eloquent\Collection::update does not exist.
