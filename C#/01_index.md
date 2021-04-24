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

## なぜ CLR が必要なのか

- .NET Framework 対応のプログラムは全て MSIL 形式の中間コードに変換（コンパイル）される
- MSIL のコードは、マシン語に極めて近いコード
- MSIL のコードが実行できる環境を用意すれば、Visual C# で作成したプログラムも、 Visual Basic で作成したプログラムも同じように実行できる

## CLR は MSIL のコードを実行するためのソフトウェア群

- MSIL のコードは極めてマシン語に近いと言ってもマシン語ではないので、変換してやる必要がある
- MSIL のコードをマシン語にコンパイルするための JIT コンパイラーを始め、メモリ管理やセキュリティに関する処理を行うソフトウェアが含まれている
- CLR は.NET Framework 対応のプログラムを実行するために用意された環境

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
- `var`を使用すると暗黙的な型変換が行われる（コンパイラーが既定の型を指定する）

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

            // varを指定すると暗黙的な型変換が行われる
            var num = 1;
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

# 値型と参照型

- 値型：数値など
- 参照型：配列、クラス

```c#
namespace CS09
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();

            StartPosition = FormStartPosition.CenterScreen;
        }

        // 追加したボタン
        private void button1_Click(object sender, EventArgs e)
        {
            int a = 10;
            int b = 20;

            MessageBox.Show("a= " + a); // 10
            MessageBox.Show("b= " + b); // 20

            a = b;

            MessageBox.Show("a= " + a); // 20
            MessageBox.Show("b= " + b); // 20

            b = 30;

            MessageBox.Show("a= " + a); // 20
            MessageBox.Show("b= " + b); // 30

        }

        // 参照型のボタン
        private void button2_Click(object sender, EventArgs e)
        {
            Other a = new Other();
            Other b = new Other();

            a.Value = 10;
            b.Value = 20;

            MessageBox.Show("a= " + a.Value); // 10
            MessageBox.Show("b= " + b.Value); // 20

            a = b; // インスタンスbのアドレスの参照先に変更されている

            MessageBox.Show("a= " + a.Value); // 20
            MessageBox.Show("b= " + b.Value); // 20

            b.Value = 30;
            MessageBox.Show("a= " + a.Value); // 変数aに格納されているインスタンスはbの参照先に書き換わっているので、インスタンスbと同じ値が出力される
            MessageBox.Show("b= " + b.Value); // 30

        }

        private void OtherMethod(Other other)
        {
            other.Value = 55;
        }

        private void ValueMethod(int other)
        {
            other = 66;
        }

        public class Other
        {
            public int Value;
        }

        private void button3_Click(object sender, EventArgs e)
        {
            Other a = new Other();
            a.Value = 10;
            // 関数内でインスタンス内の値が変更されている
            OtherMethod(a);

            MessageBox.Show("a= " + a.Value);
        }

        private void button4_Click(object sender, EventArgs e)
        {
            int a = 10;

            // 値がコピーされているだけなので、大元の変数には影響がない
            ValueMethod(a);

            MessageBox.Show("a= " + a);
        }
    }
}
```

# データ型と変換

- キャスト：数値同士や似た者同士を変換する
- Parse：変換できなかったときは例外が発生
- TryParse：戻り値が boolean
- Convert：Parse とほぼ同じだが、null が入ってきたときは 0 を返す

```c#
namespace CS10
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        // キャスト
        private void button1_Click(object sender, EventArgs e)
        {
            double d1 = 1.9d;
            // 明示的にキャストする
            int aaa = (int)d1;
        } // このあたりでブレークポイントを付けてみる

        // Parse
        private void button2_Click(object sender, EventArgs e)
        {
            // string value = "あああ"; // 例外が発生する
            // string value = "10.1"; // 例外が発生する
            // string value = null; // 例外が発生する

            string value = "10";
            // intに変換
            int intValeu = int.Parse(value);
        }

        // TryParse
        private void button3_Click(object sender, EventArgs e)
        {
            string value = "10";
            // intに変換した後の変数を用意する必要がある
            int intValue;
            // valueが正しく変換されたら true そうでなければ false が返ってくる
            bool result = int.TryParse(value,out intValue); // outというのはresultに出力されるという意味
        }

        // Convert
        private void button4_Click(object sender, EventArgs e)
        {
            // string value = "10.4"; // 例外が発生

            // Parseではnullは例外になるが、Convertでは0が返ってくる
            string value = null;

            // string value = "10";

            // 何ビットに変換するか指定できる
            int intValeu = Convert.ToInt32(value);
        }

    }
}
```

# 配列

```c#
namespace CS11
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        // 配列
        private void button1_Click(object sender, EventArgs e)
        {
            int aaa = 1;

            // 新しい配列を生成（型や許容できる値の個数も指定する）
            int[] values = new int[3];

            values[0] = 10;
            values[1] = 20;
            values[2] = 30;
            // values[3] = 40; // 3つまで入る配列なので、4つ目は入れれない

            // 省略した書き方
            int[] values2 = { 1, 2, 4 }; // int[3]の配列の中に 1,2,4 の数値が入っている
            // 配列の中の個数を取得する
            MessageBox.Show(values2.Length.ToString());
        }
    }
}
```

# 動的配列

- 前回の配列よりも、こっちのほうが便利なので基本的にこの書き方を使うことが多い

```c#
namespace CS12
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        // 動的配列
        private void Form1_Load(object sender, EventArgs e)
        {
            // System.Collections.Generic; が読み込まれていると使える
            // Tには値の型を入れる
            List<int> valuse = new List<int>();
            // 値を追加
            valuse.Add(10);
            valuse.Add(20);
            valuse.Add(50);
            valuse.Add(51);
            valuse.Add(52);

            // 配列内の要素数を取得
            MessageBox.Show(valuse.Count.ToString());

            // インデックス指定で値を取得
            MessageBox.Show(valuse[2].ToString());

            // 値を指定して削除
            valuse.Remove(20);

            // インデックスを指定して削除
            valuse.RemoveAt(3);

            // 全部削除
            // valuse.Clear();
        }
    }
}
```

# 演算子

- C#は静的型付け言語なので、型まで比較する`===`の演算子が無い

# 条件分岐

```c#
namespace CS14
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, EventArgs e)
        {
            // テキストボックスに入力された値をint型に変換して変数に格納
            int value = Convert.ToInt32(textBox1.Text);

            if (value == 10)
            {
                MessageBox.Show("10です");
            }

            else if (value == 20)
            {
                MessageBox.Show("20です");
            }

            else
            {
                MessageBox.Show("どれでもない");
            }

            switch (value)
            {
                case 10:
                    MessageBox.Show("10です");
                    break;

                case 20:
                    MessageBox.Show("20です");
                    break;

                default:
                    MessageBox.Show("どれでもない");
                    break;
            }
        }
    }
}
```

# whilw 文

- 条件が一致する間は処理を繰り返す

```c#
namespace CS15
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {
            int i = 0;

            while( i < 10)
            {
                i++;

                if (i == 3)
                {
                    // ループを飛ばす
                    continue;
                }

                if (i == 5)
                {
                    // ループの処理を抜ける
                    // 5でbreakで入るのでメッセージボックスには4までが表示される
                    break;
                }

                MessageBox.Show(i.ToString());
            }
        }
    }
}

```

# do - while 文

- 必ず一回は処理されるループ文

```c#
namespace CS16
{
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }

        private void Form1_Load(object sender, EventArgs e)
        {
            int i = 100;

            do
            {
                i++;

            } while (i < 100);

            MessageBox.Show(i.ToString()); // 101
        }
    }
}
```

# foreach 文

- `Control`は、コントロールの基本クラスで、コントロールを操作するためのプロパティやメソッドが定義されている
- ループ中に**値を変更したり消したりするとエラーになる**
- 値を変更したい場合は`for文`を使用する
- `for文`で値を消す場合は**for 文の逆回し**を行うようにする
- なぜなら、値を消した際に配列の中のインデックスも詰まるので、思うように処理が行われない場合がある
- 該当の要素を末尾から消すことによって、要素の順番に不整合が起こらないので意図した処理を実装する事ができる

```c#
// オブジェクトに対しての繰返し処理
foreach(オブジェクトの型 変数名 in 対象のオブジェクト)
{
    //
}

// Controlsコレクションに対しての繰返し処理
foreach(Control 変数名 in 対象のフォーム名.Controls)
{
    //
}

for(int i = items.Count - 1; i >= 0; i-- )
{
    if(items[i] == 10){
        items.RemoveAt(i);
    }
}
```

# ループのネスト

```c#
for(int row = 1; row < 11; row++){
    for(int col = 1 ; col < 11; col++ ){
        Console.WriteLine($"The cell is ({row}, {col})");
    }
}
```

# 配列

- 配列は初期化時に要素の数を指定する
- 配列は固定長のデータを扱うのに適している
- **初期化後は要素の数を増やしたり減らしたりすることはでき無い**
- 配列を初期化するには以下のように記述する

```c#
// 要素の数を指定
string[] array = new string[3];

// 初期化時に値も設定可能
string[] array = {"1", "2", "3"};
```

# リストを作成する

- List <T>クラス
- 配列と違い初期化する際に要素の数を指定せず、初期化した後に数を変更することが可能

```c#
var names = new List<string> { "<name>", "Ana", "Felipe" };
```

# テンプレートリテラルの使い方

- テンプレートリテラルは以下のように記述

```c#
Console.WriteLine($"The cell is ({row}, {col})");
```

# オブジェクト指向

- フィールド
- メソッド
- プロパティ
- `C#`ではクラス内で値を保持する役割は同じだが、**プロパティ**と**フィールド**機能は若干違いがある

## プロパティ

- プロパティは特殊メソッド
  - フィールドの値を設定したり取得したりする特殊なフィールド
- PHP でいうと、getter メソッドや setter メソッドと似たような役割（getter メソッドや setter メソッドは自分で実装する）
- 普通の値のように使えてカプセル化に使用できる
- 値を読み取り専用や書き込み専用にできる
- 特に値チェックのロジックが必要ない場合は**自動実装プロパティ**にできる

```c#
// フィールド
private int _count = 0;

// プロパティの書き方（内部で条件分岐したり何かロジックがある場合）
public int Count
{
    // インスタンス化した後に、フィールドもしくはプロパティの値を取得しようとするとこっちの処理に入る
    get
    {
        return _count;
    }

    // インスタンス化した後に、フィールドもしくはプロパティに値を代入するときはこっちの処理に入る
    set
    {
        if (value >= 100)
        {
            _count = 100;
            return;
        }

        _count = value;
    }
}

// 自動実装プロパティ（内部でロジックがない場合）
// 後からロジックを実装して処理を分けることもできる（フィールドに直接代入だと何も変更できない）
public int _count { get; set ; } // この場合はフィールド private int _count = 0; の記述も不要

// 取得はパブリックに公開されて、値の設定はクラス内でしか出来ないカプセル化されたやり方
public int Count { get; private set; }
```

## コンストラクタ

- コンストラクタは 2 つ書くことができる
- コンストラクタからコンストラクタを呼ぶことができる
- それにより、渡した引数の数で初期値を与えたりすることができる
- 一番引数の数が多いコンストラクタにロジックを集めることで、コンストラクタが複数になっても重複しないコードを書くことができる

# Dispose とは

- インスタンスのリソースを開放する時に呼び出すメソッド
- IDisposable というインタフェースを実装しているクラスには必ず実装されているメソッド

## リソース開放とは

- アプリケーションが動作中に確保していくメモリを開放すること
  ー C#で int と宣言して変数を作れば 4 バイトのメモリが確保されるが、そのままにしていればメモリが食いつぶされていしまう
