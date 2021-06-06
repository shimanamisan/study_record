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

## アプリケーション全体で例外を受ける

- 今はテキストボックスが空の状態で Insert すると例外が発生する
- `Program.cs`のクラス内で例外を受ける処理を実装する
- 本当はバリデーションチェックを行う必要があるが、こういう方法もある

```c#
// Program.cs

static class Program
{
    /// <summary>
    /// アプリケーションのメイン エントリ ポイントです。
    /// </summary>
    [STAThread]
    static void Main()
    {
        // まとめて一括して例外を受け取る
        Application.ThreadException += Application_ThreadException; // 追加したコード

        Application.EnableVisualStyles();
        Application.SetCompatibleTextRenderingDefault(false);
        Application.Run(new Form1());
    }

    // まとめて一括して例外を受け止めている
    // 追加したコード
    private static void Application_ThreadException(object sender, System.Threading.ThreadExceptionEventArgs e)
    {
        MessageBox.Show(e.Exception.Message);
    }
}
```

# SqlCommand で Update 文を発行する方法

- 処理はほぼ Insert する時と同じ
- Update するレコードがなかったら Insert も行う

```c#
// ProductSQLServer.cs
public static void Update(ProductEntity products)
{

    string sql = @"update Product set ProductName=@ProductName, Price=@Price where ProductId=@ProductId";

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

        // ExecuteNonQueryの実行結果でUpdateされたレコードの件数が返ってくる
        // それが0件だったらInsertする処理を追加
        if (updateCount < 1)
        {
            Insert(products);
        }
    }
}

// Form.cs
private void UpdateCommandButton_Click(object sender, EventArgs e)
{
    int productId = Convert.ToInt32(ProductIdTextBox.Text);
    string productName = Convert.ToString(ProductNameTextBox.Text);
    int price = Convert.ToInt32(PriceTextBox.Text);

    var entity = new ProductEntity(productId, productName, price);
    ProductSQLServer.Update(entity);
}
```

# SqlCommand で Delete 文を発行する方法

```c#
// ProductSQLServer.cs
public static void Delete(int producId)
{

    string sql = @"delete Product where ProductId=@ProductId";

    using (var connection = new SqlConnection(_connectionString))
    using (var command = new SqlCommand(sql, connection))
    {
        connection.Open();

        // SQLの値を指定する
        command.Parameters.AddWithValue("@ProductId", producId);

        // Insertする場合はExecuteNonQueryでSQLコマンドのSQLが実行される
        command.ExecuteNonQuery();
        // 何件削除したかメッセージなどで表示させたければcount変数を作って戻り値を受けてもよい

    }
}

// Form.cs
private void DeleteCommandButton_Click(object sender, EventArgs e)
{
    int productId = Convert.ToInt32(ProductIdTextBox.Text);

    ProductSQLServer.Delete(productId);
}
```

# Dapper でデータを取得する方法

- 基本的にReaderを使ったやり方とほぼ同じ
- SQLは書くけど、実行結果を取得する処理を自動でやってくれる
- Readerでは実行結果を取得する処理も自分で実装するが、行数が多いと大変なのでその辺りを自動でやってくれるのがDapperという機能
- ソリューションエクスプローラーの「参照」を右クリック → NuGetパッケージの管理 → Dapperと検索

```c#
// ProductSQLServer.cs
using Dapper; // 追記する

public static List<ProductEntity> GetDapper()
{
    // 改行が必要な場合は先頭に@マークをつける
    var sql = @"select
                ProductId,
                ProductName,
                Price from
                Product";

    using (var connection = new SqlConnection(_connectionString))
    {
        // SQLを実行してProductEntityの型にマッピングして値を返してくれる
        // Queryメソッドに合わせてF12キーを押すとDapperクラスに飛べる
        return connection.Query<ProductEntity>(sql).ToList();
    }

}

// Form.cs
private void DapperReadButton_Click(object sender, EventArgs e)
{
    dataGridView1.DataSource = ProductSQLServer.GetDapper();
}
```

# DapperでInsertする方法

- UpdateやDeleteも同じようなやり方で実装できる

```c#
// ProductSQLServer.cs

public static void DapperInsert(ProductEntity products)
{
    string sql = @"insert into Product(ProductId,ProductName,Price) values(@ProductId,@ProductName,@Price)";

    using (var connection = new SqlConnection(_connectionString))
    {
        connection.Execute(sql, new { products.ProductId, products.ProductName, products.Price });
    }
    
// Form.cs
private void DapperInsertButton_Click(object sender, EventArgs e)
{
    int productId = Convert.ToInt32(ProductIdTextBox.Text);
    string productName = Convert.ToString(ProductNameTextBox.Text); 
    int price = Convert.ToInt32(PriceTextBox.Text);

    var entity = new ProductEntity(productId, productName, price);
    ProductSQLServer.DapperInsert(entity);
}
```

# EntityFrameworkでデータを取得する

- EntityFrameworkをインストールする
- SQLを書かなくてもC#の記述だけで実行することが出来る

## EntityFrameworkをインストールする

- NuGetパッケージ管理 → EntityFrameworkと検索してインストール
- プロジェクト名を右クリック → 追加 → 新しいフォルダー → 今回は**Models**とする
- Modelsフォルダを右クリック → 追加 → 新しい項目 → 左のメニューから「データ」を選択 → `ADO.NET Entity Data Model`を選択して追加 → 「データベースからCode First」を選択して次へ
- 新しい接続 → データソースで「Microsoft SQL Server」を選択して続行 → サーバー名を指定（今回は `HOME-SERVER-01\SQLEXPRESS` を指定）
- データベース名の選択または入力 → プルダウンから作成したDBを指定する（今回はMy_DB名前で作っている）
- 接続名に名前をつけてApp.Configに保存 → 「DbContext」と名前をつける → 次へ
- テーブルにチェックを入れる → 生成されたオブジェクトの名前を複数化または単数化する（リストを扱う変数を返してくれるが、データが1行の場合は単数形の変数名で返却して、複数行ある場合は複数形の変数名にしてデータを返してくれる） → 完了
- 実行時、データ取得まで少し時間がかかる

```c#
// Form.cs

using SQLServer_Lesson.Models;

private void EFReadtButton_Click(object sender, EventArgs e)
{
    // EntityFrameworkで自動的に生成されたリストを使用する
    var source = new List<Product>();

    // 接続はApp.Configにつながる
    // connectionString="接続先"となっているので接続先を変更したければここを変更する
    using (var db = new Model1())
    {
        source.AddRange(db.Products);
    }

    dataGridView1.DataSource = source;
}
```

# EntityFrameworkでInsertする

```c#
// Form.cs
private void EFInsertButton_Click(object sender, EventArgs e)
{
    // EntityFrameworkで自動生成されたクラスに格納する
    Product p = new Product();

    p.ProductId = Convert.ToInt32(ProductIdTextBox.Text);
    p.ProductName = Convert.ToString(ProductNameTextBox.Text);
    p.Price = Convert.ToInt32(PriceTextBox.Text);

    using (var db = new Model1())
    {
        db.Products.Add(p);
        // データベースに保存するときは必ず実行する
        db.SaveChanges();
    }
}
```

# EntityFrameworkでUpdateする

- テクストボックスに入力されたIDを元にデータを検索してアップデートする

```c#
// Form.cs

private void EFUpdateButton_Click(object sender, EventArgs e)
{
    using (var db = new Model1())
    {
        // IDをキーにしてデータを取得する
        var p = db.Products.Find(Convert.ToInt32(ProductIdTextBox.Text));

        p.ProductName = ProductNameTextBox.Text;
        p.Price = Convert.ToInt32(PriceTextBox.Text);
        db.SaveChanges();
    }
}
```

# EntityFrameworkでDeleteする

- 行っていることはUpdateの時と同じ

```c#
private void EFDeleteButton_Click(object sender, EventArgs e)
{
    using (var db = new Model1())
    {
        // Updateと同様、IDからレコードを取得する処理を記述する
        var p = db.Products.Find(Convert.ToInt32(ProductIdTextBox.Text));
        db.Products.Remove(p);
        db.SaveChanges();
    }
}
```

# Helperクラスを作ってSqlCommand操作を共通化する

- ProductSQLServerクラスに書かれている、データベースへの接続処理などは共通化することが出来る
- SqlServerHelperクラスを作成

```c#
// SqlServerHelper.cs

class SqlServerHelper
{
    // リファクタリングによりProductSQLServerクラスより移動
    internal static string ConnectionString { get; }

    static SqlServerHelper()
    {
        // リファクタリングによりProductSQLServerクラスより移動
        var builder = new SqlConnectionStringBuilder();
        builder.DataSource = @"HOME-SERVER-01\SQLEXPRESS"; // バックスラッシュがある場合は文字列の先頭に@をつける
        builder.InitialCatalog = "My_DB"; // DB名
        builder.IntegratedSecurity = true; // Windows認証の場合
        ConnectionString = builder.ToString();
    }

    // リファクタリングによりProductSQLServerクラスより移動
    public static DataTable GetDataTable(string sql)
    {
        DataTable dt = new DataTable();

        // Disposeが呼び出せる処理はusingを使用してエラー時には接続が閉じるようにする
        // 処理の閉じ忘れを防止して、メモリーの消費を防ぐため
        using (var connection = new SqlConnection(ConnectionString))
        using (var adapter = new SqlDataAdapter(sql, connection)) // SQLと接続情報を渡す
        {
            connection.Open();
            // SQLが発行されてDataTableに結果が入る
            adapter.Fill(dt);
        }

        return dt;
    }

    // リファクタリングによりProductSQLServerクラスより移動
    // 値を返すのではなくActionを使ってSqlDataReaderを通知するだけにする
    public static void Query(string sql, Action<SqlDataReader> action)
    {

        var result = new List<ProductEntity>();
        using (var connection = new SqlConnection(ConnectionString))
        using (var command = new SqlCommand(sql, connection))
        {
            // データベースに接続してSELECT文の実行結果を1行ずつ返す
            connection.Open();
            using (var reader = command.ExecuteReader())
            {
                // 1行ずつ結果を返す、結果がなくなれば処理をfalseを返す
                // 取得したデータは維持されないので変数に格納しないと消えて無くなる
                while (reader.Read())
                {
                    // reader[0] クエリの実行で取得したデータは項目を追加したときにバグが発生するので
                    // インデックスではなく、カラムの名前で取得する
                    // 値はObject型で返ってくるので、必ずデータベースの型と合わせてConvertする
                    // SQLServerのintとC＃のintはどちらも32ビット
                    // SQLServerでBigintだったらC＃ではlongでConvertする
                    // 整数はビット数に気を付ける

                    // SqlDataReaderで取得したものを通知する
                    action(reader);
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
    }

    public static int Execute(string sql, List<SqlParameter> sqlParameters)
    {
        
        using (var connection = new SqlConnection(ConnectionString))
        using (var command = new SqlCommand(sql, connection))
        {
            connection.Open();

            if (sqlParameters != null)
            {
                // Listになっているので、配列に直してAddする必要がある
                command.Parameters.AddRange(sqlParameters.ToArray());
            }

            // Insertする場合はExecuteNonQueryでSQLコマンドのSQLが実行される
            return command.ExecuteNonQuery();
        }
    }
}
```

## 呼び出す側も修正

- データベースへの接続処理は共通なので全て`SqlServerHelperクラス`へ移動

```c#
public static class ProductSQLServer
{

    // private static string _connectionString;

    // このクラスにアクセスしようとしたときに最初に動作するコンストラクタを作成
    static ProductSQLServer()
    {
        // リファクタリングによりProductSQLServerクラスへ移動

        //var builder = new SqlConnectionStringBuilder();
        //builder.DataSource = @"HOME-SERVER-01\SQLEXPRESS"; // バックスラッシュがある場合は文字列の先頭に@をつける
        //builder.InitialCatalog = "My_DB"; // DB名
        //builder.IntegratedSecurity = true; // Windows認証の場合
        //_connectionString = builder.ToString();
    }

    public static DataTable GetDataTable()
    {
        var sql = @"select * from Product";

        return SqlServerHelper.GetDataTable(sql);

    }

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

        SqlServerHelper.Query(sql, reader =>
        {
            // SqlServerHelperの方でループ処理が実行されているときに
            // ここの処理が呼ばれて1行ずつリストに追加される
            result.Add(new ProductEntity(
                    Convert.ToInt32(reader["ProductId"]),
                    Convert.ToString(reader["ProductName"]),
                    Convert.ToInt32(reader["Price"])
                    ));

        });

        return result;
    }

    public static void Insert(ProductEntity products)
    {
        string sql = @"insert into Product(ProductId,ProductName,Price) values(@ProductId,@ProductName,@Price)";

        var p = new List<SqlParameter>();

        p.Add(new SqlParameter("@ProductId", products.ProductId));
        p.Add(new SqlParameter("@ProductName", products.ProductName));
        p.Add(new SqlParameter("@Price", products.Price));
        SqlServerHelper.Execute(sql, p);
        
    }

    public static void Update(ProductEntity products)
    {

        string sql = @"update Product set ProductName=@ProductName, Price=@Price where ProductId=@ProductId";

        var p = new List<SqlParameter>();
        p.Add(new SqlParameter("@ProductId", products.ProductId));
        p.Add(new SqlParameter("@ProductName", products.ProductName));
        p.Add(new SqlParameter("@Price", products.Price));
        var update_count = SqlServerHelper.Execute(sql, p);
        
        if (update_count < 1)
        {
            Insert(products);
        }

    }

    public static void Delete(int producId)
    {

        string sql = @"delete Product where ProductId=@ProductId";

        var p = new List<SqlParameter>();
        p.Add(new SqlParameter("@ProductId", producId));
        SqlServerHelper.Execute(sql, p);
    }

    // GetDapper
    public static List<ProductEntity> GetDapper()
    {
        // 改行が必要な場合は先頭に@マークをつける
        var sql = @"select
                    ProductId,
                    ProductName,
                    Price from
                    Product";

        using (var connection = new SqlConnection(SqlServerHelper.ConnectionString))
        {
            // SQLを実行してProductEntityの型にマッピングして値を返してくれる
            // Queryメソッドに合わせてF12キーを押すとDapperクラスに飛べる
            return connection.Query<ProductEntity>(sql).ToList();
        }

    }

    // DapperInsert
    public static void DapperInsert(ProductEntity products)
    {
        string sql = @"insert into Product(ProductId,ProductName,Price) values(@ProductId,@ProductName,@Price)";

        using (var connection = new SqlConnection(SqlServerHelper.ConnectionString))
        {
            connection.Execute(sql, new { products.ProductId, products.ProductName, products.Price });
        }
    }

    // DapperUpdate
    public static void DapperUpdate(ProductEntity products)
    {
        string sql = @"update Product set ProductName=@ProductName, Price=@Price where ProductId=@ProductId";

        using (var connection = new SqlConnection(SqlServerHelper.ConnectionString))
        {
            connection.Execute(sql, new { products.ProductId, products.ProductName, products.Price } );
        }
    }

    // DapperDelete
    public static void DapperDelete(int productId)
    {
        string sql = @"delete Product where ProductId=@ProductId";

        using (var connection = new SqlConnection(SqlServerHelper.ConnectionString))
        {
            connection.Execute(sql, new { productId });
        }
    }

}
```

# 大量データの作成

- 今まで作成してきた処理のパフォーマンスがどのように違うのか見ていく

```c#
// Form.cs

private void InsertDataButton_Click(object sender, EventArgs e)
{
    for (int i = 10; i <= 50000; i++)
    {
        ProductSQLServer.Insert(new ProductEntity(i, "product:" + i, i * 10));
    }

    MessageBox.Show("完了しました");
}
```

## 速度の調べ方

- C#のストップウォッチ機能を使う
- `Dapper`と`SqlDataReader`は速度は早くメモリ使用量も少ない
- `EntityFramerork`は一番遅い

```c#
// ストップウォッチ機能を使って処理の速度を計測する
System.Diagnostics.Stopwatch sw = new System.Diagnostics.Stopwatch();
sw.Start();
// 処理
sw.Stop();
// 何ミリ秒かかったか文字列に戻して出力する
this.Text = sw.ElapsedMilliseconds.ToString();
```