###### tags: `C#でラムダ式を書く方法`

# C#でラムダ式を書く方法

# ラムダ式とは？

- JavaSprict で言うところのアロー関数のような書き方
- `Delegate, Predicate, Action, Func`などを把握しつつ、最終的にはコレクションにアクセスする方法を学ぶ
- `values.Find(x => x.Contains("B"))`このような書き方がラムダ式
- `=>` この記号は**ゴーズトゥー**と読む

# ラムダなしの書き方

```c#
//ラムダなし
private void button1_Click(object sender, EventArgs e)
{
    var values = new string[] { "A", "BB", "CCC", "DDDD", "EEEEE" };

    var result = new List<string>();

    // 3文字以上の文字列を抽出
    foreach (var val in values)
    {
        if (val.Length >= 3)
        {
            result.Add(val);
        }
    }

    Console.WriteLine(string.Join(",", result));

    //--------------------------------------------
    var result2 = GetValue1(values);
    Console.WriteLine(string.Join(",", result2));
}

// 共通化したパターン
private string[] GetValue1(string[] values)
{
    var result = new List<string>();
    foreach (var val in values)
    {
        if (val.Length >= 3)
        {
            result.Add(val);
        }
    }

    // リストを配列に変換
    return result.ToArray();
}

// 引数に切り取る文字数を追加したパターン
private void button2_Click(object sender, EventArgs e)
{
    var values = new string[] { "A", "BB", "CCC", "DDDD", "EEEEE" };

    var result = GetValue2(values, 4);

    Console.WriteLine(string.Join(",", result));
}

// 4文字以上の文字列を切り取るように引数を追加
private string[] GetValue2(string[] values, int len)
{
    var result = new List<string>();
    foreach (var val in values)
    {
        if (val.Length >= len)
        {
            result.Add(val);
        }
    }

    return result.ToArray();
}
```

# デリゲートを定義する方法

- メソッドを引数に与えることで使いやすいように作っていくことを**デリゲート**という
- デリゲートを使う場合は、**戻り値、名前、引数**を宣言する
- .NET フレームワーク初期の段階からある書き方

```c#
// デリゲート
private void button3_Click(object sender, EventArgs e)
{
    var values = new string[] { "A", "BB", "CCC", "DDDD", "EEEEE" };

    // デリゲートを引数に渡すためにメソッドを定義して渡す
    var result = GetValue3(values, Shiki1);

    Console.WriteLine(string.Join(",", result));
}

// デリゲートを使う場合は宣言する
// 戻り値がなければ void でも良い
delegate bool LenCheck(string value);

// 定義したデリゲートを引数に追加
// LenCheck で定義した型を、lenCheck という変数で受けるという意味
private string[] GetValue3(string[] values, LenCheck lenCheck)
{
    var result = new List<string>();
    foreach (var val in values)
    {
        // string 受けて bool を返すようになっているので、string型の値を渡す
        if (lenCheck(val))
        {
            result.Add(val);
        }
    }

    return result.ToArray();
}

// デリゲートで定義した型と合わせる
private bool Shiki1(string value)
{
    // valueの文字列が3かどうかを判定してboolを返す
    return value.Length == 3;
}

// valueの文字列が4文字以上のときにtrueの判定を返すメソッドShiki2を作って渡す
// GetValue3の内容は変わっていないので使いまわしやすい
private void button4_Click(object sender, EventArgs e)
{
    var values = new string[] { "A", "BB", "CCC", "DDDD", "EEEEE" };
    var result = GetValue3(values, Shiki2);

    Console.WriteLine(string.Join(",", result));
}

private bool Shiki2(string value)
{
    return value.Length >= 4;
}
```

# デリゲートの引数を 2 つ渡す

- 判定する文字列の桁数を変えたいときにどのように実装するか
- 今の書き方だと、判定するためのメソッドを書かなければならないので面倒

```c#
private void button5_Click(object sender, EventArgs e)
{
    var values = new string[] { "A", "BB", "CCC", "DDDD", "EEEEE" };

    // ここの引数に渡す数値を変えてやれば、判定する文字列の長さを変えることができる
    var result = GetValue5(values, 2, Shiki4);

    Console.WriteLine(string.Join(",", result));
}

// stringとintを渡したらboolを返す書き方
delegate bool LenCheck5(string value, int len);

private string[] GetValue5(string[] values, int len, LenCheck5 lenCheck)
{
    var result = new List<string>();
    foreach (var val in values)
    {
        // 文字列と文字列の長さを示す引数を渡す
        if (lenCheck(val, len))
        {
            result.Add(val);
        }
    }

    return result.ToArray();
}

private bool Shiki3(string value, int len)
{
    // 文字列を判定したら真偽値を返す
    return value.Length == len;
}

private bool Shiki4(string value, int len)
{
    // lenより大きい値だとtrueを返す
    return value.Length >= len;
}
```

# 匿名メソッドの使い方

- 引数 1 つのときの書き方
- 名前をつけたり、別にメソッドを定義しなくて良いメリットが有る
- .NET フレームワークの 2.0 から入った

```c#
// GetValuesとLenCheckの定義は必要

// メソッドを別に定義したときの書き方と比較
private bool Shiki1(string value)
{
    // valueの文字列が3かどうかを判定してboolを返す
    return value.Length == 3;
}

// 匿名メソッド
private void button6_Click(object sender, EventArgs e)
{
    //delegate bool LenCheck(string value);
    var values = new string[] { "A", "BB", "CCC", "DDDD", "EEEEE" };
    var result = GetValue3(
          values,
          // 匿名メソッドを使ったときの書き方
          delegate (string value)
          {
              return value.Length == 3;
          }
        );

    Console.WriteLine(string.Join(",", result));
}
```

# Predicate（プレディケート）と使い方

- デリゲートの定義が必要なくなる
- 型指定がジェネリックで入っている？

```c#
// Predicate
// プレディケート
// delegate bool LenCheck(string value);
private void button8_Click(object sender, EventArgs e)
{
    var values = new string[] { "A", "BB", "CCC", "DDDD", "EEEEE" };
    var result = GetValue8(
          values,
          delegate (string value)
          {
              return value.Length == 3;
          }
        );

    Console.WriteLine(string.Join(",", result));
}

// boolを戻り値に取る引数1つという形であれば、デリゲートの定義は必要なくプレディケートを使用するだけで同様に機能を実装できる
private string[] GetValue8(string[] values, Predicate<string> predicate)
{
    var result = new List<string>();
    foreach (var val in values)
    {
        if (predicate(val))
        {
            result.Add(val);
        }
    }

    return result.ToArray();
}
```

# 機能の振り返り

- .NET 1.0
  - デリゲート（delegate）
- .NET 2.0
  - 匿名メソッド
  - プレディケート（Predicate）
  - ジェネリック
- .NET 3.0
  - ラムダ式
  - Linq
- .NET 4.0
  - リフレクション
  - Async
  - Await

# ラムダ式の書き方

```c#
// デリゲートの書き方
delegate(string s) { return s.Length == 3;}

// ラムダ式の書き方
// 左辺がパラメータ、右辺が「式」or「文」
s => s.Length == 3;
```

# 匿名メソッドとの比較

```c#
// 匿名メソッドの書き方
delegate(string s)
{
  return s.Length == 3;
}

// ラムダ式の書き方
// 左辺のパラメータの型は書かなくて良い

// 左辺のパラメータが0個の場合
() => Console.WriteLine("通知されました");

// 1個の場合
s => s.Length == 5;

// 複数の場合
(a, len) => a.Length == 5 + len;
```

# 処理を書く必要がある場合

```c#
// 右辺が式のみの場合はreturnを書かなくて良い
s => s.Length == 5;

// 右辺が式や処理の場合
(a, len) =>
{
  if(a[0] == 'E')
  {
    return s.Length > len;
  }

  return false;
}
```

# ラムダ式の実装

```c#
private void button9_Click(object sender, EventArgs e)
{
  var values = new string[] { "A", "BB", "CCC", "DDDD", "EEEEE" };

  // 匿名メソッド
  var result = GetValue8(
        values,
        delegate (string value)
        {
            return value.Length == 3;
        }
      );

  Console.WriteLine("匿名メソッド：" + string.Join(",", result));


  // ラムダ式 パターン1
  var result2 = GetValue8(
        values,
        (value) =>
        {
            return value.Length == 3;
        }
      );

  // ラムダ式 パターン2
  var result3 = GetValue8(
        values,
        value =>
        {
            return value.Length == 3;
        }
      );

  // ラムダ式 パターン3
  var result4 = GetValue8(values, value => value.Length == 3);
  var result5 = GetValue8(values, value => value.Length > 3);

  Console.WriteLine("ラムダ式：" + string.Join(",", result4));
}
```
