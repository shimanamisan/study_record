###### tags: `JavaScript`

# JavaScript とは

- ブラウザ上で動作させるプログラムが書ける言語で、HTML や CSS を動的に書き換えることができる
- HTML では`文書構造`を作り、CSS で`見た目を整える`ことをして、JavaScript で`動き`をつける
- ホームページに動きをつけることができる

## JavaScript の書き方

### 1.`<script>`タグの中に書く

- 要素が全て読み込まれないと予期せぬバグを生むので、`bodyタグ`の終了直前に記述することが多い

### 2. HTML タグ内に書く

```html
<p onclick="alert('アラートです');">
	押すと「アラート」というウィンドウが出る！
</p>
```

### 3. 外部ファイルから読み込む

- `type="text/javascript"`は HTML5 以前のバージョンでは、そのスクリプトが`JavaScript`であることを記述する必要があったが、HTML5 から`type`の指定がない場合はデフォルトで JavaScript として処理される
- 文書宣言は`<!DOCTYPE html>`で宣言すると HTML5 で書いているという意味

```html
<script type="text/javascript" src="main.js"></script>
```

## 関数とは

- 関数は毎回同じ処理を書かなければいけない部分を、`一つの処理に`まとめて、**色々な場所から呼び出して使う**ことができる
- 関数には`return`を使うことで、`結果の値を返すことができる`
- 関数には`引数`としていくつも値を渡すことができるので、**渡す値によって返ってくる結果を返る**ことができる
- 関数名が同じでも、**引数の個数が違う場合は別の関数**となる

## DOM とは

- `DOM（Document Object Model）`は、HTML の要素にアクセスしてその要素の情報を書き換えたり削除したり操作するための仕組みのこと
- DOM はブラウザの中では、`DOMツリー`と呼ばれる方法で管理されている
- 要素ごとに枝分かれさせて、親子・兄弟のようにお互いの関係をわかりやすく表現されている
- `要素＝ノード`と呼ばれる
- プログラミング言語は上から順番に読み込まれるので、DOM が出来上がる前に JavaScript で操作しようとしても**エラーになる**
- まず初めに`documentというノード`が作成されて、そこから`htmlタグ`、`bodyタグ`と作成されていく

```js
// JavaScriptの書き方の例
window.addEventListener(
	"DOMContentLoaded",
	function () {
		// スクリプトを書く
		alert("test");
	},
	false
);
```

- `DOMContentLoaded`だと、DOM を作り終えたら実行、**こっちのイベントを使う**
- `load`だと、画像や CSS がすべて読みこまれたら実行
- 動作としてはさほど違いはないが、JavaScript は DOM を操作していくので、DOM を作り終わった段階で実行しても問題ない

## JavaScript はオブジェクトを操作する言語

```html
<!DOCTYPE html>
<html lang="ja">
	<head>
		<meta charset="UTF-8" />
		<meta name="viewport" content="width=device-width, initial-scale=1.0" />
		<title>JavaScript</title>
	</head>
	<body>
		<h1 id="title">ホームページの見出し</h1>
		<p>
			最初のホームページが完成しました
			<a href="index.html">次へ </a>
		</p>
		<script>
			window.addEventListener(
				"DOMContentLoaded",
				function () {
					// documentとうオブジェクトの中の、getElementByIdと言うメソッドを呼び出している
					var Node = document.getElementById("title");
					// カラーのプロパティを変更している
					Node.style.color = "red";
				},
				false // 第3引数にfalseを指定しているが、おまじないのようなもの
			);
		</script>
	</body>
</html>
```

```js
// 抜粋
window.addEventListener(
	"DOMContentLoaded",
	function () {
		// documentとうオブジェクトの中の、getElementByIdと言うメソッドを呼び出している
		var Node = document.getElementById("title");
		// カラーのプロパティを変更している
		Node.style.color = "red";
	},
	false // 第3引数にfalseを指定しているが、おまじないのようなもの
);
```

- DOM も`オブジェクト`のことで、オブジェクトとは`プロパティ`と`メソッド`と言うものを持っている

## 色々なオブジェクト

- `documentオブジェクト`は、`html要素`を書き換えたり操作したりするための`メソッド`や`プロパティ`を持っている
- `windowオブジェクト`は、ブラウザの横幅や縦幅といった`画面の情報（プロパティ）`やそれを操作するための`メソッド`を持っている
- `locationオブジェクト`は、パスの`プロパティ`や`メソッド`を持っている
- `Stringオブジェクト`は、文字列の`プロパティ`や`メソッド`を持っている

## オブジェクトの操作方法

- オブジェクトのメソッドを実行したい場合、`オブジェクト名.メソッド名(引数値);`
- オブジェクトのプロパティを変更したい場合、`オブジェクト名.プロパティ名 = 値;`

```js
// HTML要素を取得する方法

// HTML5以前
getElementByid("id値");
getElementsByTagName("タグ名");
getElementsByClassName("クラス名");

// HTML5から
querySelector("セレクター");

// 例
document.querySelector("#title");
```

# 補足

## イベントリスナーとは

- `addEventListenerメソッド`は、JavaScript からさまざまなイベント処理を実行することができるメソッド。
- **イベント処理**とは
  Web サイトを構成するページ内では、以下のようにさまざまなイベントが発生するのです。

・Web ページが読み込まれたかどうか？
・マウスによるクリックがされたかどうか？
・フォームに何らかの操作が行われたかどうか？
・キーボードから入力が行われたかどうか？
・etc…

【label タグ】
「名前」などと書かれたテキストを label で囲むと、このテキストをクリックしたときにフォームにフォーカスが当たる

【clearFormAll メソッドの使い方例】

<script>
function clearFormAll() {
    for (var i=0; i<document.forms.length; ++i) {
        clearForm(document.forms[i]);
    }
}
function clearForm(form) {
    for(var i=0; i<form.elements.length; ++i) {
        clearElement(form.elements[i]);
    }
}
function clearElement(element) {
    switch(element.type) {
        case "hidden":
        case "submit":
        case "reset":
        case "button":
        case "image":
            return;
        case "file":
            return;
        case "text":
        case "password":
        case "textarea":
            element.value = "";
            return;
        case "checkbox":
        case "radio":
            element.checked = false;
            return;
        case "select-one":
        case "select-multiple":
            element.selectedIndex = 0;
            return;
        default:
    }
}
</script>
