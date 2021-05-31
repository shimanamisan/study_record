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

# SqlDataReader でのデータの取得と結果をカスタムクラスに入れる方法

- `ProductEntity`クラスを作成する

```c#
// ProductEntity.cs
// 継承しないクラスにはsealedをつけたほうが良い
public sealed class ProductEntity
{
    // Ctrl + Tabキー2回でコンストラクターが作れる
    // コンストラクターの引数は命名規約上、先頭の文字は小文字にする
    public ProductEntity(
        int productId,
        string productName,
        int price)
    {
        ProductId = productId;
        ProductName = productName;
        Price = price;
    }

    // select文で取得したものを格納する
    // 型はデータベースの型と合わせる
    public int ProductId  { get; }
    public string ProductName { get; }
    public int Price { get; }
    }

// ProductSQLServer.cs
// Readerの処理
public static List<ProductEntity> GetDataReader()
{
    // 改行が必要な場合は先頭に@マークをつける
    var sql = @"select
                ProductId,
                ProductName,
                Price from
                Product";

    var result = new List<ProductEntity>();
    using (var connection = new SqlConnection(_connectionString))
    using (var command = new SqlCommand(sql, connection))
    {
        // データベースに接続してSELECT文の実行結果を1行ずつ返す
        connection.Open();
        using (var reader = command.ExecuteReader())
        {
            // 1行ずつ結果を返す、結果がなくなれば処理をfalseを返す
            // 取得したデータは維持されないので変数に格納しないと消えて無くなる
            while(reader.Read())
            {
                // reader[0] クエリの実行で取得したデータは項目を追加したときにバグが発生するので
                // インデックスではなく、カラムの名前で取得する
                // 値はObject型で返ってくるので、必ずデータベースの型と合わせてConvertする
                // SQLServerのintとC＃のintはどちらも32ビット
                // SQLServerでBigintだったらC＃ではlongでConvertする
                // 整数はビット数に気を付ける

                result.Add(new ProductEntity(
                            Convert.ToInt32(reader["ProductId"]),
                            Convert.ToString(reader["ProductName"]),
                            Convert.ToInt32(reader["Price"])
                            ));
            }

            // SQLServer real →     C# float ToSingle()
            // SQLServer float →    C# double ToDouble()
            // SQLServer bigint →   C# long ToInt64()
            // SQLServer int →      C# long ToInt32()
            // SQLServer smallint → C# long ToInt16()
            // SQLServer tinyint →  C# long ToByte()
            // SQLServer varchar →  C# string ToString()

        }
    }

    return result;
}

// Form.cs
private void ReaderRead(object sender, EventArgs e)
{
    // データグリッドのデータソースに取得してきた値を代入する
    dataGridView1.DataSource = ProductSQLServer.GetDataReader();
}
```

# SqlCommand で Insert 文を発行する方法

```c#
// ProductSQLServer.cs
public static void Insert(ProductEntity products)
{
    string sql = @"insert into Product(ProductId,ProductName,Price) values(@ProductId,@ProductName,@Price)";

    using (var connection = new SqlConnection(_connectionString))
    using (var command = new SqlCommand(sql, connection))
    {
        connection.Open();

        // SQLの値を指定する
        command.Parameters.AddWithValue("@ProductId", products.ProductId);
        command.Parameters.AddWithValue("@ProductName", products.ProductName);
        command.Parameters.AddWithValue("@Price", products.Price);

        // Insertする場合はExecuteNonQueryでSQLコマンドのSQLが実行される
        command.ExecuteNonQuery();
    }
}

// Form.cs
private void InsertCommandButton_Click(object sender, EventArgs e)
{
    int productId = Convert.ToInt32(ProductIdTextBox.Text);
    string productName = Convert.ToString(ProductNameTextBox.Text);
    int price = Convert.ToInt32(PriceTextBox.Text);

    var entity = new ProductEntity(productId, productName, price);
    ProductSQLServer.Insert(entity);
}
```
