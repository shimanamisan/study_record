###### tags: `JavaScript`

# JavaScript 学習メモ

# 実行環境

## JavaScript 実行環境

- ブラウザには様々な機能がある
  - User Interface：ユーザーからの画面操作を受け付けるインターフェース
  - Data Strage：保存する領域
  - Rendering Engine：画面描写を司るエンジン
  - JavaScript Engine：JavaScript が実行される環境
- Chrome は V8 という名前の JavaScript Engine が使用されている
- V8 エンジン自体はオープンソースで様々なソフトウェアに JavaScript を実行する環境を組み込むことが可能
- このように様々なソフトウェアの中で動く JavaScript のことを、**Universal JavaScript**と呼ぶ

### API

- JavaScript Engine では様々な API が提供されている
  - DOM API
  - WebRTC
  - Fetch API
  - XHR API
  - Geolocation API

### まとめ

- JavaScript は **JavaScript エンジン**で実行される
- 一番シェアがあるのは**V8**と呼ばれる JavaScript エンジン
- JavaScript から**Web APIs を通してブラウザを操作**する

## JavaScript

- コードの実行前にあらかじめ準備しているものとして**グローバルオブジェクト**と、**this**というキーワードがある
- グローバルオブジェクトはブラウザ上では**window オブジェクト**と呼ばれ、この中に**Web APIs**が含まれている`
- 例として`alert("");`などが挙げられる

## 実行コンテキスト

### コンテキストとは？

- コードを実行する際の文脈、状況のこと
- ブラウザ上でコードを実行された場合に、`windowオブジェクト`や`this`が使える状態など

### グローバルコンテキスト

- JavaScript 内の直下のに書かれた実行環境のこと
- `ES2016`からモジュールを使うとグローバルコンテキストの代わりに**モジュールコンテキスト**が使える様になる

### グローバルコンテキスト内で使用できるもの

1. 実行中のコンテキスト内で宣言した変数と関数が使える
2. グローバルオブジェクト
3. `this`

```js
// この様にJavaScript内直下で宣言されたものが、
// グローバルコンテキスト内で宣言された変数と関数となる
let a = 0;
function b() {}

b();
```

## 関数コンテキスト

- 関数を宣言した際に`{}`で囲まれている部分のこと

### 関数コンテキスト内で使用できるもの

1. 実行中のコンテキスト内で宣言した変数と関数が使える
2. `this`
3. `arguments`
4. 外部変数
5. `super`：特殊な環境でのみ使用可能

```js
// この関数の波括弧で囲まれたブロックが関数コンテキストとなる

let a = 0; // 関数の外で宣言された変数が外部変数

function b() {
  console.log(this, arguments, a); // 最後のaは外部変数を参照している
}
```

## コールスタック

- 実行中のコードがたどってきた、**コンテキストの積み重ね**のこと
- コールスタックの特徴として、あとから積み上がってきたものから実行されて消滅していく
- `Chrome`の開発ツールでもみることができる

```js
function a() {}

function b() {
  a();
}
function c() {
  b();
}
c(); // グローバルコンテキスト内にある関数

// c()が呼び出されてコールスタックに積まれる
// 次にc()の中でb()が呼ばれているので、b()がコールスタックに積まれる
// b()の中でa()が呼ばれているので、a()がコールスタックに積まれる
// そこから順番にa()の処理から順番に実行されていく
// 処理が完了した段階で、コールスタックから消滅していく
// 最後にグローバルの処理が完了する
```

## コールスタックの仕組み

- コールスタックは**後から積み重なったものから先に消滅していく**と言う仕組みになっている
- このような仕組みを**Last In, First Out：後入れ先出し**と言う
- **First In, First Out**と言うものもある

# ホイスティング

- コンテキスト内で宣言した変数や関数の定義を**コード実行前にメモリに配置する**こと
- 別の呼び方で**宣言の巻き上げ**とも言う
- 変数`var`はホイスティングが行われる（しかし、**var の使用は非推奨になっているので極力使わないようにする**）
- プログラムは上から順番に実行されるが、ホイスティングが行われると**変数や関数の宣言の前の行に、変数の参照や関数の実行を行ってもエラーにならない**
- `let`や`const`では、JavaScript エンジンによる`undefined`の初期化が行われない

```js
a(); // 関数は実行前にメモリ上に配置されているのでコンソールログに表示される

function a() {
  console.log("a !!");
}

// var では変数宣言時に値が代入されていないとJavaScriptエンジンによって自動的に undefined が代入される
var b;

console.log(b); // var と言う変数の宣言を見つけたら、undefined と言う特殊な値を代入している

var b = 0;

console.log(c); // 変数を const で定義した場合はホスティングが行われないので error
console.log(d); // 変数を let で定義した場合はホスティングが行われないので error

// let や const では、宣言時にメモリスペースは確保されるが、JavaScriptエンジンによる undefined 初期値の設定は行われない
const c = 0;
let d = 0;
```

- **コンテキストが生成されるごとにホイスティングが発生し宣言部がメモリ上に展開される**
  - 例えば、関数コンテキストの宣言時など

```js
a();

function a() {
  console.log(c);
  let c = 1; // error

  d();

  // ホイスティングが行われて、関数の実行を宣言の前においてもエラーが出ない
  function d() {
    console.log(" 関数dが呼ばれました ");
  }
}
```

## 関数宣言と関数式の挙動の違い

```js
// ホイスティングが行われる
function a() {}

// 変数のホイスティングと挙動が同じになるので注意
const a = function () {};
```

# JavaScript エンジンによる挙動の違い

- Firefox は`スパイダーモンキー`
- Chorome は`V8`
- **微妙であるが、JavaScript エンジンによって挙動が違うので注意**

# スコープ

- 実行中のコードから値と式が参照できる範囲のこと（コード 1 行から見える値と式のこと）

## スコープの種類

1. グローバルスコープ
2. スクリプトスコープ
3. 関数スコープ
4. ブロックスコープ
5. モジュールスコープ

# グローバルスコープとスクリプトスコープ

- `debugger;`というコードを記述すると、その行で実行が止まる
- グローバルスコープは変数宣言や関数宣言を`var`や`function`で行うと`Global Scope`に入る
- スクリプトスコープは変数宣言や関数宣言を`let`や`const`で行うと`Script Scope`に入る
- window オブジェクト ＝ グローバルスコープ
- 使い勝手の点で違いはないので、一般的にスクリプトスコープもグローバルスコープと呼ばれる（しかし、実行結果が違ってくる場合があるので注意しておく）

```js
let a = 0;
var b = 0;
function c() {}

console.log(b);

window.d = 1; // グローバルオブジェクトに変数を宣言
console.log(d); // グローバルオブジェクト（windowオブジェクト）は省略可能

let d = 2; // スコープチェーンの挙動により let で宣言したものが呼ばれる
console.log(d);
```

# 関数スコープとブロックスコープ

- 関数スコープとは、関数で宣言された`{}`の中のことを**関数スコープ**と呼ぶ

# レキシカルスコープ

- ソースコードが記載している場所のことで、コードを**書く場所によって参照できる変数が変わるスコープ**
- コードを決定した時点で決定するスコープなので**静的スコープと呼ばれる**

# 変数

## let const var

- `let`、`const`は ES6 から使える様になった
- `var`は現在は非推奨になっている

  | タイプ | 再宣言 | 再代入 | スコープ | 初期化    |
  | ------ | ------ | ------ | -------- | --------- |
  | let    | ×      | ●      | ブロック | ×         |
  | const  | ×      | ×      | ブロック | ×         |
  | var    | ●      | ●      | 関数     | undefined |

- 再宣言：一度宣言した関数をもう一度宣言する
- 再代入：一度宣言した関数に違う値を代入する（変数を変更できるかどうか）
- スコープ：参照できる範囲のこと
  - `let`、`const`は関数スコープは生成される
- 初期化：`let`、`const`はホスティングによる初期化は行われない（エラーになる）
  - `var`はホスティングによる初期化が行われて、`undefined`が入る

## データ型

- null は**データが入っておらず、変数が空という意味**
- 2020 年 5 月時点で`Bigint`は Safari 未対応
- オブジェクトとはプロパティと値を対で格納した入れ物

| 型           | 英名      | 例           |
| ------------ | --------- | ------------ |
| 真偽値       | Boolean   | true / false |
| 数値         | Number    | 12           |
| 文字列       | String    | "Helloe"     |
| undefined    | Undefined | undefined    |
| null         | Null      | null         |
| シンボル     | Symbol    | 一意の値     |
| Bigint       | Bigint    | 12n          |
| オブジェクト | Object    | {a: "value"} |

## 暗黙的な型変換

- 変数が呼ばれた状況によって**変数の方が自動的に変換**されること
- 例として**文字列**として宣言した値が、状況によって**数値**に変換されること
  - `"1" => 1`

### JavaScript は動的型付け言語

- 変数宣言時に**型の宣言無し**
- 変数を使用する**状況によって変数の型が変更される**
  - `let num = 0`

### 静的型付け言語

- C 言語や JAVA など
- 変数宣言時に**型の宣言を行う**
- 変数を使用する**状況に左右されずに常に同じ型を維持**
  - `int num = 0`

## 変換の例

- **数値の 0 は**真偽値に変換したときに`false`になる

```js
let a = 0; // 数値

let b = "1" + a; // 1の文字列の方に、変数aの数値が変換れている
console.log(typeof b, b); // String 10 文字列が結合された形になっている

let c = 15 - b; // マイナス演算子は計算でしか使われないため、変数bが数値に変換されて、15 - 10 が計算されている
console.log(typeof c, c); // Number 5

let d = c - null; // nullは空を意味していて、nullが数値の0に暗黙的に変換されて、5がそのまま出力される
console.log(typeof d, d); // Number 5

let e = d - true; // 真偽値は数値の計算で使用された場合は、trueは1、falseは0として数値計算に使用される
console.log(typeof e, e); // Number 4
```

### 明示的に型を変換する関数もある

- `parseInt("1")`：文字列が数値に変換される

## 厳格な等価性と抽象的な等価性

- 等価性とは値を比較したときに同じとみなせるか、ということ

### 厳格な等価性

- `a === b`：型の比較あり

### 抽象的な等価性

- `a == b`：型の比較なし
- 抽象的な等価性では、まず**暗黙的な型変換が行われた後に、厳格な等価性で比較されることを覚えておく**

```js
let a = 1;
let b = true;

console.log(b == a); // true

// 流れとしては以下のような感じ
console.log(b === Boolean(a)); // 変数bの真偽値に合わせて、変数aが型変換されて厳密な比較が行われる
```

### 空文字

- 空文字は抽象的な等価性だと、数値の 0 と同じとみなされる
- 空文字は数値に変換できないので、真偽値に変換されてから比較される
- **文字列は Boolea に変換すると`true`になる**

```js
function printEquality(a, b) {
  console.log(a === b);
  console.log(a == b);
}

let e = "";
let d = 0;

printEquality(e, d);

let g = null;
let h; // undefined が 設定される

printEquality(g, h);
// g === h false
// g == h true
// 抽象的な比較は、データの型が違っても判定がtrueになることがあるので注意
```

### どちらを使うのか？？

- 思わぬバグを生む原因になるので、**厳格な等価性を使用すること**

## falsy と truthy

- falsy：**Boolean**で真偽値に変換した場合に**false**になる値
  - `false`, `null`, `0`, `undefined`, `0n`, `Nan`, `""`
- truthy：**Boolean**で真偽値に変換した場合に**true**になる値
  - 上記`falsy`の値以外のもの

```js
// 戻り値がNaNになる変換
let a = parseInt("");

console.log(a); // NaN
// 空文字は数値に変換できないのでNaNが帰ってくる

console.log(Boolean(a)); // false
// NaNはfalsyになる値
```

## AND 条件と OR 条件

- `a && b`：どちらも`truthy`だった場合に`true`を返す
  - AND 条件の動きは、`a`が`falsy`だった場合には、`a`の値をそのまま返す
  - `a`が`truthy`だった場合は、`b`の値を返す
  - まとめ：AND 条件は、`falsy`の値があった時点でその値を条件の結果として返し、すべて`truthy`であれば**条件式の一番最後の値を条件の結果として返す**
- `a or b`どちらか`true`だった場合
  - `a`の値の評価が`falsy`だった場合に`b`の値を判定しに行く
  - `a`の値の評価が`truthy`だった場合は`a`の値を条件の結果として返す
  - まとめ：OR 条件は、`truthy`が見つかった時点でその値を条件の結果として返し、全て`falsy`だった場合は**一番最後の値が条件の結果として返却される**

```js
const a = 0;
const b = 1;
const c = 3;
const d = 0;

console.log(a && b && c); // 0
console.log(a || b || c); // 1
console.log((a || b) && c); // 3
console.log((a || b) && (c || d)); // 1 && 3 => 3
```

## AND 条件と OR 条件（応用）

- 関数の中で引数で渡ってきた値が`falsy`だった場合に初期値を設定する、など利用される場合が多い
- 数値を渡す場合は、0 は`falsy`になるので注意が必要

```js
function hello(name) {
  name = name || "Tom"; // 値がfalsyな場合に、OR条件により"Tom"という初期値が設定される
  console.log("Hello " + name);
}

hello("Bob"); // truthyになる値を渡した場合は、その内容が出力される

// ES6から引数に初期値を設定できるようになっているので、そちらを使用するケースも有る
function hello(name = "Tom") {}

// AND条件を利用した応用例
let name;
name && hello(name); // nameがtruthyだった場合に処理を実行
```

## プリミティブ型とオブジェクト

### データ型とは

- 文字列、数値など**異なる値の型をデータ型という**

### プリミティブ型

1. String
2. Number
3. Boolean
4. Undefined
5. Null
6. Symbol
7. BigInt

- 変数には**値が格納される**
- 一度作成するとその値を変更することは出来ない
- **imuutable なものである（不変）**
- 再代入をする際に宣言した**プリミティブ値**は別のメモリ空間に確保され、変数がそれを参照している形である
- `let`の再代入の概念は変数の中の値が変わったのではなく、**変数が参照しているメモリの番地が切り替わった**ということ

### オブジェクト型

- **プリミティブ型以外のものすべて**
- オブジェクトは**参照が保持される**
- **mutable なものである（可変）**
- **オブジェクトの実体が変更されても、変数やオブジェクトの参照が変更されるわけではないので、ミュータブルと呼ばれる**

```js
let a = {
  prop: "hello",
};

// aという変数はオブジェクトへの参照を保持している（メモリのどこか）
// オブジェクトの参照は、オブジェクトが格納されている実体を参照している
// オブジェクトの実体は"hello"という値を参照する

// 重要なのは、helloという値はメモリ空間のどこかに格納されていて、その値をオブジェクトが参照して保持いるということ
```

- オブジェクトは、名前（プロパティー）と Value（値）を対に格納した入れ物、と呼んできたがもっと本質的に言うと ↓
- オブジェクトは、名前（プロパティー）付きの**参照を管理する入れ物**である

## 参照とコピー

### プリミティブ型のコピー

- プリミティブ型の値を他の変数に渡した場合、それぞれの変数の値は独立している（コピーされている）
- どちらかの値を変更しても、もう一方の値が変更されることはない

### オブジェクトのコピー

- オブジェクトを他の変数にコピーした場合は、**オブジェクトの参照がコピーされる**ので、コピー先の変数でプロパティの値を変更した場合に、コピー元の変数のプロパティの値も変更される

```js
// プリミティブ型のコピー
let a = "hello";
let b = a;
b = "bye";
console.log(a, b); // hello bye どちらの値も独立している

// オブジェクトのコピー
let c = {
  prop: "hello",
};
let d = c;
console.log(c, d); // {prop: "hello"} {prop: "hello"}

d.prop = "bye";
console.log(c, d); // {prop: "bye"} {prop: "bye"} コピー元のプロパティも変更されている

d = {};
console.log(c, d); // {prop: "bye"} {} 変数d は新しいオブジェクトを参照するようになるので、それぞれのオブジェクトのプロパティなどを変更しても影響しない
```

## 参照と const

-

```js
const a = "hello";
// a = "bye"; 再代入はエラーになる

const b = {
  prop: "hello",
};
// b = {}; // オブジェクトへの参照がロックされているので、オブジェクトの再代入は出来ない
b.prop = "bye"; // オブジェクトの中のプロパティは変更できる
console.log(b);
```

## 参照と引数

- 関数の引数に変数を渡した場合は、**引数が渡ってきた変数を参照しているかたちになる**
- 引数の場合も、値がコピーされるのか、参照がコピーされているのか意識する！

```js
// プリミティブ型
let a = 0;

function fn1(arg1) {
  arg1 = 1;
  console.log(a, arg1); // プリミティブ型の値はそれぞれ独立してる
}
fn1(a);

let b = {
  prop: 0,
};

// オブジェクト型
function fn2(arg2) {
  arg2.prop = 1;
  console.log(b, arg2); // {prop: 1} {prop: 1}
}

fn2(b);

// オブジェクトを新しくする場合
function fn3(arg2) {
  arg2 = {};
  console.log(b, arg2); // {prop: 1} {}
}

fn3(b);
```

## 参照と分割代入

- オブジェクトから**オブジェクトから特定のプロパティを抽出するときに使用する**
- `let {a, b} = object;`：オブジェクトのプロパティが変数 a,の初期値となる
- 分割代入で抽出した値は、**オブジェクトの参照から切り離されているので、値を変更しても影響がない**
- 分割代入する際は、**プロパティの値と変数は同じにしなければならない**

```js
const a = {
  prop: 0,
};

// let {prop: b} = a; 分割代入の変数をプロパティの名前から変更するやり方

let { prop } = a;

prop = 1;

console.log(a, prop);

function fn({ prop }) {
  // 引数で分割代入を使用すると、渡ってきた時点でオブジェクトからプロパティが抽出された状態になっている
  // 元のオブジェクトの中身は変更されない（抽出した時点でオブジェクトのプロパティをは違う変数になっている）
  prop = 1;
  console.log(a, prop);
}

fn(a);

const c = {
  prop1: {
    prop2: 0,
  },
};

// 分割代入した時点で、prop2への参照が保持されて抽出される
let { prop1 } = c;
// prop1の中のprop2の値が変更される
// prop1はprop2への参照を保持しているので、参照先の値が変われば元のオブジェクトcから見たときも値は変更されている
prop1.prop2 = 1;

console.log(c, prop1);
```

## 参照の比較と値の比較

- オブジェクトの比較は**オブジェクトの参照先を比較することになる**ので、メモリ番地が異なることになり判定は`false`となる
- オブジェクトを比較する際は**プロパティの値同士を比較する**
- プロパティの値は、オブジェクトが参照しているプロパティの**値**を取得していることになるので、比較することが出来る

# 関数とオブジェクト 1

## 関数

- JavaScript では渡ってくる引数の順番が重要になってくる
- 同じ関数を宣言した場合は**後から宣言した物によって上書きされる**
- 関数の引数の数によって**別の関数と判断されないので注意！**
- 関数式を使って宣言すると、**同じ関数があった場合にエラーが出る**のでそのようなやり方もある
- 引数の初期値で意図してからであることを伝えるときは`null`を使用する。`undefined`を初期値として代入することは無い
- `return`を使用すると関数の実行結果で値を返却することが出来る

```js
function fn(a, b) {
  console.log(a, b); // 1, undefined
}

// 後から宣言した関数で上書きされる。エラーも出ない
// function fn(a, b) {
// 	console.log(2); // 2
// }

// let c = undefined; // これはやらない
// let c = null; // こっちを使う

fn(1); // 引数の値の数や順番で出力結果が違ってくる
```

## arguments

- 関数コンテキスト生成時に自動的に作られるので、自分で宣言することはない
- 引数の役割を arguments が代わりに行うので、関数の引数を指定しなくても値を渡せる
- ES6 か**レスとパラメータが追加されたので**、最近では利用されない
- このような形で可変長の引数にも対応：`function fn({...args})`
- **arguments**は配列ほど便利に扱えない

```js
function fn(a, b) {
  // const a = arguments[0]; // 引数の役割をargumentsが代わりに行うので、関数の引数を指定しなくても値を渡せる
  // const b = arguments[1];
  // console.log(arguments);
  console.log(a, b);
}

let c = fn(1, undefined);
```

## 関数とオブジェクト

- **関数は実行可能なオブジェクトである**
- 関数宣言した関数に、ドット記法でプロパティやメソッドを追加することが出来るので、**関数はオブジェクトであるということが言える**
- 丸かっこ`()`を使用することで関数を実行することが出来る

```js
function a() {
  console.log("hello");
}

a.prop = 0; // 関数にpropというプロパティが追加される
a.method = function () {
  // method()というメソッドが関数オブジェクトに追加されている
  console.log("method");
};

console.log(a);
```

## コールバック関数

- コールバック関数とは、**他の関数の引数として渡せる関数のこと**
- **関数はオブジェクトなので、引数として渡すことが出来る**
- 丸かっこ`()`を使用することで、**実行可能なオブジェクトを実行している**という意味になる
- コールバック関数を使用して、一部の処理を切り出すことでコードの使い回しが実現できる

```js
function hello(name) {
  // コールバック関数に引数を定義する事もできる
  console.log("hello " + name);
}

function bye() {
  // 引数の数が異なってもエラーが出ない
  console.log("bye");
}

function fn(cb) {
  cb(); // 渡ってきた関数が内部で実行される。これがコールバック関数
}

fn(hello); // コールバック関数を渡している
fn(bye);
// 無名関数でコールバック関数を定義
fn(function (name) {
  console.log("hello " + name);
});

// よく使われるコールバック関数の実例
setTimeout(hello, 2000); // 第一引数にコールバック関数
// setTimeout内では引数を渡さずコールバック関数が実行されている
// 引数を実行しない関数に引数を渡す方法もある
```

## this

- **this とは呼び出し元への参照を保持するキーワード**
- 呼び出し元とはオブジェクトを経由してメソッドなどが呼び出されるので、そのように表現される
- 直接メソッドが呼び出された場合は、**呼び出し元はオブジェクトにならない**
- 主に関数コンテキストで使用される
- 実行コンテキストによって`this`の参照先は変わる

```js
const person = {
  name: "Tom",
  hello: function () {
    console.log("Hello " + this.name); // personのオブジェクトを参照している。その中のプロパティを読み取っている。
  },
};
person.hello();
```

## 参照のコピーと`this`

- オブジェクト内のメソッドを**変数にコピーしてから実行すると、コンテキストが変化するので this でプロパティなどを呼び出せない！**
- 変数にコピーして実行すると、**グローバスコンテキストを参照しに行く**

### オブジェクトと呼び出し元

- オブジェクトのメソッドとして実行される場合、**"this"は呼び出し元のオブジェクトを参照**
- 関数として実行された場合は、**"this"はグローバルオブジェクトを参照する**

```js
window.name = "John";

const person = {
  name: "Tom",
  hello: function () {
    console.log("Hello " + this.name); // person.name = "Tom" を参照
    a(); // 関数を実行した場合は windowオブジェクトを参照 "John"

    const person = {
      name: "Tim",
      hello: function () {
        console.log("Hello " + this.name); // person.name = "Tim"を参照
        a(); // 関数を実行した場合は windowオブジェクトを参照 "John"
      },
    };
    person.hello();
  },
};

// const ref = person.hello;
// ref();

person.hello();

function a() {
  console.log("Hello " + this.name); // このthisはグローバルオブジェクトを参照する
}
```

## コールバック関数と"this"

- オブジェクトのメソッドを**引数に渡した段階で、それは既に関数になるので呼び出し元のオブジェクトはグローバルオブジェクトになる**

```js
window.name = "John";

const person = {
  name: "Tom",
  hello: function () {
    console.log("Hello " + this.name);
  },
};
person.hello();

function fn(ref) {
  // オブジェクトのメソッドを引数という「変数」に渡して「関数」として実行している
  // 「関数」を実行すると"this"の参照先（呼び出し元のオブジェクト）はグローバルオブジェクトになる
  ref();
}

// オブジェクト内の「関数」を渡している
fn(person.hello); // "Hello John"
```

## bind と this

- `bindメソッド`を使用することで、**this の向き先（呼び出し元のオブジェクト）を固定出来る**
- `bind`は**引数も固定することが出来る**
- `bind`で**this や変数を固定することで、元の関数とは別の関数としてメモリ空間に生成される**
- この挙動を**this を束縛する**という
- **this を束縛した場合は、とのようなコンテキストでも this の参照先を固定できる**

```js
window.name = "John";

const person = {
  name: "Tom",
  hello: function () {
    console.log("Hello " + this.name);
  },
};
person.hello();

// bindの第一引数で設定したオブジェクトをhelloの中で実行されるthisの参照先に設定する
// そして変数の中に新しい関数として格納している
const helloTom = person.hello.bind(person);

function fn(ref) {
  ref();
}

fn(helloTom); // Hello Tom

function a(name) {
  console.log("hello " + name);
}

// 第二引数で変数の値を固定することも出来る
// その際は第一引数にオブジェクトを渡す必要はないので、nullを渡してやる
const b = a.bind(null, "Tim");

b();
```

## call、apply と "this"

### "this"

- bind で this を束縛した時点では**実行されない**

### call、apply

- bind で this や引数を束縛できるが、**使用した時点で関数が実行される**
- `apply`は**ES6 の書き方でスプレッド演算子を使用することが多い**

```js
function a(name, name1) {
  console.log("hello " + name + " " + name1);
}

const tim = { name: "Tim" };

const b = a.bind(tim);

b();

// この時点で関数の実行まで行われる
a.apply(tim, ["Tim", "Bob"]); // applyの第二引数では配列を渡す
// この時点で関数の実行まで行われる
a.call(tim, "Tim", "Bob"); // callの場合は、第二引数以下が関数の引数に渡っていく

const arry = [1, 2, 3, 4, 5];

// const result = Math.max.apply(null, arry); // 第一引数はオブジェクト、第二引数は配列
const result = Math.max(...arry); // スプレッド演算子のほうが簡単に書ける（ES6より使用可能）
console.log(result);
```

## アロー関数

- ES6 で追加された**無名関数を簡略した記述方法**

```js
function a(name) {
  return "hello " + name;
}

// 引数が一つの場合はかっこを省略できる
// 実行行が1行の場合は、なみかっっこ{} と return が省略することが出来る
const b = (name) => "hello " + name;

// まるかっこが面倒なときに、ダミーの変数を入れて記述していることもある
// const c = _ => "hello ダミー";

console.log(b("Tom"));
```

## アロー関数と'this'

|           | 無名関数 | アロー関数 |
| --------- | -------- | ---------- |
| this      | ●        | ×          |
| arguments | ●        | ×          |
| new       | ●        | ×          |
| prototype | ●        | ×          |

- アロー関数は、呼び出し元がオブジェクトや関数だったとしても**その実行コンテキストの this は無視されてグローバルオブジェクトを参照しに行くことに注意！**

```js
window.name = "John";

// const a = () => console.log('Bye ' + this.name); // ここの呼び出された関数のレキシカルスコープは、グローバルスコープになる // Bye John

// グローバルコンテキスト
const person = {
  name: "Tom",
  hello() {
    // hello: () => {} 無名関数がhelloプロパティに格納されているという意味
    console.log("Hello " + this.name);
    // アロー関数で宣言
    // const a = () => console.log('Bye ' + this.name); // ここの呼び出された関数のレキシカルスコープは、hello()のスコープになる // Bye Tom
    // a();
  },
};
// person.hello();

// グローバルコンテキストで宣言、関数呼び出し
function b() {
  // 関数コンテキスト内でアロー関数で宣言、a()のレキシカルスコープは function b(){} が参照するスコープになる
  const a = () => console.log("Bye " + this.name);
  a();
}

b(); // Bye John
```

# 関数とオブジェクト 2

## コンストラクター関数

- 新しく**オブジェクトを作成**するための雛形となる関数で、定義方法は一般的な関数と変わりない
- オブジェクトを大量に生成する際に役に立つ

```js
// 例
function A() {
  this.prop = 0;
}
```

## 呼び出し方

- `new演算子`を使用してインスタンスを生成することを、**インスタンス化**を言う
- それによって生成されたものを**インスタンスおよびインスタンスオブジェクト**という

```js
const obj = new A();
```

## 例

- コンストラクター関数の頭文字は**大文字で定義するのが一般的**

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}

const bob = new Person("Bob", 18);
const tom = new Person("Tom", 33);
const sun = new Person("Sun", 20);
```

## prototype

### プロトタイプの定義

- オブジェクトに存在する特別なプロパティー
- コンストラクター関数と合わせて使用する

### プロトタイプの使い方

- オブジェクトに新しいメソッドを定義する事ができる
- 新しいインスタンスを生成した際に、プロトタイプに追加したメソッドが繰り返し使える
- インスタンス化した際は**prototype オブジェクトの参照が proto** にコピーされる
- プロトタイプは通常のインスタンス化よりも**余分なメモリを消費しないようにするための、JavaScript における重要な技術**

```js
// コンストラクター関数を定義
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.hello = function () {
  console.log("hello " + this.name);
};

const bob = new Person("bob", 18);
const tom = new Person("tom", 45);
const sun = new Person("sun", 34);

bob.hello(); // hello bob
tom.hello(); // hello tom
sun.hello(); // hello bun

// インスタンスに __proto__ が追加されている
// __proto__ はプロトタイプの参照のコピーになるので、Person.prototype と を比較するとtrueになる
// Personオブジェクトのhelloメソッドと、プロトタイプのhelloメソッドは同じ参照先となる
console.log(Person.prorotype === bob.__prot__); // true
```

## new 演算子

### new 演算子の定義

- コンストラクター関数からインスタンスを作成するために使用する演算子
- コンストラクター関数内の記述によっては挙動が異なってくる

```js
// コンストラクター関数の戻り値がオブジェクトの場合
return {
  /*
   */
}; // new したらオブジェクトが返ってくる {}

// コンストラクター関数の戻り値がオブジェクト以外、及び戻り値が定義されていない場合は
// プロトタイプを __proto__ にコピーする
// そしてコンストラクター関数内で使用されているthisを呼び出し元に返却する
```

- `Object.create()`は、\***\*prot**に、引数で指定したオブジェクトをコピーして、更に空のオブジェクトを返す\*\*

```js
function F(a, b) {
  this.a = a;
  this.b = b;
  // return {};
}

F.prototype.c = function () {};

// new演算子というのは、内部でnewOpe()のような処理を行っている
// ...args 引数で渡ってきたものを配列で返す
function newOpe(C, ...args) {
  // Object.create()を使用して、thisというオブジェクトをprototypeの参照を保持した状態で生成する
  // Object.createは__prot__に、引数で設定した C.prototype をコピーして、空のオブジェクトを返却している
  // _thisはprototypeが__proto__に格納された状態で生成される空のオブジェク
  const _this = Object.create(C.prototype);

  // そして空の_thisを引数で渡ってきたCのコンストラクター関数のthisにする方法
  // 第一引数にthisを指定して、第二引数にコンストラクター関数に渡すための値を配列で渡す
  C.apply(_this, args);
  console.log(_this);

  console.log(typeof result);
  // nullもobjectが返ってくるので、nullではないという条件を追加
  if (typeof result === "object" && result !== null) {
    return result; // 空のオブジェクトが返される
  }

  return _this;
}

const instance = newOpe(F, 1, 2);

console.log(instance);
```

## instanceof

- どのコンストラクターから生成されたオブジェクト（インスタンス）かを確認する
- インスタンスというのは`Object`オブジェクトのプロトタイプを継承していることになるので、**すべてのオブジェクトの大元となる**

```js
function F(a, b) {
  this.a = a;
  this.b = b;
  return { a: 1 };
}

F.prototype.c = function () {};

const instance = new F(1, 2);
// return {a: 1};をコメントアウトして、オブジェクトを返却すると下記の処理はfalseになる
console.log(instance instanceof F); // false

// return {a: 1}はオブジェクトリテラル new Object から生成されたインスタンスなので、Fのコンストラクター関数から生成されたものではない
// なので instance.__proto__ が参照しているのは、Object.prototype となる
console.log(instance.__proto__ === instance.prototype); // false

// 例として、オブジェクトと配列の値の追加方法は違うので、instanceof で作成元のコンストラクターを特定することで、オブジェクトの種類によって処理を変更する
function fn(arg) {
  if (arg instanceof Array) {
    arg.push("val");
  } else {
    arg["key"] = "val";
  }
  console.log(arg);
}
fn({});
fn([]);
```

## 関数コンストラクター

- **関数は実行可能なオブジェクトである**

```js
// 関数コンストラクターから定義した場合
// こちらの場合はスコープは内部的にグローバルスコープで行われるため、
// 外部変数含まれていたらスクリプトスコープからグローバルスコープに変数を探しに行く
const fn1 = new Function("a", "b", "return a + b");
const result = fn1(1, 2);
console.log(result);

// 関数宣言の場合
function fn2(a, b) {
  return a + b;
}
// fn2もFunctionコンストラクターから生成されたインスタンスである
console.log(fn2 instanceof Function);

// const obj = new function() {
// 	this.a = 0;
// }

const fn3 = new Function("this.a = 0;"); // 関数コンストラクターは脆弱性の原因にもなるので一般的には使用しない
const obj3 = new fn3();
console.log(obj, obj3);
```

## プロトタイプチェーン

- プロトタイプの**多重形成**をプロトタイプチェーンと言う
- 呼び出され方にはルール（順番）がある

### プロトタイプチェーンの優先順位

1. 自身のプロパティ
2. コンストラクターが持っている**プロトタイプのメソッド**
3. `Object`のプロトタイプ（すべてのオブジェクトの大元となるオブジェクト）
4. なければ`undefined`が返ってくる

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
  // this.hello = function() {
  //     console.log('OwnProperty: hello ' + this.name);
  // }
}

// Person.prototype.hello = function() {
//     console.log('Person: hello ' + this.name);
// }

// 最終的にこのObjectオブジェクトのプロトタイプにメソッドが見つからないと、undefined が返ってくる
Object.prototype.hello = function () {
  console.log("Object: hello " + this.name);
};

const bob = new Person("Bob", 18);
bob.hello();

// hasOwnProperty('プロパティ')にてプロパティが存在しているのか確認する
// というのは Object の __proto__ で
const result = bob.hasOwnProperty("name");
console.log(result);
```

## hasOwnProperty と in

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Object.prototype.hello = function () {
  console.log("Object: hello " + this.name);
};

const bob = new Person("Bob", 18);
const result = bob.hasOwnProperty("name"); // 自分自身のオブジェクトの中に存在するプロパティを探す true / false を返す
console.log(result);

// bobというオブジェクトのプロパティまたは __proto__ にnameという文字列のプロパティまたはメソッドが格納されているか調べる
// プロトタイプチェーンをさかのぼって探しに行く true / false を返す
console.log("name" in bob);
console.log("hasOwnProperty" in bob); // hasOwnPropertyというメソッドをプロトタイプチェーンをさかのぼって探している trueが返ってくる
```

## プロトタイプ継承

- 別のコンストラクター関数の**プロトタイプを受け継いで、機能を流用出来るようにすること**

### 継承

- 別のコンストラクター関数自体を受け継ぐこと

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.hello = function () {
  console.log("hello" + this.name);
};

function Japanese(name, age) {
  // この関数コンテキストのthisがPersonに渡っていき、this.nameとthis.ageが設定される
  Person.call(this, name, age);
}

// Personのプロトタイプが、Japaneseのプロトタイプの中に含まれるプロトタイプとなる（ややこしい。。。）
Japanese.prototype = Object.create(Person.prototype);

const taro = new Japanese("Taro", 23);
console.log(taro);

taro.hello();
```

## クラス

- コンストラクター関数をクラス表記で書けるようにしたもの
- シンタックスシュガー：**すでにある書き方を簡単に書けるよにしたもの**
- ES6 から基本は**クラス表記で記述するが、**JavaScript の裏の処理の理解としてプロトタイプの処理は把握しておくこと！

```js
// クラス定義の方法
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  hello() {
    console.log("hello " + this.name);
  }
}

// // コンストラクター関数記法
// function Person(name, age) {
//   this.name = name;
//   this.age = age;
// }
// Person.prototype.hello = function() {
//   console.log('hello ' + this.name)
// }

const taro = new Person("Taro", 24);

// クラス表記を使って生成したインスタンスもオブジェクトであることを確認
console.log(taro);
```

## クラス継承

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  hello() {
    console.log("hello " + this.name);
  }
}
// function Japanese(name, age, gender) {
//   Person.call(this, name, age);
//   this.gender = gender;
// }

class Japanese extends Person {
  constructor(name, age, gender) {
    super(name, age, gender); // 継承したPersonのconstructor()が実行される
    this.gender = gender;
  }

  hello() {
    console.log("Konnichiwa " + this.name);
  }
  bye() {
    console.log("Sayonara " + this.name);
  }
}

// Japanese.prototype = Object.create(Person.prototype);

// Japanese.prototype.hello = function() {
//   console.log('Konnichiwa ' + this.name);
// }

// Japanese.prototype.bye = function() {
//   console.log('Sayonara ' + this.name);
// }

const taro = new Japanese("Taro", 23, "Male");

console.log(taro);
```

## super

- 継承元の関数を呼び出すためのキーワード
- クラス表記となっても、内部的にはコンストラクター関数を実行しているので、**関数コンテキストにコールスタックが積まれていく**
- `super`は特殊な表記で、他のクラスを継承して親のコンストラクターを初期化している
- 基本的に**クラスを継承したコンストラクター関数ないか、メソッド内で呼び出される**（例外なものもある）
- 親のメソッドも呼び出すことが出来る

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  hello() {
    console.log("hello " + this.name);
  }
}

class Japanese extends Person {
  constructor(name, age, gender) {
    super(name, age, gender);
    this.gender = gender;
  }

  hello() {
    console.log("Konnichiwa " + this.name);
    super.hello(); // メソッド内でお呼び出すことが出来る
  }
  bye() {
    console.log("Sayonara " + this.name);
  }
}

const taro = new Japanese("Taro", 23, "Male");

console.log(taro);
```

### オブジェクト内で super()を使用した場合

```js
const american = {
  hello() {
    console.log("hello " + this.name);
  },
};

const bob = {
  name: "Bob",
  hello() {
    super.hello();
    // console.log('hello ' + this.name);
  },
};
// bobというオブジェクトに対して、americanのプロトタイプを追加する
// 第一引数：オブジェクト
// 第二引数：オブジェクトに継承させたいオブジェクト（プロトタイプに代入したいオブジェクト）
Object.setPrototypeOf(bob, american); // bobのオブジェクトのプロトタイプに対してamericanを部ジェクトを格納する

bob.hello();
```

## ビルドインオブジェクト

- コードの実行前に JavaScript エンジンによって**自動的に生成されるオブジェクト**
- 例として`String, Object, Number, Function, Math, Boolean, Date, Symbol`など。。。

```js
const arry = new Array(1, 2, 3, 4);
console.log(arry);
console.log(arry["0"]);

// プロトタイプチェーンをさかのぼってプロパティを探している
console.log(arry.hasOwnProperty(0)); // true

// 添字は0番目から始まるので、4という添字（プロパティ）は存在していない
console.log(arry.hasOwnProperty(4)); // false
```

## ラッパーオブジェクト

- プリミティブ値を内包するオブジェクト

```js
const a = new String("hello");

console.log(a); // Stringというラッパーオブジェクト
/*
String {
  '0': 'h',
  '1': 'e',
  '2': 'l',
  '3': 'l',
  '4': 'o',
  length: 5,
  __proto__: String {
    length: 5,
    constructor: ƒ String(),
    anchor: ƒ anchor(),
    big: ƒ big(),
    blink: ƒ blink(),
    bold: ƒ bold(),
    charAt: ƒ charAt(),
    charCodeAt: ƒ charCodeAt(),
    codePointAt: ƒ codePointAt(),
    concat: ƒ concat(),
    endsWith: ƒ endsWith(),
    fontcolor: ƒ fontcolor(),
    fontsize: ƒ fontsize(),
    fixed: ƒ fixed(),
    includes: ƒ includes(),
    indexOf: ƒ indexOf(),
    italics: ƒ italics(),
    lastIndexOf: ƒ lastIndexOf(),
    link: ƒ link(),
    localeCompare: ƒ localeCompare(),
    match: ƒ match(),
    matchAll: ƒ matchAll(),
    normalize: ƒ normalize(),
    padEnd: ƒ padEnd(),
    padStart: ƒ padStart(),
    repeat: ƒ repeat(),
    replace: ƒ replace(),
    search: ƒ search(),
    slice: ƒ slice(),
    small: ƒ small(),
    split: ƒ split(),
    strike: ƒ strike(),
    sub: ƒ sub(),
    substr: ƒ substr(),
    substring: ƒ substring(),
    sup: ƒ sup(),
    startsWith: ƒ startsWith(),
    toString: ƒ toString(),
    trim: ƒ trim(),
    trimStart: ƒ trimStart(),
    trimLeft: ƒ trimStart(),
    trimEnd: ƒ trimEnd(),
    trimRight: ƒ trimEnd(),
    toLocaleLowerCase: ƒ toLocaleLowerCase(),
    toLocaleUpperCase: ƒ toLocaleUpperCase(),
    toLowerCase: ƒ toLowerCase(),
    toUpperCase: ƒ toUpperCase(),
    valueOf: ƒ valueOf(),
    replaceAll: ƒ replaceAll(),
    at: ƒ at(),
    codePoints: ƒ codePoints()
  }
}
*/

console.log("hello".toUpperCase()); // プリミティブ型でもメソッドが対応していれば暗黙的にラッパーオブジェクトが呼ばれる
console.log(a.toUpperCase()); // Stringというラッパーオブジェクト

const b = new Number(100);
console.log(b); // 100という値が格納されたオブジェクトが生成される
console.log(b.toExponential()); // '1e+2' 100が10の二乗であるという意味
```

## Symbol

- プロパティの重複を避けるために、**必ず一意の値を返す関数**
- `Symbol`というプリミティブ型は文字列ではなく、内部的に一意となるようなデータ構造を持っている
- 引数には何らかの文字列を格納することが出来る
- ビルドインオブジェクトのプロトタイプに独自のメソッドを追加することは、**プロトタイプ汚染**とよばれ一般的にやらないので注意。あくまで検証という観点で今回はコードを書く
- ES5 から ES6 にバージョンが上がった際に、**既存で動いているコードを破壊しないために導入された一意の値を得るためのプロパティ識別子のこと**
- 基本的に JavaScript 開発者は意識することはく、JavaScript エンジン側を開発する人が頻繁に触ることになる機能

```js
const s = Symbol("hello");
const s2 = Symbol("hello");
console.log(s); // Symbol()

// 異なる値が返ってくるのでfalse判定となる
// あくまで名前をつけているだけなので、値としては同じものではない
// 呼び出しごとに変わってくる
console.log(s === s2); // false

console.log(typeof s);

const str = new String("Tom");
console.log(str);

String.prototype.iterator = function () {
  // もしバージョンが上がった際に、新しいバージョンで同じメソッドがプロトタイプい追加されたらエラーになる
};

// シンボルは同じ名前をつけても異なる値として扱われるので、競合しないプロパティを作成することが出来る
String.prototype[s] = function () {
  return "hello " + this;
};

// プロパティ自身は異なるものとして追加することが出来る
String.prototype[s2] = function () {};

const tom = "Tom";

// ドット記法だと tomのsというプロトタイプにアクセスしている意味になる
// 今回の場合は、sという変数に格納されているSymbolをtomのオブジェクトから取得したいのでブラケット記法にしている
console.log(tom[s]());
```

## プロパティーとディスクリプター

- プロパティは`value`以外に設定値を持っており、それを**ディスクリプター**を呼ぶ
- ディスクリプター：`value, configurable, enumerable, writable`
- プロパティーの設定値
  - value : 値
  - configurable : 設定変更可能性
  - enumerable : 列挙可能性
  - writable : 値の変更可能性

```js
"use strict";

const obj1 = { prop: 0 };

const descriptor1 = Object.getOwnPropertyDescriptor(obj1, "prop");

console.log(descriptor1);
/*
{value: 0, writable: true, enumerable: true, configurable: true}

オブジェクトリテラルでプロパティを定義した場合はすべての値がtrueに設定されている
configurable: true
enumerable: true
value: 0
writable: true
__proto__: Object
*/

const obj2 = {};

Object.defineProperty(obj2, "prop", {
  value: 0,
  writable: true, // 値が書き変わる
  configurable: true, // プロパティの設定の書き換えを許可
});

delete obj2.prop; // プロパティを削除する configurableがfalseだったらエラーになる

// obj2.prop = 1;
console.log(obj2.prop); // 書き込みが出来ない設定がされているので値が変わらないし、エラーにならない
// 先頭に 'use strict' を記述するとエラーにすることが出来る

const descriptor2 = Object.getOwnPropertyDescriptor(obj2, "prop");

console.log(descriptor2);
/*
{value: 0, writable: false, enumerable: false, configurable: false}

デフォルト値がfalseで設定されている
configurable: false // 設定の変更はできない
enumerable: false
value: 0
writable: false // 値の書き換えができない
__proto__: Object
*/
```

## getter/setter と static

- 何らかの操作を挟む際に使用される
- `staticメソッド`：インスタンス化を行わずともクラスから呼び出せるメソッド
- インスタンス化をしていないので、スタティックメソッドの中で`this`は使用できない

```js
function Person1(name, age) {
  this.name = name;
  this.age = age;
}

// ディスクリプターを使う
// 第一引数：ディスクリプターを使って設定したいオブジェクトを指定
// 第二引数：プロパティの名前
// 第三引数：getterとsetterを設定する
Object.defineProperty(Person1.prototype, "name", {
  // getterが呼ばれた際の動作を記述していく
  get: function () {
    console.log("getが呼ばれています");
    return this._name;
    // return 'hello';
  },
  set: function (val) {
    // 引数に設定された値が渡ってくる
    // 使用方法として他の値も同時に変更したい場合などにつかう
    this._name = val;
  },
});

// インスタンス化
const p1 = new Person1("bob", 32);

// プロトタイプで設定したプロパティの値を取りに行くような動作をするとgetterがよばれる
console.log("Person1: " + p1.name);

// ES6から簡略化して記載できる
class Person2 {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  // definePropertyを設定したのと同じ
  get name() {
    console.log("getが呼ばれています");
    return this._name;
    // return 'hello';
  }

  set name(val) {
    // 引数に設定された値が渡ってくる
    // 使用方法として他の値も同時に変更したい場合などにつかう
    this._name = val;
  }

  // インスタンス化をしなくとも呼べるメソッド
  // thisを使用するとインスタンス化していないので、クラスの記述が返却されてくる
  static hello() {
    console.log("Peson2のスタティックメソッド： hello");
  }
}
// クラス表記の場合でも、コンストラクター関数に追加されたメソッドをスタティックメソッドとして取り扱っている
Person2.hello();

const p2 = new Person2("Tom", 55);

console.log(p2);
/*
Person2 {_name: "Tom", age: 55}
age: 55
_name: "Tom"
name: (...)
__proto__:
constructor: class Person2 <==★コンストラクター関数のこと p2.constructor === Person2 // true
name: (...)
get name: ƒ name()
set name: ƒ name(val)
__proto__: Object
*/

// コンストラクター関数を使用した書き方
Person1.hello = function () {
  console.log("Peson1のスタティックメソッド： hello");
};
Person1.hello();
```

## チェーンメソッド

- 一つのインスタンスに対して**連続した処理を行う場合に使用する**
- メソッド内にて、オブジェクトの**インスタンス（this）を返すことによって**、チェーンメソッドを実現することが出来る

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  hello(person) {
    console.log(`${this.name} says hello ${person.name}`);
    return this; // Personのインスタンスを返すように追記した
  }

  introduce() {
    console.log(`Hi, I'm ${this.name}, ${this.age} years old.`);
    return this;
  }

  shakeHands(person) {
    console.log(`${this.name} shake hands with ${person.name}.`);
    return this;
  }

  bye(person) {
    console.log(`Goodbye, ${person.name}.`);
  }
}

// インスタンスを生成
const bob = new Person("Bob", 23);
const tim = new Person("Tim", 33);

// bob.hello(tim);
// bob.introduce();
// bob.shakeHands(tim);
// bob.bye(tim);

// チェーンメソッドを使用したやり方
// メソッドをつなげて一連の処理として記述することが出来る
bob
  .hello(tim) // helloの戻り値がPersonインスタンスであればチェーンすることが出来る
  .introduce()
  .shakeHands(tim)
  .bye(tim);
```

# 反復処理

## ループ文

```js
// for文の基本的な記述（2ずつ増やす方法）
for (let i = 0; i < 10; i = i + 2) {
  console.log(i);
}

// 基本的に1ずつ増やすので、i++と記述する事が多い
for (let i = 0; i < 10; i++) {
  console.log(i);
}

// while文を使ったループ処理
let i = 0;
while (i < 10) {
  console.log("hello");
  i++;
}
```

## 演算子と優先順位

- 演算子とは、値（オペランド）を元に処理を行い、**結果を返す記号**
- `A(オペランド) ? B(オペランド)`
- `a = 3`：これは**左辺に対して右辺が代入される**ということ。そして右辺の 3 という値が**返却される**
- このようなことから**演算子というのは処理が終わった後に結果を必ず返す**

### 演算子の優先順位

- [演算子の優先順位](https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)
- 優先順位は**値が大きいものが優先順位が高い**

```js
let a = (1 + 2) * 3; // かっこで囲むとグループ化して優先的に処理を行う（MDNより一番優先順位が高い）
console.log(a);

let b = 0;
console.log("変数b: " + b);

// 後置インクリメント
let c = b++; // bという値を返したあとで値がインクリメントされる（変数cに入ったあとで+1される）
console.log("変数c: " + c, b);

// 前置インクリメント
let d = 0;
let e = ++d; // 変数dの値をインクリメントした値を返した後に変数に代入する
console.log("変数e: " + e);

// 前置インクリメントの挙動
let f = 0;
let g;
// 代入演算子 = は、右から左に評価される
// 1が変数gに格納される
// 変数gが変数fに代入される
f = g = 1;
console.log("前置インクリメントの挙動: " + f, g);

// 後置インクリメントの挙動
let h = 0;
let i;
// グループ化することで、i = h が先に評価される
// hには0が入っているので、i = 0となる
// そして代入演算子が返却するするのは右辺なので、h = h + 1; という式が残っていることになる
// その後は通常の処理を行うことで、hに1が格納されていることになる
h = (i = h) + 1;
console.log("後置インクリメントの挙動: " + h, i);

function fn() {
  let a = 0;
  return a++;
}

console.log("関数fn: " + !fn() * 5); // 乗算よりもNOT演算子の方が優先順位が高いので先に処理される
console.log("関数fn: " + !(fn() * 5)); // グループ化された中で0 * 5 が処理されて0が返ってくるので、それがNOT演算子で評価されて0はfalseなのでfalseが返却されてくる
```

## ループ文とブロックスコープ

- ループ文のブロックスコープは**1 ループごとにスコープが異なる**ので、他のブロックスコープと挙動が異なる
- **ループごとに全く異なるメモリ空間を参照しているということを把握しておく**

```js
for (let i = 0; i < 5; i++) {
  // 再代入出来ないはずのconstでもエラーが出ていないので、ループごとにブロックスコープが切り替わっている
  // 各ループごとに異なる変数として宣言されている（格納されているメモリ空間は異なる）
  const j = i * 2;
  // console.log(j);

  // varはブロックスコープを持たないので、for文の外で宣言しているのと同じ
  // その結果、setTimeoutが実行される前にjには8が代入されており、8を5回実行しているのと同じ
  // var j = i * 2;
  setTimeout(function () {
    // クロージャーの挙動と似ている
    // スコープが存在している間はjという変数が保持されて、setTimeout()の内側から参照されている
    // スコープが消滅するとjという変数は一旦消滅して、setTimeout()の内側から参照されているので保持されるが、
    // 新しいループでは全く別物の変数jとなる
    console.log(j);
  }, 1000);
}

// ブロックスコープ外で宣言された変数は、保持されているメモリ空間は変わらないので
// すべてのループが完了した値が出力される
let a;
for (let h = 0; h < 5; h++) {
  a = h * 2;

  setTimeout(function () {
    console.log("ブロックスコープが切り替わらない方: " + a);
  }, 1000);
}
```

## 配列とループ文

```js
const arry = [1, 2, 3, 4, 5];

for (i = 0; i < arry.length; i++) {
  console.log(arry[i]);
}

let v,
  j = 0;
// 6番目の要素を取ろうとすると空なのでundefinedが返ってくる
// undefinedはfalsyな値なので、ループ処理が抜ける挙動になる
// ここで気をつける点は、配列の中に0のようにfalsyの値が入っていたらすべての要素を展開する前にループ処理が中断される
while ((v = arry[j++])) {
  // 後方インクリメントなので、値を返した後に+1されている
  console.log("while文の方: " + v);
}
```

## for...in と列挙可能性

- ディスクリプターの設定値である、`enumerable`の値が`true`だったら**for...in の場合で列挙されるが、`false`であれば列挙されない**
- `for...in`の処理は、**列挙可能プロパティー**に対して順不同で反復処理を実行する
- 基本的に追加した順に処理されるが、**それが担保されているわけではない**ので順不同となる
- プロトタイプチェーンも列挙対象となる。自分自身に設定されているプロパティのみ列挙したい場合は`Object.hasOwnPropertyメソッド`を使用する
- **Symbol で定義したプロパティーは for...in の列挙対象にはならない**

```js
const s = Symbol();
const obj = {
  prop1: "value1",
  prop2: "value2",
  prop3: "value3",
  [s]: "value4", // 変数の値をオブジェクトのプロパティとして扱う方法
};

// Objectのプロトタイプに追加すると、この追加したものもfor...inの列挙対象として追加される
// 基本的にオブジェクト自身のプロパティのみ列挙したいことが多いので、enumerableをfalseにすることで列挙対象に上がらなくなる
Object.prototype.method = function () {};
// 追加したmethodを列挙対象にしない処理を行う
Object.defineProperty(Object.prototype, "method", {
  enumerable: false, // 追加したプロトタイプのディスクリプターの設定値が変わった
});

// // objのプロパティを列挙対象から外すやり方
// // こうするとfor...inの結果からprop1が消える
// Object.defineProperty(obj, 'prop1', {
// 	enumerable: false // 追加したプロトタイプのディスクリプターの設定値が変わった
// });

// ディスクリプターの設定値を確認してみる
const d = Object.getOwnPropertyDescriptor(Object.prototype, "method");
// シンボルの値を確認してみる
const e = Object.getOwnPropertyDescriptor(obj, s);
console.log(d, e);
/*
{writable: true, enumerable: false, configurable: true, value: ƒ}
configurable: true
enumerable: false // falseになっている！
value: ƒ ()
writable: true
__proto__: Object
*/

// 基本的にビルドインメソッドのenumerableはデフォルトでfalseになっているので、for...inで列挙対象にはならない

for (let key in obj) {
  // プロトタイプを列挙対象から外したい場合の書き方
  // keyに指定したものが自分自身のプロパティか確認して、そうであればtrue or falseを返す
  // Object.prototype.~ のように継承したプロトタイプなどは列挙対象から外れる
  // if(obj.hasOwnProperty(key)){
  // 	// keyにはプロパティの名前が入ってくる
  // 	// そのkeyを利用することでオブジェクトの値も取得できる
  // 	console.log(key, obj[key]);
  // }
  console.log(key, obj[key]);
}
```

## for...of と反復可能性

- `for...of`：イテレーターを持つオブジェクトの反復操作を行う
- イテレーターとは、**反復操作を行う際に利用するオブジェクトのこと**（反復可能オブジェクトとも呼ばれる）
- `String, Array, Map, Set, arguments`など。。。
- for...of では、あくまでオブジェクトに格納されている**イテレーターの挙動に影響される！**

```js
const arry = ["a", "b", "c"];
arry[4] = "e"; // 3番めが設定されていないので、3番めにはundefinedが格納される

Object.prototype.method = function () {};

// for...ofはenumerableは参照されていないので、値を変更しても列挙対象として出力される
Object.defineProperty(arry, 0, {
  enumerable: false,
});

const d = Object.getOwnPropertyDescriptor(arry, 0);
console.log(d);
/*
{value: "a", writable: true, enumerable: false, configurable: true}
configurable: true
enumerable: false // falseになっていても値が返ってきている！
value: "a"
writable: true
__proto__: Object
*/

// for...ofでは、あくまでオブジェクトに格納されているイテレーターの挙動に影響される！

// Array ▶ __proto__ ▶ Symbol(Symbol.iterator が存在しており、ここの値を書き換えることでfor...ofの挙動を変えることが出来る
for (let v of arry) {
  console.log(v); // vには配列の値が格納される
}
```

## Map と Set

- `Map`と`Set`はデータを管理するための入れ物
- このような入れ物を**コレクション**と呼ぶ
- 細かい動作ではパフォーマンスなども影響があるが、読み書きでは**Map の方が性能が良いとされている**

|          | Object | Map      |
| -------- | ------ | -------- |
| キー     | 文字列 | 制約なし |
| for...in | ●      | ×        |
| for...of | ×      | ●        |

|          | Array | Map |
| -------- | ----- | --- |
| 重複値   | ●     | ×   |
| for...in | ●     | ×   |
| for...of | ●     | ●   |

```js
const map = new Map(); // コンストラクター関数からMapオブジェクトを作成
const key1 = {};

// オブジェクトをキーにして値をセット
map.set(key1, "value1");
// セットした値を取得
console.log(map.get(key1)); // オブジェクトの参照を利用して値を保存している
console.log(map.get({})); // 新しいオブジェクトを生成したら値は取得できない

const key2 = function () {};
// 関数をキーにして値をセット
map.set(key2, "value2");
// セットしたキーを取得
console.log(map.get(key2));
// 値が存在しているか確認
console.log(map.has(key2));

let key3;
// プリミティブ型をキーにして値をセット
map.set((key3 = 0), "value3"); // このような書き方をされているコードもたまにある
console.log(map.get(key3));
console.log(map.get(0)); // プリミティブ型の場合は値そのものを変数が参照しているので、0としても値を取得できる

// セットした値を削除
map.delete(key3);
console.log(map.get(key3));

// 現時点でのMapオブジェクト
/*
Map(2) {{…} => "value1", ƒ => "value2"}
[[Entries]]
0: {Object => "value1"}
1: {function() {} => "value2"}
size: (...)
__proto__: Map
*/
// MapはEntriesというもので値を管理していて、オブジェクトと値の管理方法が違うということを頭の片隅においておく

console.log(" ");

// 配列でキーと値を取得
for (const m of map) {
  console.log(m);
}

console.log(" ");

// 分割代入でキーと値を取得
for (const [v, m] of map) {
  console.log(v, m);
}

// Mapではfor...inは出力されない
for (const k in map) {
  console.log(k);
}

// Setオブジェクトを作成
const s = new Set();
// 値をセット
s.add(key1);
s.add(key1); // 重複した値は出力されない
s.add(key2);
s.add(key3);
s.delete(key3); // 値を削除
console.log(" ");
console.log(s.has(key2)); // 値が存在しているか確認（Mapオブジェクトでも使える）
console.log(s.has(key3)); // 上で削除したのでfalseが返ってくる
console.log(" ");

// 配列のように添字を指定して特定の値を取得できないので、そうしたい場合は配列に戻して取得する
// const arry = Array.from(s);
const arry = [...s]; // スプレッド構文を使うことで、Array.from()と同様の挙動を実現できる
console.log(" ");
console.log(arry);
console.log(" ");

for (let k of s) {
  console.log("Setオブジェクト");
  console.log(key1);
}
```

## イテレーター

- 反復操作を行う際に使用するオブジェクトのこと（ある一定のルールが存在する）
- 今回はイテレーターを使ってオブジェクトを反復可能オブジェクトに書き換える

```js
// イテレーター書き方
function genIterator() {
  // 返却されているオブジェクト部分がイテレーター
  return {
    // イテレーターではnetメソッドを必ず保持する
    next: function () {
      // 戻り値としてオブジェクトを返す
      return {
        done: true / false, // ループの継続
        value: "値", // ループ時に渡ってくる値を格納するプロパティ
      };
    },
  };
}
```

```js
function genIterator(max = 10) {
  let i = 0; // ループの回数
  return {
    next: function () {
      if (i >= max) {
        return {
          done: true, // doneがtrueとしたら反復処理を終了する
        };
      }
      return {
        done: false,
        value: i++, // nextメソッドが呼ばれるたびに+1されていく
      };
    },
  };
}

// console.log(it.next());
// console.log(it.next()); // valueの値がカウントアップされている

const it = genIterator(100);
let a = it.next();
while (!a.done) {
  // console.log(a.value);
  a = it.next();
}

// Symbol()のイテレータに追加したら反復可能オブジェクトになる
// const obj = {
//     [Symbol.iterator] : genIterator // genIteratorメソッドに上限値を引数に設定（10回）
// }
const obj = {
  [Symbol.iterator]: genIterator.bind(null, 10), // bindを使って引数を渡す
};

for (const i of obj) {
  console.log(i);
}

// Setのインスタンス化を行う際に反復可能オブジェクトを初期値として代入するとインスタンスに対して初期値を設定できる
const s = new Set(obj);
console.log(s); // Setのオブジェクトに対して初期値が設定されている
```

## 反復可能オブジェクト（イテレーター）

- Object のプロトタイプの変更は**通常の開発では行われないので、レクチャーの一環で行うということを念頭においておく**

```js
const items = {
  prop1: "value1",
  prop2: "value2",
  prop3: "value3",
};

// 通常のオブジェクトだとfor...ofで反復操作は出来ないので、
// Object.entries()で配列に変換してfor...ofで反復処理を行う
// const items = Object.entries(obj);
// for (let item of items) {
// 	console.log(item);
// }

// 今回はオブジェクトのプロトタイプにイテレーターを追加して、
// Object.entries()を使用せずともfor...ofでオブジェクトを反復操作出来るようにする
Object.prototype[Symbol.iterator] = function () {
  console.log(this); // オブジェクトのプロパティと値をthisから取得していってvalueに代入している
  return {
    next() {
      return {
        value: 0,
        done: true,
      };
    },
  };
};

Object.prototype[Symbol.iterator] = function () {
  const keys = Object.keys(this);
  console.log(keys); // プロパティが配列になったものが出力される ["prop1", "prop2", "prop3"]
  return {
    next() {
      return {
        value: 0,
        done: true,
      };
    },
  };
};

// keysの値を1つずつ取得したい場合
Object.prototype[Symbol.iterator] = function () {
  const keys = Object.keys(this);
  let _this = this;
  let i = 0;
  return {
    next() {
      // nextメソッドが呼ばれたときに、key情報を+1していって値を取得したい
      let key = keys[i++]; // 渡ってきたiを返した後にインクリメントする（後方インクリメント）
      // i++;
      // console.log(_this); // メソッド内で宣言されていない変数なので、スコープチェーンをたどってレキシカルスコープに値を探しに行く（Objectの_thisを参照しに行く）
      return {
        value: [key, _this[key]],
        done: i > keys.length, // iがkeysの要素数より大きくなるとtrueになるのでループが終了する
      };
    },
  };
};

for (let item of items) {
  console.log(item);
}

for (let [k, v] of items) {
  console.log(k, v);
}
```

## ジェネレーター

- **イテレーター**を生成するための特殊な関数
- より簡略してイテレーターを作成することが出来る

```js
// ジェネレーターの書き方
function* gen() {
  if (ループ継続) {
    // yieldでイテレーターのオブジェクトを簡易的に表現している
    yield 値; // done:false, value: 値 という意味を示す
  } else {
    return 値; // ループを終了させたい場合はこちらのreturnを呼ぶ
  }
}
```

```js
function* gen() {
  yield 1;
  yield 2;
  return 3; // 一度returnが使用されるとイテレーターの終了（doneがtrueになる）を意味する
}

const it = gen(); // イテレーターを作成
// console.log(it.next());
// console.log(it.next());
// console.log(it.next()); // 3回目で（done:true, value:3）となる
// console.log(it.next()); // 4回目は処理は行われない（done:true, value: undefined）

function* genIterator(max = 10) {
  let i = 0;
  while (i < max) {
    yield i++;
  }
  return; // 省略しても動作に影響はない
}

const g = genIterator(10);
let a = g.next();
while (!a.done) {
  console.log(a.value);
  a = g.next();
}

const obj = {
  [Symbol.iterator]: genIterator,
};

for (let i of genIterator()) {
  console.log(i);
}
```

## 反復可能オブジェクト（ジェネレーター）

```js
const items = {
  prop1: "value1",
  prop2: "value2",
  prop3: "value3",
};

Object.prototype[Symbol.iterator] = function* () {
  for (let key in this) {
    // ここのthisがオブジェクトへの参照を保持している
    yield [key, this[key]]; // yieldでイテレーターのオブジェクトを簡易的に表現している
  }
};

for (let [k, v] of items) {
  console.log(k, v);
}
```

## イテレーターとスプレッド構文

- スプレッド演算子は、反復可能や列挙可能オブジェクトの展開を行う
- `let a = [...array]`：主に配列を展開するときに使用することが多い
- スプレッド演算子は**イテレーターの操作に従う**

### Rest Parameter（残余引数）

- 実引数に渡された関数を配列にまとめる
- `function(...args)`：渡ってきた値を**関数内で配列にまとめて使用することが出来る**
- 可変長の引数が渡ってくるときに使用することが多い

```js
const array1 = [1, 2, 3, 4, 5];
const array2 = [0, ...array1, 6]; // array1が展開されて新しい配列array2が生成される（別の配列）
array2.push(7); // 新しい値を追加してもarray1が影響を受けることはない

console.log(array1, array2);
console.log(array1 === array2); // 配列の格納先メモリ番地は違うので、等価性はfalseになる

// rest parameter
function sum(...args) {
  let ret = 0;
  for (let v of args) {
    // console.log(v);
    ret += v;
  }
  return ret;
}

const result = sum(1, 2, 3, 4, 5, 6);
console.log(result);

// イテレーターとの関係
const obj = {
  prop1: "value1",
  prop2: "value2",
  prop3: "value3",
};

// イテレーターを使用して反復処理ができるように変更
// これをしていないと、反復可能オブジェクトではないという言うエラーが出る
Object.prototype[Symbol.iterator] = function* () {
  for (let key in this) {
    yield [key, this[key]];
  }
};

const array3 = [...obj]; // イテレーターを使用していないと、ncaught TypeError: obj is not iterable

// オブジェクトリテラルで囲んだ場合は、イテレーターの挙動に従うのではなく別の実装によって動いている
const array4 = { ...obj };

console.log(array3);
console.log(array4); // ただ単にオブジェクトが展開されて、新しいオブジェクトにプロパティと値が格納されている
```

# 非同期処理

## ブラウザと JavaScript

- スレッド：連続して実行される**一本の処理の流れ**
- JavaScript では**メインスレッド、サービスワーカー、ウェブワーカー**などがある。重要なのは**メインスレッド**である
- メインスレッドは JavaScript の実行とレンダリング（画面描写処理）を行う
- 画面の描画を司るレンダリングでは、**コンポジタースレッド、ラスタースレッド**等がある

## FPS

- １秒間当たりの画面（フレーム）更新頻度のこと
- 60fps：1 秒間に 60 回画面更新する（16.7m 秒に 1 回画面を更新する）
- 処理が重いと画面が更新されなくなる

## 同期処理と非同期処理

- 同期処理とは、**メインスレッドでコードが順番に実行される**こと
- 1 つの処理が完了するまで次の処理には**絶対に進まない**
- **非同期処理は一時的にメインスレッドから処理が切り離される**
- その結果、一時的にメインスレッドに空きの状態ができ、後続の処理やクリックイベントを受け取ることが出来る

### 代表的な非同期処理

| 非同期 API     | UI イベント | その他       |
| -------------- | ----------- | ------------ |
| setTimeout     | クリック    | NW イベント  |
| Promise        |             | I/O イベント |
| queueMicrotask |             |              |

## タスクとコールスタック

- タスクキュートは、**実行待ち**（タスクキュー）の非同期処理の行列のこと
- 非同期処理の実行順を管理しているキューのこと
- キューの仕組みのことを**先入れ先出し**という（FIFO：First In, First Out）
- **タスクキューに入った順番に非同期処理が実行される**
- メインスレッドが占有されている状態 ＝ コールスタックに**コンテキストが積まれている状態**
- コールスタックにコンテキストが積まれている状態では、**タスクキューに入っているタスクは待ちの状態となり、コールスタックのコンテキストが消えるまで実行されることはない**

```js
// ①スクリプトの実行に伴いグローバルコンテキストが生成される
const btn = document.querySelector("button");
// ②グローバルコンテキスト内でクリックイベントが登録される（関数はtask2が登録される）
btn.addEventListener("click", function task2() {
  console.log("task2 done");
});

function a() {
  // ④setTimoutによってtask1()が非同期で実行される（4秒間待機）この間はメインスレッドから切り離されているのでクリックなどは行える
  setTimeout(function task1() {
    // ⑦4秒後に関数task1がタスクキューに登録される
    console.log("task1 done");
  }, 4000);

  // ⑤関数aの処理として、whileによって5秒間画面を占有しているので待機状態となる
  // ⑥待機している間にボタンがクリックされると関数task2が実行されタスクキューに登録される
  const startTime = new Date();
  while (new Date() - startTime < 5000);

  // ⑧関数aの実行が終了するとコールスタックから関数aが消える
  console.log("fn a done");
}

// ③グローバルコンテキスト内で関数aが実行されたので、コールスタックに関数aが積まれる
a();

// ⑨関数aの実行が終了し、グローバルコンテキストも終了し消える

// ⑩その後イベントループがコールスタックが空いたことを通知して、タスクキューから関数task2を登録して実行する
// ⑪関数task2が実行されコールスタックから消えたあと、同様にイベントループが通知してタスクキューから関数task1を登録し実行する
// ⑫関数task1の実行が終了したらコールスタックは空の状態となる
```

## コールバック関数と非同期処理

```js
function a(b) {
  // ①コールスタックに関数task1が積まれる
  setTimeout(function task1() {
    console.log("task1 done");
    // ②関数task1の後に関数bがコールスタックに積まれて、後からコールスタックに積まれた関数bから順番に実行されていく
    b(); // 関数bが実行されているレキシカルスコープに関数bの宣言があるので引数に指定しなくても良い
  });

  console.log("fn a done");
}

function b() {
  console.log("fn b done");
}

a(b);

// 関数task1の後に実行したい
// コールスタックから関数bが実行されない限り、タスクキューに積まれた関数task1が実行されない
// b();
```

## 非同期処理のチェーン

- ES6 から非同期のチェーンを繋げる**Promise**が追加された

```js
function sleep(callback, val) {
  setTimeout(function () {
    console.log(val++); // 先に実行される
    callback(val); // 後に実行される
  }, 1000);
}

// コールバック関数を渡して、特定の処理の後に実行する
// 非同期の処理をチェーンしていくコールバック地獄の完成！
sleep(function (val) {
  sleep(function (val) {
    // ここのコールバック関数の実引数は+1されている
    sleep(function (val) {
      // ここのコールバック関数の実引数は+2されている
      sleep(function (val) {
        // ここのコールバック関数の実引数は+3されている
      }, val);
    }, val);
  }, val);
}, 0);
```

## Promise

- Promise とは、非同期処理を**より簡単に、可読性が上がるように書けるようにしたもの**（ES6）
- コールバック関数でも実装できるが、コードの階層が深くなり可読性が悪くなるのが欠点
- `reject`：Promise のコールバック関数が実行されたときに、何らかのエラーが発生したときにそれを Promise に通知するための関数
  - `reject`が呼ばれたら`catchメソッド`が実行される
  - `reject(引数)`で渡した実引数を`catchメソッド`で受け取ることが出来る
  - `catchメソッド`内のコールバック関数が実行された後に、`finallyメソッド`内のコールバック関数が実行される

```js
// Promise構文
new Promis(function (resolve, reject) {
  // resolveを実行するとthen()メソッドが実行される
  resolve("hello");

  // rejectを実行するとcatch()メソッドが実行される
  reject("reject!!");
})
  .then(function (data) {
    // resolveを待つ
    // resolveで渡した実引数が渡ってくる
    console.log(data); // hello
  })
  .catch(function (data) {
    // rejectを待つ
    console.log(data); // reject!!
  })
  .finally(function () {
    // resolveとrejectの共通の終了の処理
    // ここでは引数を使用することが出来ない
  });
```

### Promise の使用方法

```js
new Promise(function (resolve, reject) {
  console.log("Promise!");
  // resolve('hello');
  // reject('bye');
  setTimeout(function () {
    resolve(); // 1秒後に実行される
    // reject();
  }, 1000);
})
  .then(function (data) {
    // 非同期で実行されるのでコールスタックが空になった後に実行される
    // global end!の出力の後に実行される
    console.log("then!: " + data);

    // rejectメソッドはPromiseの中でしか使えないので、特定のthenの中でcatchメソッドの
    // 処理に移行したい場合は、throwを使用する
    // throw new Error();
    return data; // 2つ目のthenメソッドに引数を渡したい場合はreturnで値を返す
  })
  // thenメソッドをつなげたとしても階層が深くなることはない！！
  .then(function (data) {
    // 非同期で実行されるのでコールスタックが空になった後に実行される
    // global end!の出力の後に実行される
    console.log("then!2回目: " + data);
  })
  .then(function () {
    // 非同期で実行されるのでコールスタックが空になった後に実行される
    // global end!の出力の後に実行される
    console.log("then!");
  })
  .catch(function (data) {
    console.log("catch!: " + data);
  })
  .finally(function (data) {
    // finallyにはreturnで値を返しても引数に値を渡すことは出来ない！
    console.log("fainally!!: " + data);
  });

// グローバルコンテキストの終了がわかるようにする
console.log("global end!");
```

## Promise チェーン

- Promise チェーンとは、**Promise を使って非同期処理を順次実行**すること

```js
// function sleep(callback, val) {
function sleep(val) {
  // sleepの呼び出し元にPromiseを返却する
  // こうすることで呼び出し元でthenメソッドが使用できる
  return new Promise(function (resolve) {
    setTimeout(function () {
      console.log(val++);
      // callback(val);
      resolve(val);
    }, 1000);
  });
}

console.log(sleep(0)); // 関数の呼び出し元にPromiseが返却されている

sleep(0).then(function (val) {
  console.log("then!1回目: " + val); // 引数も渡ってきている
  return sleep(val)
    .then(function (val) {
      // callback関数のreturnにnew Promiseがセットされているということ
      // 非同期の処理をつなげるためには、thenメソッドの中のコールバック関数のreturnにPromiseのインスタンスをセットしないと
      // Promiseのチェーンが切れてしまい、後続の処理がsleepの非同期の処理を待たずに実行されてしまう
      console.log("then!2回目: " + val);
      return sleep(val);
    })
    .then(function (val) {
      console.log("then!3回目: " + val);
      // 必ずPromiseのインスタンスを返す
      // そうしないと、Promiseのチェーンが切れて意図した挙動ではなくなる
      return sleep(val);
    })
    .then(function (val) {
      console.log("then!3回目: " + val);
    })
    .then(function (val) {
      console.log("then!3回目: " + val);
    });
});
```

## Promise と並列処理

- `Promise.allメソッド`を使用する
- `Promise.allSetlledメソッド`は少し挙動が違う

```js
function sleep(val) {
  return new Promise(function (resolve) {
    setTimeout(function () {
      console.log(val++);
      resolve(val);
    }, val * 500);
  });
}

// Promiseの並列処理
// Promise.all()の引数に渡した反復可能オブジェクト（配列）の中に渡したPromiseのインスタンスが
// すべて完了するまでthenメソッドの実行を待機する
// reject()が呼ばれるとcatchの方に処理が移る
Promise.all([sleep(2), sleep(3), sleep(4)]) // 配列などの反復可能オブジェクトを渡す
  .then(function (data) {
    console.log("4が呼ばれたタイミングで実行されます end!");
    // ここで渡ってくる引数は、sleep関数内のresolveメソッドで渡した引数の値（配列として出力される）
    console.log(data);
  });

// Promiseチェーンの中でPromise.all()を使う方法
sleep(0)
  .then(function (val) {
    // Promise.all()もPromiseインスタンスを返すのでこの書き方ができる
    return Promise.all([sleep(2), sleep(3), sleep(4)]);
    // 後続のthenメソッドは、このPromise.all()が終了されるまで待機状態となる
  })
  .then(function (val) {
    console.log("Promise.all()の後に実行される: " + val);
    return sleep(val);
  })
  .then(function (val) {
    return sleep(val);
  });

// raceメソッドを使ったやり方
// 配列で渡されたもののどれか一つが完了した時点でthenメソッドを実行する
Promise.race([sleep(2), sleep(3), sleep(4)]).then(function (data) {
  // sleep(2)が完了した時点でresolveの引数がこちらに渡ってくる（sleep内で後方インクリメントされているの）
  console.log("raceメソッドを実行しています: " + data);
});

// allSettled
// すべての非同期処理が完了するまで待機するが、その処理が成功したか失敗したかまではわからない
// reject()が呼ばれてもcatchに処理が移行しない
Promise.allSettled([sleep(2), sleep(3), sleep(4)])
  .then(function (data) {
    // sleep(2)が完了した時点でresolveの引数がこちらに渡ってくる（sleep内で後方インクリメントされているの）
    console.log("allSettledメソッドを実行しています");
    console.log(data);
    /*
  {
    status: "fulfilled" resolveが渡ってきた際のPromiseのステータス
    value: 3
    __proto__: Object
  }
  */
  })
  .catch(function (e) {
    console.error(e);
  });
```

## Macrotasks と Microtasks（その 1）

- `Macrotasks`と`Microtasks`はそれぞれ非同期処理の**待ち行列を管理しているキュー**であるが挙動が異なる
- `Macrotasks`とは、これまでのレクチャーで**タスクキュー**と呼んでいたもの

  - これまで出てきた`setTimeout`はマクロタスクにタスクを登録している
  - イベントループの順番が回ってききても、1 つずつしか格納されているタスクを実行しない

- `Microtasks`とは、タスクキューとは別で存在する**非同期処理の待ち行列**のこと

  - 別名**ジョブキュー**と呼ばで、**Promsie で登録された非同期処理**などが含まれる
  - イベントループで順番が回ってきたら、**すべてのジョブを実行する**
  - `Macrotasks`でジョブが実行されているときに、`Microtasks`にジョブが追加されたらそちらが優先的に実行されて、タスクが空になれば`Macrotasks`が再度処理される
  - `Microtasks`は溜まっているジョブをすべて実行する、`Macrotasks`は 1 つずつジョブが実行される

### イベントループ

- イベントループでは、`Macrotasks`と`Microtasks`がそれぞれ別々に管理されている
- `Macrotasks`と`Microtasks`が同じイベントでループいている場合は、**必ず`Microtasks`の方から実行される**

### Macrotasks で代表的な関数

- `setTimeout`
- `setInterval`
- `requestAnimationFrame`

### Microtasks で代表的な関数

- `Promise`
- `queueMicrotask`
- `MutationObserver`

```js
// Macrotasksに格納される関数
setTimeout(function task1() {
  // ④非同期処理のタスクなので、グローバルコンテキストが完了した後に実行される
  console.log("task1");
}); // 引数を指定していないので、非同期関数として実行されるが待機時間は無い

// Microtasks
new Promise(function promise(resolve) {
  // ①こちらは同期的処理される
  console.log("promise");
  resolve();
}).then(function job1() {
  // ③ここは非同期処理なのでグローバルコンテキスト完了後に実行される
  // PromiseはMicrotaskなので、MacrotaskであるsetTimeou()より先に出力される
  console.log("job1");
});

// ②非同期処理より前に実行される
console.log("global end");
```

## Macrotasks と Microtasks（その２）

```js
// Microtasks と Macrotasks が絡み合ったコード

// ①スクリプトが実行されるとグローバルコンテキストから new Promise() の中のコールバック関数であるpromiseが実行される
// Microtasks
new Promise(function promise(resolve) {
  // ここは同期的に実行されるので一番最初
  console.log("promise");

  // Macrotasks（非同期処理）
  setTimeout(function task1() {
    // ④ コールバック関数であるtask1()が Macrotasks に格納される
    console.log("task1");
    // ②setTimeoutの中でresolve()が呼ばれている
    // ⑤グローバルコンテキストが空になると、task1() ▶ resolve() の順番でコールスタックに登録される
    resolve(); // ⑥このresolve()が実行されたときにthenメソッドが実行されるので、job1メソッドが Microtasks に格納される
  });

  // ⑦そしてメソッドがコールスタックから消滅するとjob1メソッドがコールスタックに登録される

  // 非同期処理
  // setTimeout内にあるresolve()の実行を待つthenメソッドは一番最後に実行される
  // ③resolveを待つ待機状態のthenメソッド
})
  .then(function job1() {
    console.log("job1");
    setTimeout(function task1() {
      console.log("setTimeout task2");
      // resolve();

      // グローバルコンテキストが実行された後に実行したい非同期処理
      // 実質的にMicrotasksであるquueMicrotaskと同じ挙動になる
      const p = Promise.resolve();
      p.then(function job4() {
        console.log("job4");
      });
      // setTimeoutの実行を待ってこの関数が実行される
      queueMicrotask(function job4() {
        console.log("job4");
      });
    });

    // thenメソッド内でjob1を実行中に新しくMicrotasksに対してjob4が追加される
    // job2, job3 より先に実行される
    queueMicrotask(function job4() {
      console.log("job4");
    });
  })
  .then(function job2() {
    console.log("job2");
  })
  .then(function job3() {
    console.log("job3");
  });

console.log("global end");
```

## Await と Async

### Await/Async ってなに？

- `Promis`をより直感的に記述できるようにしたもの
- **ES8**から使用できるようになっている
- Async を使って宣言された関数は、**必ず Promis が返却される関数である**ということ
- Await は**Promis を返却する関数の非同期処理が完了するまで待って次の処理へ移行するように制御を加えることが出来る**
- Await を使用する際はは**呼び出し元の関数に必ず`async`を付けて、関数コンテキストの中で実行**する必要がある（グローバルコンテキスト内では使用できない）

```js
function sleep(val) {
  return new Promise(function (resolve) {
    setTimeout(function () {
      console.log(val++);
      resolve(val);
    }, 1000);
  });
}

//
async function init() {
  // awaitをつけない場合は new Promise のインスタンスが返却される
  // awaitをつけるとその関数は必ず非同期で実行されることになるので、呼び出し元の関数にasyncを付けて、
  // 非同期の関数であることを伝えてやる必要がある（asyncを付けないとエラーになる）：Uncaught SyntaxError: await is only valid in async function
  let val = await sleep(0);
  val = await sleep(val);
  val = await sleep(val);
  val = await sleep(val);
  val = await sleep(val);

  // resolveが呼ばれるまで待機状態となり、下のconsole.log()は待機状態が終わるまで実行されない
  // valに格納されているのが、thenメソッドのコールバック関数の引数
  // awaitを使うとresolveで渡された引数が変数に代入される
  console.log(val); // 1

  // throw Errorを呼ぶとcatchの処理に入る
  // throw new Error();

  // 値を返すとthenメソッドを使用したときに引数で値を渡せる
  return val;
}

init()
  .then(function (val) {
    console.log("then! と 戻り値: " + val);
  })
  .catch(function (e) {
    console.log("catchの処理: " + e);
  });

// console.log(init()); // asyncを付けた関数は return を付けなくても Promise のインスタンスを返す

// sleep(0).then(function(val) {
//   return sleep(val);
// }).then(function(val) {
//   return sleep(val);
// }).then(function(val) {
//   return sleep(val);
// }).then(function(val) {
//   return sleep(val);
// }).then(function(val) {
//   return sleep(val);
// })
```

## fetch

- `fetch関数`を使うとサーバから情報を取得することが出来る
- `fetch関数`は**Promise オブジェクトを返す**
- `response`も戻り値として Promise が返ってきている
- ブラウザで用意されている WebAPI でも Promise が返ってくる関数も存在しているので注意

```js
// window.fetch('ファイルパス', )
fetch("users.json")
  .then(function (response) {
    // console.log(response); // Promiseが返ってきている
    return response.json();
  })
  .then(function (json) {
    // 1回目のthenメソッドの処理が終了するまで待機して、次のthenメソッドを実行するｓ
    console.log(json); // jsonファイルの中身

    for (const user of json) {
      // console.log(`I am ${user.name}. ${user.age} years old`)
    }
  });

// async / await を使ったやり方
async function fetchUsers() {
  const result = await fetch("users.json").then(function (response) {
    return response.json();
  });

  for (const users of result) {
    console.log(`I am ${users.name}. ${users.age} years old`);
  }

fetchUsers();
```

## 例外処理とエラー

- 例外処理とは、エラーが発生した際に飛ぶ特別な特別な処理

```js
// 使用例
try {
  throw new Error();
} catch (e) {
  // エラーハンドリング
} finally {
  // 終了処理
}
```

```js
async function fetchUsers() {
  const response = await fetch("users.json");
  if (response.ok) {
    const json = await response.json();
    if (!json.length) {
      // メンテナンスのしやすさなど保守性の観点から、エラーの通知のみ行い、
      // エラーハンドリングに関しては実行先の関数で適時実装していく形が多い
      throw new NoDataError("サーバ空の取得データが空です");
    }
    return json;
  }
}

// カスタムエラー
// 例外処理の中で何らかの条件分岐を行いたいときに使用することがある
class NoDataError extends Error {
  constructor(message) {
    super(message);
    this.name = "NoDataError";
  }
}

// ユーザーデータを取ってくる
async function init() {
  // fetchUsers()とそのエラーハンドリングを切り離して実装する事ができる
  try {
    const users = await fetchUsers();
    console.log(users);
    for (const user of users) {
      console.log(`I'm ${user.name}, ${user.age} years old`);
    }
  } catch (e) {
    // NoDataErrorオブジェクトだったか否かで条件分岐を行う
    if (e instanceof NoDataError) {
      console.log(e);
    } else {
      console.log("Oops, something went wrong");
    }
  } finally {
    console.log("bye");
  }
  // throwが投げられた場合は try catch でエラーハンドリングを行わないと処理が停止するので、ここのメッセージは出力されない
  console.log("end");
}

init();
```

# モジュラー JavaScript

## ES Modules と CommonJS

- モジュールとは、ソースコードを機能毎に分割して、メンテナンスしやすくする仕組み（ES6 から導入された）
- 代表的なものに**ESM**と**CJS**が存在する

### CommonJS（CJS）

- Node.js 上でモジュールを管理する仕組みのこと
- `requier`や`exports`のキーワードを使用してモジュールの読み込み・出力を行う

### ES Module（EMS）

- ECMAScript によるモジュールの管理方法
- `import`や`export`のキーワードを使用してモジュールの読み込みや・出力を行う
- この仕組は主にブラウザ側で使用する仕組み

## 注意点

- CJS と EMS に**互換性は無い**
- CJS でエクスポートされたものは、EMS のキーワードでインポートは**出来ない**
- その逆も同様

## 対応表

|                  | ESM             | CJS               |
| ---------------- | --------------- | ----------------- |
| キーワード       | import / export | require / exports |
| 実行環境         | Browser         | Node.js           |
| ファイルの拡張子 | .mjs            | .cjs              |

- 現行の Node.js のバージョン（14 系）でも ESM は動かせるが、試験的な機能となっている（安定的な稼働では無い）

## import と export

- `import`：モジュールの読み込みに使用
- `export`：モジュールの露出に使用

```html
<!-- ESモジュールを読み込む場合は type を module とする必要がある-->
<script type="module" src="moduleB.js"></script>
```

```js
// moduleA.js

export let publicVal = 0;

export function publicFn() {
  console.log("publicFn called: ");
}

// このモジュールでデフォルトでエクスポートされるものが設定出来る
// インポートするでも読み込み方が違うので注意
export default 4;
```

```js
// moduleB.js

// 外部ファイルをインポートする
// export されているファイルは 中括弧{} で囲む
// import { publicVal, publicFn } from './moduleA.js'; // webpack を使用しない場合はファイルの拡張子まで記述する必要がある
// console.log(publicVal);
// publicFn();

// 名前を帰る場合は as ~ で指定する
// export default で指定したものを受け取る方法
// import defaultVal, { publicVal as p, publicFn as pFn } from './moduleA.js';
// console.log(p);
// console.log('defaultVal: ' + defaultVal);
// pFn();

// インポートしたものをオブジェクトとして受け取る方法
import * as moduleA from "./moduleA.js";
console.log(moduleA.default); // export default の値を受け取る方法
console.log(moduleA.publicVal);
moduleA.publicFn();
```

## ESModule と即時関数

- ESModule は即時関数の動きと似ている

```js
// main.js
const moduleA = (function () {
  console.log("IIFE called");

  // privateValなどの変数はオブジェクトの中に含まれている変数ではないため、外部からはアクセス出来ない
  let privateVal = 1;
  let publicVal = 10;

  function publicFn() {
    console.log("publicFn called: " + publicVal);
  }

  // 同様にplivateFnも即時関数の中の関数スコープからしか呼び出せない
  function privateFn() {}

  // ここのオブジェクトがModuleAの変数に格納される戻り値
  return {
    // ここで返却している値や関数は外部からもアクセスできる
    publicFn,
    publicVal,
  };
})();

// moduleBに格納して呼び出す方法
// 即時関数に変数定義をしなくてもレキシカルスコープ上にmoduleAがあるので、参照しに言って問題なく動作するが
// 一般的に即時関数で外部から変数を渡す場合は、実引数と仮引数に渡す変数を定義して内部でその変数を参照する
const moduleB = (function (moduleA) {
  console.log("moduleB :");
  console.log(moduleA.publicVal++); // 外部からアクセス可能
  console.log(moduleA.publicVal); // 値が+1されている

  // 最後に返却したオブジェクトがmoduleAに格納された時点で、publicValの値はプリミティブ型の値になるので値自体がコピーされている
  // そのため即時関数内で定義した変数と、オブジェクトで返却した変数の値は値がコピーされたことになり、メモリの参照先が変わっているので
  // 値が変更さても影響はない
  console.log(moduleA.publicFn()); // publicValの値は変わっていない
})(moduleA);

// 分割代入を使用した方法
const moduleC = (function ({ publicFn, publicVal }) {
  console.log("moduleC :");
  console.log(publicVal++);
  console.log(publicFn());
})(moduleA);

// 分割代入の他のパターンの構文
const moduleD = (function ({ publicFn: fn, publicVal: val }) {
  console.log("moduleD :");
  console.log(val++);
  console.log(fn());
})(moduleA);
```

```js
// moduleA
console.log("ESmodule called");

// モジュールの外部からプリミティブ型の値を操作する場合は関数の中で行うと、内部の値を参照することが出来る
let privateVal = 1;
// export let publicVal = 10;
export let publicVal = { prop: 10 };

export function publicFn() {
  // console.log('publicFn called: ' + publicVal);
  console.log("publicFn called: " + publicVal.prop);
  // 関数内でエクスポートされていない内部の値を変更する
  privateVal++;
  console.log("privateValを変更: " + privateVal);
}

function privateFn() {}
```

```js
// moduleB
import { publicVal as val, publicFn as fn } from "./moduleA.js";

fn();
fn();
// モジュールで読み込んだ変数の値を変更しようとすると「Assignment to constant variable.」というエラーが発生する
// これは即時関数で値を設定したときと同様の動きで、読み込んだ変数がそのまま使用されているわけではなく、プリミティブ型の値がコピーされている
// そのためモジュールで読み込んだ値を変更しようとするとデータの不意号が発生するため、ESModuleではモジュールで読み込んだ値の変更は許可されていない
// ただし、データをオブジェクト型に変更するとこのようなエラーは発生しない
// console.log(val++);
// console.log(val++);
console.log("prop: " + val.prop++);
console.log("prop: " + val.prop++);
// 読み込み元のmoduleAの変数の値も変更されて同期が取れている
// オブジェクトをコピーした際は、オブジェクトの参照がコピーされているため
fn();
```

## モジュールコンテキストとモジュールスコープ

### モジュールコンテキスト

- これまで学んできたコンテキストは**グローバルコンテキストと関数コンテキスト**がある
- ESModule の場合はグローバルコンテキストの部分が**モジュールコンテキスト**に変わる
- 基本的に同じコンテキストであるが、一点だけ違うのが**this が使用できない！**
- ただし`windowオブジェクト`はモジュールコンテキストでも使用できる

### モジュールスコープ

- スクリプトスコープの部分が**モジュールスコープ**に変わる
  - グローバルスコープ
  - モジュールスコープ
  - 関数スコープ
  - ブロックスコープ
- スクリプトスコープとは、`const`や`let`などを使ってスクリプトファイルの直下に記述したスコープのこと

```js
// moduleA.js
// exportを付けないとmoduleBからは参照できない
const a = 0;

// windowに格納した値をmoduleBで参照する
window.d = 1;
```

```js
// moduleB.js
// moduleAを実行したいだけの場合は from は省略できる
import "./moduleA.js";

// モジュールコンテキストの説明
// このモジュールのトップレベル（直下に記述されている）のことをモジュールコンテキストと呼ぶ
// console.log(this); // undefined

function fn() {
  console.log(this); // undefined
}
fn();

const obj = {
  fn,
};

// オブジェクト内に格納するとオブジェクトから呼び出されたものと認知され、thisは呼び出し元のオブジェクトを参照する
obj.fn();

// windowオブジェクトは参照できる
console.log(window);

// モジュールスコープの説明
// モジュールスコープはこのファイル内で生成されるスコープなので、基本的にこのファイル内でのみ参照できるスコープである
// このファイルの外から変数や関数を参照したい場合は、これまでと同様にエクスポートやインポートを行い外部から参照できるようにする
console.log("moduleAで格納したwinsowOBJの値: " + window.d);

// ただしwindow越しに変数や関数を使用するのはモジュールの考え方かた外れるので、import / export で変数や関数を読み込み実行するようにすること
```

## モジュールの特徴

- `type="module"`という属性を付けた場合は読み込まれるファイルは**非同期で読み込まれる**

```html
<!-- IEなどモジュールに対応していないブラウザでの対応 -->
<!-- moduleに対応していないブラウザだとアラートが表示される -->
<script nomodule>
  alert("nomodule");
</script>

<!-- defer属性を使用するとhtmlタグの順番を入れ替えてもエラーが出ない -->
<!-- htmlの構文解析が終わった時点で読み込まれるようになる -->
<!-- defermでは何回でも読み込まれる -->
<script src="main.js" defer></script>
<script src="main.js" defer></script>
<!-- type="module"と記述した時点でdefer自動的に付与されて同様の動きとなる -->
<!-- moduleの場合は一回しか読み込まれない -->
<script type="module" src="moduleB.js"></script>
<script type="module" src="moduleB.js"></script>
```

```js
// main.js
// documentオブジェクトの中にhtmlタグがオブジェクト形式に格納されているので
// このオブジェクトにアクセスすることでテキストなどを取得できる
// const h1 = document.querySelector('h1');
// const text = h1.textContent;
// console.log(text);

console.log("main.jsは何回でも読み込まれます");
```

```js
// moduleA.js
console.log("moduleA: モジュールで読み込んだ場合も1回しか読み込まれません");
```

```js
// mosuleB.js
// import で読み込んだモジュールも一回しか読み込まれない
import "./moduleA.js";
import "./moduleA.js";

const h1 = document.querySelector("h1");
const text = h1.textContent;
console.log("moduleBから: " + text);

console.log("moduleb: moduleは一回しか読み込まれません");
```

## Strict モード

- Strict モードとは、通常の JavaScript で許容されている**一部の書き方を制限する**

### 目的

- **意図しないバグの混入防止**
- 予約後の確保
- コードのセキュア化などが挙げられる

### Strict モードの有効化

- ファイルの先頭、もしくは関数内の先頭行に`"use strict"`と記述する
- 従来の JavaScript の記述ではエラーにならなかったものがエラーとして認識されるようになる
- モジュールでは**常に Strict モードが有効化されていることに注意！**
- `this`を戻り値とした場合に window オブジェクトが返却される状況は、**無駄な脆弱性を招きかねないという判断のもと undefined が返却される設計となっている**
- なるべく無駄なオブジェクトを露出しないことで、コードをセキュアに保つという方向に JavaScript の機能を抑制している

```js
// main.js
"use strict";

// strictモードでない場合はwindowオブジェクトに値を格納する意味なる
// strictモードではエラーになる
// a = 0;

// 関数の中で変数を設定した場合でも同様
function fn() {
  // 'use strict' // 関数の中でもモードの有効化をする
  // a = 0;

  // Strictモードでは予約後を変数で使用することは出来ない
  // const implements, interface, package

  return this;
}
// console.log(a);

// thisを戻り値とした場合にwindowオブジェクトが返却される状況は、
// 無駄な脆弱性を招きかねないという判断のもとundefinedが返却される設計となっている
// console.log(fn());

// Strictモード出ない場合のthis
// オブジェクトでラップされた値が帰って来ている
console.log(fn.call(2)); // Number {2}

// Strictモードを有効化した場合
// thisの戻り値でプリミティブ型の値を取得できる
console.log(fn.call(2)); // 2
```

```js
// module
// モジュールでは常にStrictモジュールが有効化されている
function fn() {
  a = 0;
}
fn();
console.log(a);
```

## Strict モードとクラス

- クラスやコンストラクターの内部では自動的に**Strict モードが ON になっている**

```js
// StrictモードOff
function fn() {
  console.log(this); // StrictモードがOffなのでwindowオブジェクトが返ってくる
}
fn();

class C {
  constructor() {
    function fn() {
      console.log(this); // undefined
    }
    fn();
  }

  method() {
    function fn() {
      console.log(this); // undefined
    }
    fn();
  }
}

const c = new C();
c.method();
```

## ダイナミックインポート

- 比較的新しい技術なので**ブラウザが対応しているか**調べておく
- 画面の初期表示時に必要のないファイルもあるため、**必要になったときに非同期で呼び出す事ができる**
- そうすることでなるべくユーザーを待たせないで処理を実行することが出来る
- その他のブラウザ対応は`webpack`や`babel`を利用することで対応できる
- `import()`の戻り値は**promise**なので**async、await**で処理をつなぐことが出来る

```js
// moduleA.js
export let publicVal = 0;

export function publicFn() {
  console.log("publicFn called");
}

export default 1;
```

```js
// moduleB.js

// 通常の読み込み方（同期的）
// import { publicVal, publicFn } from './moduleA.js';
// publicFn();

// ダイナミックインポート

async function fn() {
  const modules = await import("./moduleA.js");
  modules.publicFn();
}
fn();

// 非同期で読み込みが行われる
// import()の戻り値はPromiseが返ってくるのでthenで繋いで処理を記述する
import("./moduleA.js").then(function (modules) {
  console.log(modules); // publicValなどの値やメソッドがオブジェクトで返ってくる
  modules.publicFn();
});
```

# 様々なオブジェクト

## Proxy

- ES6 から導入されたオブジェクトで、**プロパティーの操作に独自の処理を追加する**ためのオブジェクト
- プロパティの**変更を検知して独自の操作を行うことが出来る**

```js
const targetObj = { a: 0 };

// handlerを持ったオブジェクトを作る
const handler = {
  // プロパティの変更があった際に動くメソッドなので、トラップとも呼ばれる（その他,、get や delete）
  // 第一引数：Proxyの第一引数で渡したオブジェクトが渡ってくる
  // 第二引数：プロパティにアクセスした際のプロパティの名前が渡ってくる
  // 第三引数：set場合は新しい値が渡ってくる
  // 第四引数：receiverでは new Proxy で作成したProxyオブジェクトが渡ってくる
  // 値をセットした際に検知される
  set: function (target, prop, value, receiver) {
    console.log(`[set]: ${prop}`);
    // target[prop] = value;
    // Errorを発生させるようにすれば値をセットできない
    throw new Error("値をセットできません");
  },
  // 値を取得した際に検知される
  get: function (target, prop, receiver) {
    console.log(`[get]: ${prop}`);
    console.log(receiver);
    // 値が見つかった時の処理
    if (target.hasOwnProperty(prop)) {
      return target[prop];
    } else {
      // プロパティが見つからなかった場合のデフォルト値
      return -1;
    }
    // return target[prop];
    // Errorを発生させるようにすれば値を取得できない
  },
  // 値が削除された際に検知される
  deleteProperty: function (target, prop) {
    console.log(`[delete]: ${prop}`);
    delete target[prop];
    // Errorを発生させるようにすれば値を削除できない
  },
};

// 第一引数：ターゲットのオブジェクト
// 第二引数：実行されるメソッド（handler）が格納されているオブジェクトを指定
const pxy = new Proxy(targetObj, handler);
// target[prop] = value と値を指定しないと値は0のままでセットされていない
// pxy.a = 1;

// getメソッドの中の処理が実行される
pxy.a;

// プロパティが見つからなかった場合に設定したデフォルト値が返される
// console.log(pxy.b);

// deletePropertyメソッドの処理が実行される（targetObjを見てみるとプロパティが削除されている）
delete pxy.a;
```

## Reflect

- ES6 から導入されたもので、**JavaScript エンジン内部の汎用的な関数**を呼び出すメソッドが格納されているオブジェクト

### Reflect を使用する目的

- 内部のメソッドを呼び出す関数の格納場所
- Proxy と合わせて使用するため
- 内部のメソッドに直接アクセスできる手段が Reflect によって実現されている
- また、Reflect によって関数表記で内部のメソッドにアクセスできるようになった

```js
class C {
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }
}

const obj1 = new C(1, 2);
console.log(obj1);
// new演算子は内部的にReflect.construct()メソッドと同様の機能を内部メソッドとして呼んでいる
// なのでnew演算子を使用しなくてもReflect.construct()メソッドを使用してインスタンス化を行うことが出来る
// 演算子を使用していたところを関数として定義できるのがReflectの特徴
// 第一引数にコンストラクタ関数を定義
// 第二引数に配列として引数を渡す
const obj2 = Reflect.construct(C, [1, 2]);
console.log(obj2);

// Reflect.hasというメソッドとin演算子は同様の動きをする
// aというプロパティがオブジェクトの中に含まれているかどうか判定している
console.log("a" in obj1); // true
console.log("c" in obj1); // false
console.log(Reflect.has(obj1, "b"));
// ときに関数として定義したい場合にReflectを使用する
// 内部メソッドにアクセスする手段を開発者に委ねることによってより柔軟にコードを書くことが出来る

// 便宜的にObjectに格納されていだけ
Object.defineProperty; // エラーが発生したら処理が止まる
// 汎用的なメソッドは今後はReflectに追加されるという仕様にECMAはなってきている
Reflect.defineProperty; // エラーが発生したらfalseが返ってくる

const bob = {
  name: "Bob",
  _hello: function () {
    console.log(`hello ${this.name}`);
  },
};

const tom = {
  name: "Tom",
  _hello: function () {
    console.log(`hello ${this.name}`);
  },
  // プロパティにアクセスしたらメソッドが呼ばれる（Classのレッスンでも解説）
  get hello() {
    return this._hello();
  },
};
tom.hello;
// 内部的には Reflect.get(tom, 'hello', reciever) を実行しているのと同じ
// recieverに登録したオブジェクトがgetメソッドで使用されるthisに束縛される（bindと同じようなもの）
Reflect.get(tom, "hello", bob); // hello Bob
```

## Reflect と Proxy

```js
const targetObj = {
  a: 0,
  // getter経由でオブジェクトの値を取得する方法
  get value() {
    // valueメソッドの返却する値がオブジェクトのaプロパティの値を返却している
    // console.log(this);
    return this.b;
    // receiverを指定した場合、ここでのthisはProxyオブジェクトを参照しているのと同様の動きをしている
    // return pxy.b;
  },
};

const handler = {
  get: function (target, prop, receiver) {
    // console.log(receiver); // receiverにはProxyオブジェクトが格納されている
    // getterにvalue()を作成しメソッドを呼ぶとpropの値はvalueが渡ってくる
    console.log(`[get]: ${prop}`);
    if (target.hasOwnProperty(prop)) {
      // オブジェクトのプロパティを取得する操作はReflectのgetで表現できる
      // ReflectのgetによってtargetObjのgetterが実行される
      // return target[prop];
      return Reflect.get(target, prop, receiver);
    } else {
      // 自分のオブジェクトのプロパティに含まれていないものだったら初期値-1を返す
      return -1;
    }
  },
};
const pxy = new Proxy(targetObj, handler);
console.log(pxy.value);

// Proxy経由だと自身のオブジェクトに含まれていないプロパティを参照したら-1が返ってきてほしいが、
// undefinedが返ってきてる
// これは、targetObj.bと取得しているのと同じなので、存在しないプロパティの値を取得しようとしておりundefinedが返却される
console.log(pxy.b);
// console.log(pxy.a);
```

## WeakMap

- 弱い参照でオブジェクトを保持するコレクションのこと（弱参照をも呼ばれる）
- **キーは必ずオブジェクト**を設定する必要がある
- `for`などを使用した**反復処理は実行できない**

### ガベージコレクション

- JavaScript エンジンにより**参照不可能になった変数や関数を定期的に削除**している

```js
const wm = new WeakMap(); // WeakMapオブジェクトをインスタンス化

const obj = {};
wm.set(obj, "value1"); // オブジェクトに値をセットする

// 上で定義したオブジェクトにnullを代入
// obj = null;
// 変数に新しいオブジェクトを定義
// ここで定義したオブジェクトと最初に定義したオブジェクトでは参照しているメモリが異なるので
// 細書に定義したオブジェクトは二度と呼ばれないことになるので、JavaScriptエンジンにより定期的に削除される
// これをガベージコレクションと呼ぶ
// obj = {};

console.log(wm.delete(obj));
console.log(wm.get(obj));
```

## WeakMap とプライベート変数

```js
// person.js

// WeakMapを使用したプライベート変数の定義方法
const wm = new WeakMap();

export class Person {
  constructor(name, age) {
    // _nameという書き方は外部からはアクセスしないということを他の開発者に明示している書き方
    // プライベート変数、プライベートプロパティなどと呼ぶ
    // this._name = name; // この記述ではエクスポート先の外部からも参照できてしまう

    // インスタンス化されたオブジェクトを元に、Personの内部でのみ使用できるオブジェクトを定義
    // WeakMap自体はエクスポートされていないので外部からはアクセスできない
    // このファイル内でのみ参照できるオブジェクト
    wm.set(this, {
      name,
    });
  }

  hello() {
    console.log(`hello ${wm.get(this).name}`); // hello Tim
  }
}
```

```js
// main.js

import { Person } from "./person.js";

const tim = new Person("Tim", 23);
const bob = new Person("Bob", 23);
tim.hello();
bob.hello();
```

## JSON

- `JSON.parse`は JSON → Object に変換する
- `JSON.stringfy`は Object → JSON に変換する
- Object は**オブジェクト**で、JSON は**文字列**である
- サーバと通信する際は直接`Object`形式では渡せないので、一旦 JSON 形式（文字列）に変換してサーバ側に渡す

```js
const obj = { a: 0, b: 1, c: 2 };

// replacerで戻した値を新しいJSONの文字列に格納することが出来る
function replacer(prop, value) {
  if (value < 1) {
    return;
  }
  return value;
}

// const json = JSON.stringify(obj, replacer); // 第二引数に関数を定義できる
const json = JSON.stringify(obj, ["a", "b"]); // 第二引数に配列で取得したいプロパティを取得できる
// console.log(typeof json); // string
console.log(json);

const obj2 = JSON.parse(json); // 文字列をObjectに変換
console.log(obj2);
// {a: 0, b: 1}
// a: 0
// b: 1
// __proto__: Object
```

## Storage

- ブラウザの保存領域に**データを格納する**ためのオブジェクト → **localStorage**
- localStorage を用いた動作は**同期的**に処理が行われる

```js
const obj = { a: 0 };
const json = JSON.stringify(obj);

// localStorageに値をセットする方法
localStorage.setItem("key", 1);
localStorage.setItem("key2", 1);
localStorage.setItem("key3", json); // JSONを格納

// 値を取得する方法
const result = localStorage.getItem("key");
const obj2 = JSON.parse(result);
console.log(result);
console.log("end");
```

## Array（Part1）

- `Array.prototypr`に格納されていないメソッドは使えない！
- `String`オブジェクトのメソッドなどと混合しないように注意

```js
// 配列の基本操作
const arry = [1, 2, 3, 4, 5];

// 末尾に値を追加
arry.push(6);
console.log(arry);

// 配列の末尾を削除
// 削除した値は実行結果として受け取る事ができる
let result1 = arry.pop();
console.log(arry, result1); // (5) [1, 2, 3, 4, 5] 6

// 配列の一番先頭の値を削除する
// 削除した値を戻り値として受け取れる
let result2 = arry.shift();
console.log(arry, result2);

// 一番先頭に値を追加する（カンマ区切りで複数指定できる）
// 戻り値は追加後の配列の長さ（配列のlengthプロパティの値）が返ってくる
let result3 = arry.unshift(0, 1);
console.log(arry, result3);

// 一番最初に指定したインデックスから、指定した個数の配列内の値を切り取る
// ここでは index 0, 1 の値が切り取られる
// 戻り値は切り取った値が配列として返ってくる
// 第3引数以降は、切り取った配列のインデックスに値を追加することが出来る
// ここでは 0, 1 切り取った箇所に、3, 9, 10 を連続で代入している
let result4 = arry.splice(0, 2, 3, 9, 10);
console.log(arry, result4); // (7) [3, 9, 10, 2, 3, 4, 5] (2) [0, 1]

// 配列を結合する（結合先の配列の末尾に結合される）
const arry2 = arry.concat([11, 12, 13]);
console.log(arry2); // [3, 9, 10, 2, 3, 4, 5, 11, 12, 13]
// スプレッド演算子でも同様のことが出来るので、こちらのほうが使われることが多い
const arry3 = ["文字列", ...arry2, "後方にも", "値を", "追加できる"];
console.log(arry3); // ["文字列", 3, 9, 10, 2, 3, 4, 5, 11, 12, 13, "後方にも", "値を", "追加できる"]
```

## Array（Part2）

```js
const arry = [1, 2, 3, 4, 5];

// 繰り返し処理を行う
// ArrayオブジェクトにはforEachメソッドが格納されている
// forEachのコールバック関数には3つの引数が指定できる
// 第一引数：配列の要素
// 第二引数：配列のインデックス
// 第三引数：渡ってきた配列
arry.forEach(function (elm, index, arry) {
  console.log(elm, index, arry);
});

// 新しい配列を作りたい
// コールバック関数に指定できる引数はforEachと同じ
const newArry = arry.map(function (elm, index, arry) {
  //   return elm * 2; // newArryの新しい配列として値が返却される
  return arry; // 配列の中に配列を格納した二次元配列を作ることが出来る
});
console.log(arry, newArry);

const filterArry = arry.filter(function (elm, index, arry) {
  // return に続く値が truthy であれば新しい配列に格納され、falsy であれば格納されない
  return index > 1;
});

console.log(filterArry);
```

## Array（Part3）

- reduce メソッドに関して

```js
const arry = [1, 2, 3, 4, 5];

// reduceメソッド
const result = arry.reduce(function (accu, curr) {
  // accuには各ループで return された値が格納されている
  // currには
  console.log(accu, curr);
  return accu + curr;
}, 0);
// 第二引数に値を渡すと、1ループ目の accu に値が渡ってくる
// 引数を指定しないと1ループ目の accu には配列の値が渡ってくるので結果が少し変わってくる

console.log(result); // すべての値が合計された 15 が格納されている
```

### チェーンメソッド

- 通常のオブジェクトは反復操作をするメソッドも無いし、`for...of...`を使うことも出来ない
- ただし、クラスで定義してやると簡単に反復操作可能オブジェクトが生成することが出来る

```js
class IteratableObject {
  constructor(obj) {
    for (let prop in obj) {
      this[prop] = obj[prop];
    }
  }

  print(label = "") {
    console.log(`%c ${label}`, "color: blue; font-weight: 600;", this);
    return this;
  }
  set(key, value) {
    // thisに対して値を設定する
    this[key] = value;
    return this;
  }

  forEach(callback) {
    // オブジェクトのインスタンスをループする
    for (let [k, v] of this) {
      // 値、プロパティ、オブジェクトのインスタンスがcallbackに渡る
      callback(v, k, this);
    }
  }
  map(callback) {
    // mapでは新しいインスタンスを生成
    // そのインスタンスに対して値を代入
    // そのインスタンスを返却
    // オリジナルには影響を与えず新しいインスタンスを作ることが出来る
    const newInstance = new IteratableObject();
    for (let [k, v] of this) {
      newInstance[k] = callback(v, k, this);
    }
    return newInstance;
  }
  filter(callback) {
    const newInstance = new IteratableObject();
    for (let [k, v] of this) {
      // callback関数の実行結果がtrueの場合に新しい値をセットする
      if (callback(v, k, this)) {
        // 新しい値をセット
        newInstance[k] = v;
      }
    }
    return newInstance;
  }

  // ジェネレーターで表現する
  *[Symbol.iterator]() {
    // オブジェクト（this）がプロパティでそれぞれループを行う
    // 一つずつ yield によって呼び出し元に値を返す
    for (let key in this) {
      yield [key, this[key]];
    }
  }
}

function prefix(v, i, obj) {
  return "prefix-" + v;
}

const original = new IteratableObject({
  key1: "value1",
  key2: "value2",
  key3: "value3",
});

original.forEach((v) => console.log(v));

const result = original
  .map(prefix) // 新しいインスタンスには prefix の文字をつける。ここで返ってくるオブジェクトはnewInstanceである
  .set("key4", "value4") // newInstanceに対して値を設定している
  .filter(function (val, key) {
    return key === "key4";
  });

console.log("%coriginal", "color: blue; font-weight: bold;", original);
console.log("%cresult", "color: red; font-weight: bold;", result);
/**
 * 期待する出力結果
 * original
 * {key1: "value1", key2: "value2", key3: "value3"}
 *
 * result
 * {key4: "value4"}
 */
```

# Vue のメカニズム

- Vue.js は type script で書かれている
- これを JavaScript に変換してブラウザで実行可能にしている
- この操作のことを**ビルド**という
- あるプログラミング言語から他のプログラミング言語に変換する処理を**トランスパイル**という
- ES6 → ES5 へのダウングレードなど

# Vue と Reactivity

- オブジェクトのプロパティの値が変更された後、他の箇所に及ぼす影響を自動で検知して何らかの関数を実行する
- Vue では`effect`と呼ばれる関数が実行される
- `reactivity.esm-browser.js` では`effect`や`computed`など関数が一つずつ export されているのではなく、オブジェクトとしてまとめて export されている

# その他オブジェクトや関数

# Array オブジェクト

- [オブジェクトが配列かどうかを判定する](https://jsprimer.net/basic/array/#detect-array)

## 配列から要素を検索

## その要素のインデックスが欲しい場合

```js
const array = ["Java", "JavaScript", "Ruby"];
const indexOfJS = array.indexOf("JavaScript");

console.log(indexOfJS); // => 1
console.log(array[indexOfJS]); // => "JavaScript"

// "JS" という要素はないため `-1` が返される
console.log(array.indexOf("JS")); // => -1
```

- `indexOf`メソッドは引数と厳密等価演算子`（===）`で一致する要素があったら、**その要素のインデックスを返す**
- 該当する要素がない場合は`-1`を返す
- `indexOf`メソッドは**先頭から検索して見つかった要素のインデックスを返す**
- `indexOf`メソッドと対になるメソッドで、`Array.prototype.lastIndexOf`メソッドがあり、`lastIndexOf`メソッドでは**末尾から検索した結果が得られる**
- [インデックスを取得](https://jsprimer.net/basic/array/#indexof)

## その要素自体が欲しい場合

- `Array.prototype.find`メソッドを使用

```js
// colorプロパティを持つオブジェクトの配列
const colors = [{ color: "red" }, { color: "green" }, { color: "blue" }];

// `color`プロパティが"blue"のオブジェクトを取得
const blueColor = colors.find((obj) => {
  return obj.color === "blue";
});
console.log(blueColor); // => { "color": "blue" }

// 該当する要素がない場合は`undefined`を返す
const whiteColor = colors.find((obj) => {
  return obj.color === "white";
});
console.log(whiteColor); // => undefined
```

## 指定範囲の要素を取得

- `Array.prototype.slice`メソッドを使用
- 第一引数：**開始位置**
- 第二引数：**終了位置**
- 第二引数は省略することができ、省略した場合は配列の末尾が終了位置となる

```js
const array = ["A", "B", "C", "D", "E"];

// インデックス1から4の範囲を取り出す
console.log(array.slice(1, 4)); // => ["B", "C", "D"]

// 第二引数を省略した場合は、第一引数から末尾の要素までを取り出す
console.log(array.slice(1)); // => ["B", "C", "D", "E"]

// マイナスを指定すると後ろからの数えた位置となる
console.log(array.slice(-1)); // => ["E"]

// 第一引数と第二引数が同じ場合は、空の配列を返す
console.log(array.slice(1, 1)); // => []

// 第一引数 > 第二引数の場合、常に空配列を返す
console.log(array.slice(4, 1)); // => []
```

## その要素が含まれているかという真偽値が欲しい場合

- `ES2016`で追加された`Array.prototype.includes`メソッドを利用する

```js
const array = ["Java", "JavaScript", "Ruby"];

// `includes`は含まれているなら`true`を返す
if (array.includes("JavaScript")) {
  console.log("配列にJavaScriptが含まれている");
}

// 別の書き方 indexOf で値が含まれていないときに-1を返すことを利用
// 含まれているかを判定する以外には利用して無いので、コードが読みづらい
const indexOfJS = array.indexOf("JavaScript");
if (indexOfJS !== -1) {
  console.log("配列にJavaScriptが含まれている");
  // ... いろいろな処理 ...
  // `indexOfJS`は、含まれているのかの判定以外には利用してない
}
```

## [追加と削除](https://jsprimer.net/basic/array/#add-and-delete)

## [配列同士を結合](https://jsprimer.net/basic/array/#concat)

## [配列から要素を削除](https://jsprimer.net/basic/array/#delete-element)

- `Arrary.prototype.splice`メソッドを利用する

```js
const array = [];
array.splice(インデックス, 削除する要素数);

// 削除と同時に要素の追加もできる
array.splice(インデックス, 削除する要素数, ...追加する要素);
```

## [length プロパティへの代入](https://jsprimer.net/basic/array/#assign-to-length)

- `Arrary.prototype.splice`でも出来るが、配列の要素を全て空にすることが出来る

## [破壊的なメソッドと非破壊的なメソッド](https://jsprimer.net/basic/array/#mutable-immutable)

- 配列をコピーする方法として、`slice`メソッドと、`concat`メソッドで**引数無しで呼び出すとその配列のコピーを返す**

## [配列を反復処理するメソッド](https://jsprimer.net/basic/array/#array-iterate)

### `Array.prototype.forEach`

- 配列の要素を先頭から順番にコールバック関数へ渡し、反復処理を行う
- コールバック関数は**要素、インデックス、配列**が引数として渡され、配列要素の先頭から順番に反復処理を行う
- `forEach`メソッドは**返り値を返さない**

```js
const array = [1, 2, 3];
array.forEach((currentValue, index, array) => {
  console.log(currentValue, index, array);
});
// コンソールの出力
// 1, 0, [1, 2, 3]
// 2, 1, [1, 2, 3]
// 3, 2, [1, 2, 3]
```

### `Array.prototype.map`

- `map`メソッドは値を返す
- 返す値は、それぞれのコールバック関数が返した値を集めた新しい配列
- 非破壊的なメソッド

```js
const array = [1, 2, 3];

// 各要素に10を乗算した新しい配列を作成する
const newArray = array.map((currentValue, index, array) => {
  return currentValue * 10;
});
console.log(newArray); // => [10, 20, 30]

// 元の配列とは異なるインスタンス
console.log(array !== newArray); // => true
```

### `Array.prototype.filter`

- コールバック関数が`true`を返した要素だけを集めた新しい配列を返す非破壊的なメソッド
- コールバック関数には**要素、インデックス、配列**が引数として渡される
- 配列から不要な要素を取り除いた配列を作成したい場合に利用する

```js
const array = [1, 2, 3];

// 奇数の値を持つ要素だけを集めた配列を返す
const newArray = array.filter((currentValue, index, array) => {
  return currentValue % 2 === 1;
});
console.log(newArray); // => [1, 3]

// 元の配列とは異なるインスタンス
console.log(array !== newArray); // => true
```

### `Array.prototype.reduce`

- **累積値と配列**を順番にコールバック関数へ渡し、**1 つの累積を返す**
- 配列から**配列以外の値を含む任意の値を作成したいとき**に利用する
- コールバック関数には**累積値、要素、インデックス、配列**を引数としてわたす
- `reduce`メソッドの第二引数には**累積値の初期値となる値を渡せる**
- `array.reduce((累積値, 要素, インデックスを) => {}, 初期値)`

```js
const array = [1, 2, 3];

// すべての要素を加算した値を返す
// accumulatorの初期値は`0`
const totalValue = array.reduce((accumulator, currentValue, index, array) => {
  return accumulator + currentValue;
}, 0);

// 0 + 1 + 2 + 3という式の結果が返り値になる
console.log(totalValue); // => 6
```
