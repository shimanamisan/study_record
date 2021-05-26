###### tags: `C#`

# WPF の扱い方

# StackPanel とは

- コントロールを縦方向か横方向に整列して配置するコントロール
- `Orientation`プロパティを指定して、縦方向に整列するのか、横方向に整列するのか指定する
- 縦に配置：`Vertical`（デフォルト）
- 横に配置：`Horizontal`

# コントロールの名前の付け方

- WindowsForms ではコントロールに名前を付けるのが常識
- WPF ではつけなくても Xaml でコントロールのプロパティ設定が可能
- コードビハインドからコントロールを操作したい場合がある
- 書き方：`x:Name="SampleLabel"`

# イベント

- WPF も WindowsForms 同じ用にイベントが書ける
- Button に Click イベントを追加
- Xaml 側で`Click="SaveButton_Click"`などと記述
- コードビハインド側にイベントが自動生成される

# StaticResource

- リソース
  - 複数の UI 要素で 1 つのオブジェクトを共有するための仕組み
  - CSS で例えると共通のクラスを作って共通レイアウトを作成して統一していくようなイメージ
- 書き方

```c#
 <!-- 定義 -->
 <Window.Resources>
     <SolidColorBrush x:Key="ButtonBackColor" Color="DarkBlue"/>
     <SolidColorBrush x:Key="ButtonForeColor" Color="White"/>
 </Window.Resources>

 <!-- 使う側 -->
 Background="{StaticResource ButtonBackColor}"

 <!-- 色の指定 -->
 Background="DarkBlue"
 Foreground="White"
```

# Application.Resource

- 各画面の `Windows.Resource` の場合は他の画面で使用できない
- 全画面共通のエリアに定義を書く必要があるので非効率
- 書き方：`App.xaml`に記述していく

```c#
 <!-- 定義 App.xaml -->

<Application.Resources>
<SolidColorBrush x:Key="backgroundColor" Color="Orange"/>
<SolidColorBrush x:Key="foregroundColor" Color="White"/>
</Application.Resources>

 <!-- 使う側 MainWindow.xaml -->

<Window.Resources>
</Window.Resources>
```

# スタイルの定義方法

- コントロールごとにデフォルトのスタイルを設定できる
- `App.xaml`にスタイルを定義していく
- 個別でスタイルを指定したい場合は、各画面の Xaml で上書きをする

```c#
 <!-- 定義 App.xaml -->

<Application.Resources>
<Style TargetType="Label">
<Setter Property="Background" Value="DarkBlue"/>
<Setter Property="Foreground" Value="Yellow"/>
<Setter Property="FontSize" Value="25"/>
</Style>

      <Style TargetType="Button">
          <Setter Property="Background" Value="DarkGray"/>
          <Setter Property="Foreground" Value="White"/>
          <Setter Property="FontSize" Value="20"/>
      </Style>

</Application.Resources>

  <!-- 使う側 MainWindow.xaml -->
  <Grid>
      <StackPanel>
          <Label Content="A1"/>
          <Label Content="A2"
          		<!-- デフォルトスタイルを変更したい場合は上書きする -->
                 Background="Green"
                 Foreground="Red"
                 FontSize="40"/>
          <Label Content="A3"/>
          <Button Content="B1"/>
          <Button Content="B2"
                 Background="DarkCyan"/>
          <Button Content="B3"/>
      </StackPanel>
  </Grid>
```

# グループごとのコントロールスタイル定義の方法

- ボタン全体のスタイル定義ではなく、特定のグループのボタンスタイルを定義したい
- そういった場合は、`Key`を指定することで実現できる
- 他のボタンのスタイルを継承して、一部分を変更する場合は`BaseOn`を指定する

```c#
 <!-- 定義 App.xaml -->
 <!-- Keyを指定 -->
 <Style TargetType="Button">
     <Setter Property="Background" Value="DarkGray"/>
     <Setter Property="Foreground" Value="Black"/>
     <Setter Property="FontSize" Value="25"/>
     <Setter Property="Margin" Value="5"/>
 </Style>
 <Style TargetType="Button" x:Key="AButtonStyle">
     <Setter Property="Background" Value="Green"/>
     <Setter Property="Foreground" Value="White"/>
     <Setter Property="FontSize" Value="25"/>
     <Setter Property="Margin" Value="5"/>
 </Style>

 <!-- BaseOnを指定 -->
 <Style TargetType="Button" x:Key="BButtonStyle" BasedOn="{StaticResource AButtonStyle}">
     <Setter Property="Background" Value="Red"/>
 </Style>

 <!-- 使う側 MainWindow.xaml -->
 <StackPanel>
     <Button Content="A1"
             Style="{StaticResource AButtonStyle}"/>
     <Button Content="A2"
             Style="{StaticResource AButtonStyle}"/>
     <Button Content="A3"
             Style="{StaticResource AButtonStyle}"/>
     <Button Content="B1"
              Style="{StaticResource BButtonStyle}"/>
     <Button Content="B2"
             Style="{StaticResource BButtonStyle}"/>
     <Button Content="B3"/>
 </StackPanel>
```

# SQLite とは？

- 気軽に使える軽量なデータベース
- ファイルが 1 つ生成されるだけ
  - SQLServer の用にインストールしたりする必要なし
  - スマホアプリなど端末に記憶させる DB として最適
  - 小規模なデスクトップアプリにも良い
- **NuGet**で SQLite を取得する
- コードファーストで作成できる（DB ツール不要）
  - テーブル作成
  - データの保存
  - データの取得

# NuGet とは？

- `.NET Framework`に対応するフリー・アンド・オープンソースのパッケージマネージャのこと
- npm や composer などといったパッケージ管理ツールと同様に、様々なライブラリを使用することができ、依存関係も自動的に解決してくれる

## 導入方法

- プロジェクト内の「参照」を右クリック → NuGet パッケージの管理 → 検索ボックスに「sqlite-net-pcl」と入力 → 最新の安定版をインストール

## SQLite 用の新しいクラスを作成する

- プロパティを作成する場合は、`prop`と入力して`Tabキー`を 2 回押すと保管機能で自動で入力される

```c#

public class Customer
{
    // PrimaryKeyとAutoIncrementの定義を設定する
    // PrimaryKeyにフォーカスさせて Ctrl + . で using を呼び出せる
    [PrimaryKey, AutoIncrement]
    public int Id { get; set; }

    /// <summary>
    /// 名前
    /// </summary>
    public string Name { get; set; }

    /// <summary>
    /// 電話番号
    /// </summary>
    public string Phone { get; set; }
}

// 共通のコードを記述しておく
// App.xaml.cs
/// <summary>
/// App.xaml の相互作用ロジック
/// </summary>
public partial class App : Application
{
    // データベース名を設定
    static string databaseName = "Shop.db";
    static string folderPath = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);

    // 外部から見れるようにする
    public static string DatabasePath = System.IO.Path.Combine(folderPath, databaseName);
}


// 実際に接続する処理
// MainWindow
public partial class MainWindow : Window
{

    public MainWindow()
    {
        InitializeComponent();
    }

    private void SaveButton_Click(object sender, RoutedEventArgs e)
    {
        var customer = new Customer()
        {
            Name = NameTextBox.Text,
            Phone = PhoneTextBox.Text,
        };

        // SQLiteConnection にフォーカスさせて Ctrl + . で using を呼び出す
        // SQLiteと接続する。引数にデータベースへのパス（保存先）を指定する必要がある
        using (var connection = new SQLiteConnection(App.DatabasePath))
        {
            // 新しくテーブルを作成
            connection.CreateTable<Customer>();
            connection.Insert(customer);
        }
    }

    private void ReadButton_Click(object sender, RoutedEventArgs e)
    {
        using (var connection = new SQLiteConnection(App.DatabasePath))
        {
            connection.CreateTable<Customer>();
            var customers = connection.Table<Customer>().ToList();
        }
    }
}
```

# ListView

- データの一覧を表示するコントロール
- ListView の ItemsSource プロパティにリストをセットする
- リストされているクラスの`ToString()`された値が表示される
- データクラスの`ToStringメソッド`をオーバーライドする
  - 文字の連結しか出来ないので自由なレイアウトができない
- Xaml の TextBlock と Binding を使って自由にレイアウトを配置する
- List クラスだと、リストの変更通知が行われない
  - `ObservableCollection`を使用する
- ListView にスクロールバーを表示する
  - ListView に Height を指定する

```c#
// 画面レイアウト
// MainWindow.xaml
<Grid>
    <StackPanel Margin="10">
        <Button x:Name="AddButton"
                Content="Add"
                FontSize="30"
                Click="AddButton_Click"/>

        <ListView x:Name="CustomerListView"
                    Margin="0,5,0,0"
                    Height="250">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <StackPanel>
                        // Customerクラスのプロパティで表示させるものを選択する
                        <TextBlock Text="{Binding Id}"/>
                        <TextBlock Text="{Binding Name}" FontSize="20"/>
                        <TextBlock Text="{Binding Phone}" Foreground="Blue"/>
                    </StackPanel>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackPanel>
</Grid>


// MainWindow.xaml.cs
public partial class MainWindow : Window
{
    // private List<Customer> _customers = new List<Customer>();

    // ItemsSourceが変更されたときに通知され、画面が再描画されるようにする
    // using System.Collections.ObjectModel を追加する
    private ObservableCollection<Customer> _customers = new ObservableCollection<Customer>();
    private int _index = 0;

    public MainWindow()
    {
        InitializeComponent();

        // 擬似的なデータを作成する
        _customers.Add(new Customer { Id = ++_index, Name = "name" + _index, Phone = "phone" + _index });
        _customers.Add(new Customer { Id = ++_index, Name = "name" + _index, Phone = "phone" + _index });
        _customers.Add(new Customer { Id = ++_index, Name = "name" + _index, Phone = "phone" + _index });

        CustomerListView.ItemsSource = _customers;
    }

    private void AddButton_Click(object sender, RoutedEventArgs e)
    {
        _customers.Add(new Customer { Id = ++_index, Name = "name" + _index, Phone = "phone" + _index });

        // 通知が来ていないので画面上は何も変わっていない
        // ItemsSourceを一回 null にすれば表示が変わるが効率が良くない
        //CustomerListView.ItemsSource = null;
        //CustomerListView.ItemsSource = _customers;
    }
}
```

## Customer クラスの ToString メソッドをオーバーライドしてレイアウトを変更する

```c#
public class Customer
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Phone { get; set; }

    // override を忘れないように
    public override string ToString()
    {
        return $"{Id} - {Name} - {Phone}";
    }
}
```

# ListView のフィルタリング

- 設定しているリストを再設定することでフィルタリング出来る
- List を Linq などを使用して絞り込んで List を再作成

```c#
// MainWindow.xaml
<Grid>
    <StackPanel Margin="10">
        <Button x:Name="AddButton"
                Content="Add"
                FontSize="30"
                Click="AddButton_Click"/>

        <TextBox x:Name="SearchTextBox"
                FontSize="20"
                // テキストボックスに入力されたら処理が走るようにする
                TextChanged="SearchTextBox_TextChanged"/>

        // バインドしたいアイテムを、CustomerListView.ItemsSource という形で指定する
        <ListView x:Name="CustomerListView"
                    Margin="0,5,0,0"
                    Height="250">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <StackPanel>
                        // バインドしたいプロパティを指定する
                        <TextBlock Text="{Binding Id}"/>
                        <TextBlock Text="{Binding Name}" FontSize="20"/>
                        <TextBlock Text="{Binding Phone}" Foreground="Blue"/>
                    </StackPanel>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackPanel>
</Grid>


// MainWindow.xaml.cs

// 一部抜粋

private void SearchTextBox_TextChanged(object sender, TextChangedEventArgs e)
{
    // customerクラスプロパティ内をLinqで検索し、テキストボックスで入力された文字列が含まれていれば文字列を返す
    var filterList = _customers.Where(x => x.Name.Contains(SearchTextBox.Text)).ToList();
    // フィルタリングされたものを ItemsSource に再度設定する
    CustomerListView.ItemsSource = filterList;
}
```

# SQLite と ListView

- SQLite、ListView、StackPanel、Button、TextBox、イベントをやってきた
- SQLite を使用して、データの取得、登録、更新、削除を実装し、ListView を使った表示を行う

```c#

```
