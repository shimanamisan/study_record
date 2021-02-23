###### tags: `C#`

# C#とは？

- マイクロソフトが開発下言語で、JAVA と C++の良いところを取り入れた言語
- オブジェクト指向言語なので、オブジェクト指向を取り入れた記述をすることが出来る
- イベントドリブン型と呼ばれていて、「ボタンをクリックしたら〜をする」などのように処理をする作りになっている

# .NET Framework とは？

- アプリケーションが動く土台となっているもの
- VB、C++、C#などを動かす環境

## クラスライブラリ（部品群）

- 全部イチから作るのではなく、すでにある部品を組み合わせて作る
- Windows フォーム、Web フォーム、WPF、UWP、Xamarin、ADO.net、WCF など
- クラスライブラリは共通関数をまとめた部品群

## 共通言語ランタイム（CLR）

- 実際にアプリケーションを動かす際にこれが裏で動いている
- これが動くことで、VB や C#が同じ動きをする
- セキュリティー関係の事やメモリ開放を制御するガベージコレクションなどが裏で動いている

# ソリューション

- アプリケーションの製品単位にソリューションを作る
- 機能やカテゴリーごとにプロジェクトを分ける
- 参照関係をつけることで、画面のプロジェクトからクラスライブラリの機能を参照したりすることが出来る

# 参照

- 開発元のファイルからクラスライブラリなどを読み込む操作
- 片方向からしか読み込めないので注意！
- UI プロジェクト → Class Library：これは OK
- Class Library → UI プロジェクト：これは NG

# 名前空間

- PHP などと一緒で、ファイルの階層をずらすことで同じクラス名を使うことが出来る
- フォルダを分けると同じファイル名の名前を付けても問題ないのと同じイメージ

# ブロック

- `Ctrl + .`で読み込むクラスを予測できる

```C#
using System.Windows.Forms;

namespace CS06
{
    public partial class Form1 : Form
    {
        private int _value = 0;

        public Form1()
        {
            InitializeComponent();
        }

        private int GetValue()
        {
            _value++;

            // メソッドで型を指定したら必ず値を返さないといけない
            return 1;
        }

        // メッセージを表示させるだけなど、値を返さない場合はvoidを指定する
        private void SetValue()
        {
            MessageBox.Show("Hello World");
        }
    }
}
```

# 変数と定数

- 値を覚えていくメモリ領域

```C#
namespace CS07
{
    public partial class Form1 : Form
    {
        // ボタンをクリックしたときに値を記憶させる
        private int _count = 0;

        // 定数
        private const int AAA = 10;

        public Form1()
        {
            InitializeComponent();
        }

        // ボタン2のクリックイベント
        private void button2_Click(object sender, EventArgs e)
        {
            MessageBox.Show(_count.ToString());
        }

        // ボタンクリックのイベント
        private void button1_Click(object sender, EventArgs e)
        {
            _count += 1;
        }
    }
}
```

# 値の型

```C#
    // 数値の型
    byte byteValue = 255; // 255まで値が入る
    // byte byteValue2 = 256; // 256はエラーになる

    //
    short shortValue = 10;
    short shortMax = short.MaxValue; // 最大値の32767が代入される

    // ushort ushortValue = -1; // マイナスを含めない

    int intValue = -10; // マイナスを含めることができる
    int intMaxValue = int.MaxValue; // 最大値が代入される

    uint uintValue = -10; // マイナスは使用できない

    // 同様にマイナスを含めることができる型と、そうでない型がある
    long longValue = -10;
    ulong ulongValue = 10;

    // 浮動小数点型
    float floatValue = 1.2334455f; // 明示的に使用する場合は末尾にfを記述しないと、double型と認識されてエラーになる
    double doubleValue = 1.23455d; // 末尾に明示的にdとしても良い
    decimal decimalValue = 1.2345m; // decimalも末尾にmをつける必要がある

    // Boolean
    bool boolValue = false; // true false しか入らない

    if (boolValue)
    {
        // trueの時
    }

    if (!boolValue)
    {
        // falseの時
    }

    // String型
    string strValue = "sfasdfasあああ";
    string strValue1 = "12234"; // 数字なので数値とは全く別物
```

## 参照

```c#

```
