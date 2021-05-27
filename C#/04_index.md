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
// 参考になった記述

// LisytViewが選択されていることを判断する書き方
// 選択されている行をCustomerクラスに変換する
var item = CustomerListView.SelectedItem as Customer;

// 空のコンストラクター（新規登録時に呼ぶ）
public Customer()
{

}
// 新規登録時に名前を追加する場合のコンストラクター
public Customer(string name)
{
    Name = name;
}

// アップデート時のコンストラクター
public Customer(int id ,string name)
{
    Id = id;
    Name = name;
}
```

# Button

- ボタンの中に`StackPanel`を置くことも可能
- 画像と文字を並べて表示できる

```c#
<Button Margin="10"
        x:Name="NormalButton"
        Click="NormalButton_Click">
    <Button.Content>
        <StackPanel Orientation="Horizontal">
            <Ellipse Fill="Red"
                        Width="15"
                        Height="15"
                        Margin="0,0,30,0"/>
            <TextBlock Text="AAA"/>
        </StackPanel>
    </Button.Content>
</Button>
```

# RepeatButton

- ボタンを押している間、クリックイベントを通知
  - Interbal：間隔（ms）
  - Delay：リピートを開始するまでの待機時間（ms）

```c#
// 押している間ずっとイベントの通知が来る
<RepeatButton Content="repeat"
            FontSize="20"
            x:Name="RepeatButton"
            Click="RepeatButton_Click"
            Interval="2000" // 何秒おきに通知が来るか指定
            Delay="5000" // 5秒後からリピートが始まるように指定
            />
```

# ToggleButton

- 押し下げ状態と押し上げ状態を表現
- CheckBox と同じ

```c#
<ToggleButton Content="toggle"
            FontSize="20"
            x:Name="MyToggleBtton"
            Click="MyToggleBtton_Click"/>

// MainWindow.xaml.cs
// 以下の書き方でチェックしているか否か、True、Falseで判定出来る
MyToggleBtton.IsChecked
```

# CheckBox

- 状態の取得方法
- IsChecked
  - ON
  - OFF
  - Indeterminate（不確定）
- InThreeState が True のときのみ不確定状態あり

## イベントは 3 種類ある

- Checked
- Unchecked
- Indeterminate

```c#
 <CheckBox x:Name="MyCheckBox"
        FontSize="20"
        Margin="10"
        Content="check box"
        VerticalContentAlignment="Center"
        Height="30"
        VerticalAlignment="Top"

        IsThreeState="False" // TrueにするとON、OFF、NULL（不確定）の3つの状態が取得できる
        Checked="MyCheckBox_Checked"
        Unchecked="MyCheckBox_Unchecked"
        Indeterminate="MyCheckBox_Indeterminate"
        IsChecked="{x:Null}" // デフォルト値を設定して起動時だけ不確定な状態を作ることも出来る
        />
```

# RadioButton

- グループの中から 1 つを選択させる
- 1 つのパネルに複数のラジオボタンを設置できる

## 状態

- IsChecked
  - True
  - False
  - 不確定

## イベント

- Checked
- Unchecked

## GroupName

- GroupName を指定することで同一パネル上でも異なるグループとして動作する

```c#
<StackPanel Margin="10">
    <RadioButton x:Name="ARadioButton"
                    Content="AAA" FontSize="20"
                    Checked="ARadioButton_Checked"/>
    <RadioButton x:Name="BRadioButton"
                    Content="BBB"
                    FontSize="20"
                    Checked="BRadioButton_Checked"/>
</StackPanel>

<StackPanel Margin="10">
    <RadioButton x:Name="CRadioButton"
                    Content="CCC" FontSize="20"
                    Checked="CRadioButton_Checked"/>
    <RadioButton x:Name="DRadioButton" Content="DDD" FontSize="20"
                    Checked="CRadioButton_Checked"/>

    // グループを分けると上記のラジオボックスから独立した状態になる
    <RadioButton x:Name="ERadioButton" Content="EEE" FontSize="20"
                    GroupName="1"/> // グループを分けたやり方
    <RadioButton x:Name="FRadioButton" Content="FFF" FontSize="20"
                    GroupName="1"/> //
</StackPanel>

// MainWindow.xaml.cs
// どのラジオボタンか判断したいとき
var radioButton = sender as RadioButton;
if(radioButton == CRadioButton)
{

}
else if(radioButton == DRadioButton)
{

}

// チェック状態を取得したいとき
// CRadioButtonは x:Name で指定したもの
CRadioButton.IsChecked
```

# Expander

- 開閉式のグループボックスのこと
- タイトルは`Headerプロパティ`で変更できる

## 開閉状態

- IsExpanded

## 色

- BorderBrush

## 高さの指定

- 高さを指定しなければ、開閉すると下のコントロールが上に詰まる

```c#
<StackPanel>
    <Expander Header="TitleA"
                Margin="10"
                IsExpanded="True" // 起動時に開いている
                BorderBrush="Red"
                Height="100"> // 高さを指定してコントロールが折りたたまれない用にした方法
        <StackPanel>
            <RadioButton Content="AAA" FontSize="20"/>
            <RadioButton Content="BBB" FontSize="20"/>
        </StackPanel>
    </Expander>

    <Expander Header="TitleB"
                Margin="10"
                IsExpanded="True"
                BorderBrush="Red">
        <StackPanel>
            <RadioButton Content="CCC" FontSize="20"/>
            <RadioButton Content="DDD" FontSize="20"/>
        </StackPanel>
    </Expander>
</StackPanel>
```

# グループボックス

- ヘッダーとグループ内に複数のコントロールを配置する
- ヘッダーエリアに複数のコントロールの設置も可能
  - `GroupBox.Header`というキーワードを使う

```c#
<StackPanel>
    <GroupBox Margin="10"
            Header="group A"
            FontSize="20">
        <StackPanel>
            <RadioButton FontSize="20" Content="AAA"/>
            <RadioButton FontSize="20" Content="BBB"/>
        </StackPanel>
    </GroupBox>

    <GroupBox Margin="10">
        <GroupBox.Header>
            // 複数要素を設置する場合は、StackPanelで囲む
            <StackPanel Orientation="Horizontal">
                <CheckBox Content="check box" FontSize="20"/>
                <TextBlock Text="AAA" FontSize="20" Margin="10,0,0,0"/>
            </StackPanel>
        </GroupBox.Header>
        <StackPanel>
            <RadioButton FontSize="20" Content="CCC"/>
            <RadioButton FontSize="20" Content="DDD"/>
        </StackPanel>
    </GroupBox>
</StackPanel>
```

# Slider

- TicPlacement

  - 目盛りを表示させる
  - None：目盛りなし
  - Both：上下に目盛り表示
  - BottomRight：下に目盛り表示
  - TopLeft：上に目盛り表示

- IsSnapToTickEnabled

  - 近くの値にスナップするときに`True`

- TickFrequency

  - つまみを移動させたときの増減する値（カーソルでドラッグ）

- SmallChange

  - 矢印キーを押したときに増減する値

- LargeChange

  - クリックしたときの増減する値

- Orientation

  - 向きが縦か横か

```c#
<StackPanel Orientation="Horizontal">
    <StackPanel>
        // 目盛り上下
        <Slider TickPlacement="Both" Width="200" Foreground="Black" Margin="10"/>
        // 目盛り下
        <Slider TickPlacement="BottomRight" Width="200" Foreground="Black" Margin="10"/>
        // 目盛りなし
        <Slider TickPlacement="None" Width="200" Foreground="Black" Margin="10"/>
        // 目盛り上
        <Slider TickPlacement="TopLeft" Width="200" Foreground="Black" Margin="10"/>
        <Slider Width="200" Foreground="Black" Margin="10"/>
    </StackPanel>

    <StackPanel>
        <Slider Width="200"
                TickPlacement="Both"
                Foreground="Black"
                Margin="10"
                IsSnapToTickEnabled="True" // スライダーの位置にスナップする機能を有効化
                TickFrequency="10" // 変化量が10単位で変化する
                SmallChange="20" // 矢印キーで動かすと20ずつ変化する
                LargeChange="50" // 適当な場所でクリックすると50ずつ変化する
                Minimum="0"
                Maximum="100"
                ValueChanged="Slider_ValueChanged"/> // イベントを設定
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Center">
            <TextBlock Text="SliderValue:"/>
            <TextBlock x:Name="ASlider"/>
        </StackPanel>

        <Slider Width="200"
                TickPlacement="Both"
                Foreground="Black"
                Margin="10"
                IsSnapToTickEnabled="False" // スナップしないパターン（細かい単位も表示される）
                TickFrequency="10"
                SmallChange="20"
                LargeChange="50"
                Minimum="0"
                Maximum="100"
                ValueChanged="BSlider_ValueChanged"
                Orientation="Vertical" // 縦向きに設定する
                Height="120"/>
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Center">
            <TextBlock Text="SliderValue:"/>
            <TextBlock x:Name="BSlider"/>
        </StackPanel>
    </StackPanel>
</StackPanel>

// MainWindow.xaml.cs
// 設定したイベントの取得方法（ここでは小数点以下の値も取得している）
private void Slider_ValueChanged(object sender, RoutedPropertyChangedEventArgs<double> e)
{
    ASlider.Text = e.NewValue.ToString();
}

private void BSlider_ValueChanged(object sender, RoutedPropertyChangedEventArgs<double> e)
{
    BSlider.Text = e.NewValue.ToString();
}
```

# ProgressBar

- 作業の進捗状態を示すコントロール
- 時間がかかる処理を非同期で進捗させて状況を表示させる

## プロパティ

- Value、Minimum、Maximum を設定

## イベント

- ValueChanged

## 表示のパターン

- 進捗状況を横に表示
- 進捗状況と ProgressBar を重ねる
  - 同一の Grid にして行と列も同じにして`TextBlock`を重ねて表示
- 進捗状況がわからないときは、動き続けるだけのアニメーションを表示
  - IsIndeterminate を`True`にする

```c#
<StackPanel Margin="5,10,5,5"
            Orientation="Horizontal">
    <ProgressBar x:Name="AProgressBar"
                Margin="25,0,0,0"
                Height="30"
                Width="180"
                Minimum="0"
                Maximum="100"
                HorizontalAlignment="Left"
                ValueChanged="AProgressBar_ValueChanged"
                />
    <TextBlock x:Name="ATextBlock"
                Margin="10,0,0,0"
                HorizontalAlignment="Center"
                VerticalAlignment="Center"
                Text="xxx"
                Width="50"
                FontSize="20"/>
    <Button x:Name="AButton"
                Margin="10,0,0,0"
                Click="AButton_Click"
                Height="30"
                Width="60"
                Content="Run"/>

</StackPanel>

// プログレスバーとTextBlockを重ねたやり方
<StackPanel Margin="5,10,5,5"
            Orientation="Horizontal">
    // ProgressBarとTextBlockを同一グリッドにする
    // こうすることで、ProgressBarの中にTextBlockを表示する事ができる
    <Grid>
        <ProgressBar x:Name="BProgressBar"
                Margin="25,0,0,0"
                Height="30"
                Width="180"
                Minimum="0"
                Maximum="100"
                HorizontalAlignment="Left"
                ValueChanged="BProgressBar_ValueChanged"
                />
        <TextBlock x:Name="BTextBlock"
                Margin="10,0,0,0"
                HorizontalAlignment="Center"
                VerticalAlignment="Center"
                Text="xxx"
                Width="50"
                FontSize="20"/>
    </Grid>

    <Button x:Name="BButton"
                Margin="10,0,0,0"
                Click="BButton_Click"
                Height="30"
                Width="60"
                Content="Run"/>

</StackPanel>

// IsIndeterminateをTrueにするとき
<StackPanel Margin="5,10,5,5"
                Orientation="Horizontal">
    <Grid>
        <ProgressBar x:Name="CProgressBar"
                Margin="25,0,0,0"
                Height="30"
                Width="180"
                Minimum="0"
                Maximum="100"
                HorizontalAlignment="Left"
                />
        <TextBlock x:Name="CTextBlock"
                Margin="10,0,0,0"
                HorizontalAlignment="Center"
                VerticalAlignment="Center"
                Text=""
                FontSize="14"/>
    </Grid>

    <Button x:Name="CButton"
                Margin="10,0,0,0"
                Click="CButton_Click"
                Height="30"
                Width="60"
                Content="Run"/>

</StackPanel>
```

```c#
// MainWindow.xaml.cs

//
private void AProgressBar_ValueChanged(object sender, RoutedPropertyChangedEventArgs<double> e)
{
    ATextBlock.Text = AProgressBar.Value.ToString() + "%";
}

private void AButton_Click(object sender, RoutedEventArgs e)
{
    // 非同期処理
    Task.Run(() =>
    {
        for (int i = 0; i < 10; i++)
        {
            System.Threading.Thread.Sleep(500);

            // 非同期で処理をしている場合にUIコントローラーを操作する場合は
            // UIスレッド上に戻す処理が必要（非同期中にUIコントローラーをそのまま操作するとエラーになる）
            Application.Current.Dispatcher.Invoke(() =>
            {
                AProgressBar.Value += 10;
            });
        }
    });
}

// プログレスバーにTextBlockを重ねたやり方
private void BProgressBar_ValueChanged(object sender, RoutedPropertyChangedEventArgs<double> e)
{
    BTextBlock.Text = BProgressBar.Value.ToString() + "%";
}

private void BButton_Click(object sender, RoutedEventArgs e)
{
    Task.Run(() =>
    {
        for (int i = 0; i < 10; i++)
        {
            System.Threading.Thread.Sleep(500);

            Application.Current.Dispatcher.Invoke(() =>
            {
                BProgressBar.Value += 10;
            });
        }
    });
}

// IsIndeterminate適応時
private void CButton_Click(object sender, RoutedEventArgs e)
{
    // 処理が終了したときはFalseにする必要がある
    CProgressBar.IsIndeterminate = true;
    CTextBlock.Text = "検索しています...";
}
```

# ComboBox

- 複数の項目から 1 つのアイテムを選択するというコントロール
- Items に単純に Add する方法とデータバインディングする方法がある
- 基本的に内部的には ID で管理して、データバインディングするのが一般的
- バインディングは ItemsSource を使う
- ItemesSource の単一項目を表示する場合
  - SlectedValuePath
    - 選択されているときに SelectedValue で取得される
  - DisplayMemberPath
    - 表示する文字の項目を設定。Text で取得される値
- 独自に加工する場合
- ListView と同様に `ItemTemplate`、`DataTemplate` の記述でバインディングが可能

```c#
<StackPanel>
    <StackPanel Orientation="Horizontal">
        <ComboBox x:Name="MyComboBox"
                    Height="40"
                    Width="200"
                    Margin="10"
                    FontSize="20"
                    VerticalAlignment="Center"/>
        <Button Margin="10"
                Width="50"
                Content="check"
                Click="MyButton_Click"
                />

    </StackPanel>

    <StackPanel Orientation="Horizontal">
        <ComboBox x:Name="AComboBox"
                    Height="40"
                    Width="200"
                    Margin="10"
                    FontSize="20"
                    VerticalAlignment="Center"
                    SelectedValuePath="Id"
                    DisplayMemberPath="Name"/>
        <Button Margin="10"
                Width="50"
                Content="check"
                Click="AButton_Click"
                />

    </StackPanel>

    <StackPanel Orientation="Horizontal">
        <ComboBox x:Name="BComboBox"
                    Height="40"
                    Width="200"
                    Margin="10"
                    FontSize="20"
                    VerticalAlignment="Center">
            // データを加工したい場合に、バインディングを使用したやり方
            <ComboBox.ItemTemplate>
                <DataTemplate>
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Text="{Binding Id}" FontSize="20" Margin="5"/>
                        <TextBlock Text="{Binding Name}" FontSize="20" Margin="5"/>
                        <TextBlock Text="{Binding Phone}" FontSize="20" Margin="5"/>
                    </StackPanel>
                </DataTemplate>
            </ComboBox.ItemTemplate>
        </ComboBox>
        <Button Margin="10"
                Width="50"
                Content="check"
                Click="BButton_Click"
                />

    </StackPanel>
</StackPanel>
```

```c#
// MainWindow.xaml.cs

// 一部抜粋
private void AButton_Click(object sender, RoutedEventArgs e)
{
    // ComboBoxのSelctedItemにバインディングされているCustomerが入ってくる
    var item = AComboBox.SelectedItem as Customer;
    if (item != null)
    {
        var sb = new StringBuilder();
        sb.AppendLine("AComboBox.SelectedIndex:" + AComboBox.SelectedIndex);
        sb.AppendLine("AComboBox.SelectedValue:" + AComboBox.SelectedValue);
        sb.AppendLine("AComboBox.Text:" + AComboBox.Text);
        sb.AppendLine("-------");
        sb.AppendLine("SelectedItem.Id : " + item.Id);
        sb.AppendLine("SelectedItem.Name : " + item.Name);
        sb.AppendLine("SelectedItem.Phone : " + item.Phone);
        MessageBox.Show(sb.ToString());
    }
}
```

# ListBox

- 一覧から任意の行を選択できる（単一や複数）
- バインド方法は`ListVie`や`ComboBox`と同様
- SelectionMode
  - Single：1 つのみ選択可能
  - Extebded：Ctrl キーや Shift キーを押しながら複数選択可能
  - Multiple：キーを押さずにクリックのみで複数選択可能。クリックすると ON・OFF が切り替わる

```c#
<StackPanel>
    <ListBox x:Name="MyListBox"
                HorizontalAlignment="Left"
                VerticalAlignment="Top"
                Margin="10"
                Width="260"
                Height="180">
        <ListBox.ItemTemplate>
            <DataTemplate>
                <StackPanel Orientation="Horizontal">
                    // ItemsSourceで指定したものがバインディングされる
                    <Image Source="{Binding FileName}"
                            Width="50"
                            Height="50"
                            Margin="10"
                            />
                    <TextBlock Text="{Binding Name}"
                            Margin="10"
                            />
                </StackPanel>
            </DataTemplate>
        </ListBox.ItemTemplate>
    </ListBox>
    <StackPanel Orientation="Horizontal">
        <RadioButton x:Name="SingleRadioButton" Content="Single" Checked="RadioButton_Checked"/>
        <RadioButton x:Name="ExtendedRadioButton" Content="Extended" Checked="RadioButton_Checked"/>
        <RadioButton x:Name="MultipleRadioButton" Content="Multiple" Checked="RadioButton_Checked"/>
    </StackPanel>
    <Button FontSize="20"
            Content="check"
            Click="Button_Click"/>
</StackPanel>
```

```c#
// MainWindow.xmal.cs

public partial class MainWindow : Window
{
    private ObservableCollection<Dto> _dtos = new ObservableCollection<Dto>();

    public MainWindow()
    {
        InitializeComponent();

        _dtos.Add(new Dto("Images/A.jpeg", "Shinichi ONO"));
        _dtos.Add(new Dto("Images/B.jpeg", "Jyunta INAMOTO"));
        _dtos.Add(new Dto("Images/C.jpeg", "Naotaro TAKAHARA"));
        MyListBox.ItemsSource = _dtos;
        SingleRadioButton.IsChecked = true;
    }

    private void RadioButton_Checked(object sender, RoutedEventArgs e)
    {
        // ラジオボタンのチェックがされていたら各処理を実行する
        if(SingleRadioButton.IsChecked.Value)
        {
            MyListBox.SelectionMode = SelectionMode.Single;
        }
        else if (ExtendedRadioButton.IsChecked.Value)
        {
            MyListBox.SelectionMode = SelectionMode.Extended;
        }
        else
        {
            MyListBox.SelectionMode = SelectionMode.Multiple;
        }
    }

    private void Button_Click(object sender, RoutedEventArgs e)
    {

    }
}

public sealed class Dto
{
    public Dto(string fileName, string name)
    {
        FileName = fileName;
        Name = name;
    }

    public string FileName { get; set; }
    public string Name { get; set; }
}
```

# TabControl

- ヘッダー付きのページ
- エリアが限られている場合に有効
- グループごとの入力エリア

## ページの変更

- `SelectedIndex`で変更

```c#
<TabControl x:Name="MyTabControl"
            Margin="20">
    <TabItem Header="AAA">
        // この中にその他のコントロールを設置
        <StackPanel>
            <TextBox Margin="10"/>
            <Button Content="Save"
                    Margin="10"/>
        </StackPanel>
    </TabItem>
    <TabItem Header="BBB">
        <StackPanel>
            <TextBox Margin="10"/>
            <Button Content="Search"
                    Margin="10"/>
        </StackPanel>
    </TabItem>
</TabControl>

// MainWindow.xaml.cs
public MainWindow()
{
    InitializeComponent();

    // 起動時に任意のタブを選択しておきたい場合
    MyTabControl.SelectedIndex = 1;
}
```

# TreeView

- 階層構造を表現するコントロール
  - **ファイルエクスプローラー**のようなイメージ

## 静的に生成する場合

- TreeView の中に TreeViewItem を作る、さらにその中に TreeViewItem を作る...以下繰り返し
- 文言は Header に設定する
  - TreeViewItem.Header を利用することで、マーク付きの文言などが簡単に表示できる

## 動的に生成する場合

- 階層構造で使うクラスを作成（例：Dto クラスを作成した場合）
  - Dto クラスの中に **Dto のリストを保持するプロパティ**を作る（例：Dtos）
- TreeView の ItemsSource にデータ（Dto のリスト）を設定する
- `TreeView.ItemTemplate`の下に`HierachicalDataTemplate`を作成し、更にその配下に以下のデータを設定する
  - DataType：扱うクラス（ここでは Dto クラス）
  - ItemsSource：Dtos

```c#
<StackPanel>
    // TreeViewを親にして、TreeViewItemを入れ子にしていく
    <TreeView FontSize="20">
        <TreeViewItem Header="A部">
            <TreeViewItem Header="Aグループ">
                <TreeViewItem Header="グループ員１"/>
                <TreeViewItem Header="グループ員２"/>
                <TreeViewItem Header="グループ員３"/>
            </TreeViewItem>
        </TreeViewItem>
    </TreeView>

    <TreeView FontSize="20">
        <TreeViewItem>
            // Headerにマークを付けるパターン
            <TreeViewItem.Header>
                <StackPanel Orientation="Horizontal">
                    <Ellipse Height="10"
                                Width="10"
                                Margin="6"
                                Fill="Red"/>
                    <TextBlock Text="B部"/>
                </StackPanel>
            </TreeViewItem.Header>
            <TreeViewItem Header="グループ員１"/>
        </TreeViewItem>
    </TreeView>

    <TreeView x:Name="CTreeView" FontSize="20">
        // データをバインディングさせる領域を作成
        <TreeView.ItemTemplate>
            <HierarchicalDataTemplate
                    DataType="local:Dto"
                    ItemsSource="{Binding Dtos}">
                <TextBlock Text="{Binding Name}"/>
            </HierarchicalDataTemplate>
        </TreeView.ItemTemplate>
    </TreeView>
</StackPanel>
```

```c#
// MainWindow.xaml.cs
public partial class MainWindow : Window
{
    // TreeViewに動的に表示していく要素
    private ObservableCollection<Dto> _dtos = new ObservableCollection<Dto>();
    public MainWindow()
    {
        InitializeComponent();

        var dto1 = new Dto("Name1");
        // 新しくインスタンスを追加し、子階層を作成する
        dto1.Dtos.Add(new Dto("Name1-1"));
        dto1.Dtos.Add(new Dto("Name1-2"));
        _dtos.Add(dto1);

        // この要素をバインディングする
        CTreeView.ItemsSource = _dtos;
    }
}

public sealed class Dto
{
    public Dto(string name)
    {
        Name = name;
    }

    public string Name { get; set; }
    public List<Dto> Dtos { get; set; } = new List<Dto>();
}
```

# TextBlock、TextBox について

- FontSize
- FontWeight：普通、太文字
- FontStyle：普通、イタリック
- TextTrimming：表示しきれないとき「...」を表示
- 改行の指定：Run、LineBreak
- 改行の方法：TextWrapping

```c#
<StackPanel>
    <TextBlock Text="AAA BBB CCC DDD EEE FFF"
                FontSize="20"
                Width="120"
                FontWeight="Bold"
                FontStyle="Italic"
                TextTrimming="CharacterEllipsis" // TextBlockから見切れる場合は ... に変わる
                />

    <TextBlock Text="AAA BBB CCC DDD EEE FFF"
                FontSize="20"
                Width="120"
                FontWeight="Bold"
                FontStyle="Italic"
                TextTrimming="WordEllipsis" // 見切れる場合は単語単位で切り取る
                />

    <TextBlock FontSize="20"
                Width="120">
        <Run Text="AAA"/>
        <LineBreak/>
        <Run Text="BBB"/>
    </TextBlock>

    <TextBox FontSize="20"
                Width="120"
                Text="aaabbbcccdddeeefff abc"
                TextWrapping="NoWrap"/> // 改行モード無効

    <TextBox FontSize="20"
                Width="120"
                Text="aaabbbcccdddeeefff abc"
                TextWrapping="Wrap"/> // 改行モード有効（単語の途中でも改行される）

    <TextBox FontSize="20"
                Width="120"
                Text="aaabbbcccdddeeefff abc"
                TextWrapping="WrapWithOverflow"/> // 単語の途中で改行されない
</StackPanel>
```

# Menu

- MenuItem で定義する
  - テキストは`Header`で設定
- 階層関係で MenuItem を指定する場合
  - MenuItem の中に MenuItem を繰り返す

```c#
<StackPanel Margin="0 10 0 0">
    <Menu>
        <MenuItem Header="ファイル">
            <MenuItem Header="開く"/>
            <MenuItem Header="新規">
                <MenuItem Header="雛形"/>
            </MenuItem>
        </MenuItem>
        <MenuItem Header="編集"/>
        <MenuItem Header="印刷" Click="MenuItem_Click"/>
    </Menu>
</StackPanel>
```

# ToolBar

- Buttno に Image を設定
- Separator で間仕切りを引くことでグループ分け出来る

```c#
<ToolBar Height="45"
        VerticalAlignment="Top">
    <Button>
        <Image Source="Images\file.png"/>
    </Button>
    <Separator/>
    <Button Click="Button_Click">
        <Image Source="Images\graph.jpg"/>
    </Button>
</ToolBar>
```

# StatusBar

