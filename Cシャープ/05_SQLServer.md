###### tags: `C#`

# SQLServer と C# をつなぐ

# SQLServer のインストール

1. [https://www.microsoft.com/ja-jp/sql-server/sql-server-2019](https://www.microsoft.com/ja-jp/sql-server/sql-server-2019)にアクセス
2. 「今すぐ試す」→「Express 版」をクリックしてダウンロード → インストール
3. [SSMI の日本語版をインストール](https://docs.microsoft.com/ja-jp/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15#available-languages)（デフォルトだと英語版がインストールされるので注意）

# データベースの作成

# クエリを作成する

- 作成した DB 右クリック → 新しいクエリ

```sql
select * from Product
# F5キーで実行出来る
```

# C#側から SQLServer に接続してデータテーブルにデータを表示する

- ツールボックスで画面にボタンとデータテーブルを作成しておく
- `using`に追加するには、`ctrl + .`で一覧を表示して Enter キーで追加出来る
- SqlDataAdapter の`Fillメソッド`は、全てのデータが取得されるまで結果を返さず待機している

```c#
using System.Data.SqlClient; // 追加するクラス

private void DatabaseRead(object sender, EventArgs e)
{
    // 接続情報を作成する
    var builder = new SqlConnectionStringBuilder();
    builder.DataSource = @"HOME-SERVER-01\SQLEXPRESS"; // バックスラッシュがある場合は文字列の先頭に@をつける
    builder.InitialCatalog = "My_DB"; // DB名
    builder.IntegratedSecurity = true; // Windows認証の場合
    //builder.UserID = ""; // SQLServer認証の場合はIDとPassを入力し、IntegratedSecurityをFalseにする

    // SQLSeverの接続するために必要な文字列が生成される
    var connectionString = builder.ToString();

    var sql = @"select * from Product";

    DataTable dt = new DataTable();

    // Disposeが呼び出せる処理はusingを使用してエラー時には接続が閉じるようにする
    // 処理の閉じ忘れを防止して、メモリーの消費を防ぐため
    using (var connection = new SqlConnection(connectionString))
    using (var adapter = new SqlDataAdapter(sql, connection)) // SQLと接続情報を渡す
    {
        connection.Open();
        // SQLが発行されて実行結果がdtに格納される
        adapter.Fill(dt);
    }

    // 実行結果を画面に表示させる
    dataGridView1.DataSource = dt;
}
```

# Sql アクセスクラスを作成

- データベースに接続する処理を共通クラスに記述する
- プロジェクト名を右クリック → 追加 → 新しいフォルダー
- 作成したフォルダーを右クリック → 追加 → 新しい項目 → クラスを選択して`ProductSQLServer`というクラスを作成

```c#
// ProductSQLServer.cs

public static class ProductSQLServer
{

    private static string _connectionString;

    // このクラスにアクセスしようとしたときに最初に動作するコンストラクタを作成
    static ProductSQLServer()
    {
        var builder = new SqlConnectionStringBuilder();
        builder.DataSource = @"HOME-SERVER-01\SQLEXPRESS"; // バックスラッシュがある場合は文字列の先頭に@をつける
        builder.InitialCatalog = "My_DB"; // DB名
        builder.IntegratedSecurity = true; // Windows認証の場合
        _connectionString = builder.ToString();
    }

    public static DataTable GetDataTable()
    {
        var sql = @"select * from Product";

        DataTable dt = new DataTable();

        // Disposeが呼び出せる処理はusingを使用してエラー時には接続が閉じるようにする
        // 処理の閉じ忘れを防止して、メモリーの消費を防ぐため
        using (var connection = new SqlConnection(_connectionString))
        using (var adapter = new SqlDataAdapter(sql, connection)) // SQLと接続情報を渡す
        {
            connection.Open();
            // SQLが発行されてDataTableに結果が入る
            adapter.Fill(dt);
        }

        return dt;
    }
}


// 共通処理をProductSQLServerクラスに移行した後のボタンの処理
using SQLServer_Lesson.SQLServer; // クラスを読み込む

private void DatabaseRead(object sender, EventArgs e)
{
    // 実行結果を画面に表示させる
    dataGridView1.DataSource = ProductSQLServer.GetDataTable();
}
```

# SqlDataReaderでのデータの取得

```c#
```