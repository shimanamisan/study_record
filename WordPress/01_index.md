###### tags: `WordPress`

# WrodPress でオリジナルテーマを作成する

# テーマの保存場所

![](/img/wordpress/L03_1.jpg)

# WordPress に必要なファイル

1. index.php
2. style.css
3. screenshot.png（必ず必要ではないが、管理画面でテーマを選択する時に表示される画像）

# screenshot.png の適切なサイズ

- 画像名：screenshot
- 画像形式：png、jpg、gif
- 画像サイズ：300×225（2 倍のサイズで高解像度の端末にもきれいに表示させることができる）
- テーマのフォルダの中に入れたら、名前を**screenshot**に変更する

# style.css に決まったコメントを入れる

- 上から、**テーマの名前、テーマの作成者、テーマの説明文**を入れていく
- 決まったコメントを入れなければならない

```css
/*
Theme Name: しまなみさんブログ
Author: しまなみさん
Description: テーマの説明文
*/
```

### その他にもコメントとして以下の内容が記述できる

![](/img/wordpress/L03_4.jpg)

# テンプレート作成手順

![](/img/wordpress/L03_5.jpg)

- 固定ページ：WordPress の機能で管理画面から好きに自分でページが作れる

### ウィジェット

- 画像やリンクなどを 1 つのパーツとして管理し、ドラッグ&ドロップで並べ替えが出来る

### カスタムフィールド

- 予め決められた箇所に、テキストや画像などを挿入できる

# Lesson3 まとめ

![](/img/wordpress/L03_6.jpg)

# その他の共通パーツの読み込み方

切り出すパーツは以下のもの

1. `header.php`：head タグまでの共通のもの（header タグの中身では無いので注意！）
2. `content-menu.php`：ロゴやメニューバーの共通部分
3. `sidebar.php`
4. `footer.php`

## 注意！

- WordPress では共通パーツは、`header.php`、`footer.php`、`sidebar.php`の 3 つしか切り出せない
- その他のパーツを切り出したい（header タグの中に記述されたナビゲーションバーなど）場合は以下のようなルールが有る

![](/img/wordpress/L04_1.jpg)

## header.php を切り出す

```html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="utf-8" />
    <title>サンプルホームページ</title>
    <link rel="stylesheet" type="text/css" href="style.css" />
    <link
      href="http://fonts.googleapis.com/css?family=Montserrat:400,700"
      rel="stylesheet"
      type="text/css"
    />
  </head>

  <body></body>
</html>
```

## hedaer.php を読み込む

- `index.php`の切り取った箇所に以下を記述する

```php
<?php get_header(); ?>
```

## header.php を編集する

- WordPress 特有のメソッドなどを記述していく

```html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <!-- bloginfo()メソッドはWordPressで設定されている色々な情報を引き出すもの（ここでは文字コードを指定できるようにしている） -->
    <meta charset="<?php bloginfo(' charset '); ?>" />
    <!-- タイトルを表示させるWordPressで用意されたメソッド -->
    <title><?php wp_title(); ?></title>
    <!-- このテーマで使われているstyle.cssを探して、そのパスを自動的に埋め込んでくれる -->
    <link href="<?php echo get_stylesheet_uri(); ?>" rel="stylesheet" />
    <link
      href="http://fonts.googleapis.com/css?family=Montserrat:400,700"
      rel="stylesheet"
      type="text/css"
    />
    <!-- WordPress管理画面などから設定した内容が反映される -->
    <?php wp_head(); ?>
  </head>

  <!-- bodyタグの中にWordPressで使うクラス属性をつける -->
  <body <?php body_class(); ?>
    >
  </body>
</html>
```

## footer.php を切り出す

```html
    <!-- footer -->
    <footer>
      Copyright <a href="http://webukatu.com/">ウェブカツ!!サンプルホームページ</a>. All Rights
      Reserved.
    </footer>
  </body>
</html>
```

## footer.php を読み込む

- `index.php`の切り取った箇所に記述する

```php
<?php get_footer(); ?>
```

- 今回は`footer.php`に特有のメソッドなどは記述しない

## メニュー部分を作成する

- `content-menu.php`を作成する
- `スラッグ名-テンプレート名`という命名ルールに従いファイルを作成する
- テンプレート名は無くても良い

```html
<!-- メニュー -->
<header class="site-width">
  <h1><a href="index.html">WEBUKATU</a></h1>
  <nav id="top-nav">
    <ul>
      <li><a href="index.html">HOME</a></li>
      <li><a href="#about">ABOUT</a></li>
      <li><a href="#merit">MERIT</a></li>
      <li><a href="#recruit">RECRUIT</a></li>
      <li><a href="contact.html">CONTACT</a></li>
      <li><a href="map.html">MAP</a></li>
      <li><a href="blog_list.html">BLOG</a></li>
    </ul>
  </nav>
</header>
```

# 固定ページの作成方法

# WordPress ファイルの読み込み順序

# 記事詳細画面

# functions.php ファイルを作成してカスタマイズしていく

## ページネーション

```php
function pagination( $pages='', $range=2 )
{
    // 表示するページ数（5ページ表示）
    $showitems = ($range * 2)+1;

    // 現在のページの値
    global $paged;
    // デフォルトのページ
    if( empty($paged) ) $paged = 1;


    if( $pages =='' ){

        global $wp_query;
        $pages = $wp_query->max_num_pages; // 全ページを取得
        if(!$pages){ // 全ページ数が空の場合は1とする
            $pages = 1;
        }
    }

    if( 1 !=$pages ) // 全ページが1でない場合はページネーションを表示する
    {
        // ダブルクォーテーションはそのままでは出力できないのでエスケープする
        echo "<div class=\"pagenation\">\n";
        echo "<ul>\n";

        // PREV：現在のページ値が1より大きい場合は表示
        if( $paged > 1 ) echo "<li class=\"prev\"><a href='".get_pagenum_link($paged-1)."'>Prev</a></li>\n ";

        for($i=1; $i <= $pages; $i++)
        {
            if(1 != $pages &&( !($i >= $paged+$range+1 || $i <= $paged-$range-1) || $pages <= $showitems))
            {
                // 三項演算子での条件分岐
                echo ($paged == $i)? "<li class=\"active\">" .$i. "</li>\n" : "<li><a href='".get_pagenum_link($i)."'>" .$i. "</a></li>\n";
            }
        }

        // NEXT：総ページ数より現在のページ値が小さい場合は表示
        if($paged < $pages) echo "<li class=\"next\"><a href='".get_pagenum_link($paged+1)."'>Next</a></li>\n";
        echo "</ul>\n";
        echo "</div>\n";
    }
}
```

## カスタムフィールド

- カスタムフィールドを作成するには`add_action関数`を使ってこれから使うカスタムフィールドを登録していく
- 第一引数：必ず`admin_menu`
- 第二引数：これから作る関数名を指定。カスタムフィールドの設定情報を定義するための関数
- データベースに登録する関数も同様に作成する。第一引数は必ず`save_post`とする

```php
// 投稿ページへ表示するカスタムボックスを定義する
add_action('admin_menu', 'add_custom_inputbox');
// 追加した表示項目のデータ更新・保存のためのアクションフック
add_action('save_post', 'save_custom_postdata');
```

- `functions.php`では最後の**php の閉じタグは閉じては駄目！**

## カスタムウィジェット

- ウィジェットの良いところは、**並び替えができる**ことと、ウィジェット本体を作ってそのエリアに色々なウィジェットを登録することで、**ウィジェット本体の方で持っている HTML のテンプレートを生成できる**こと

## サイドバーウィジェット

- 管理画面のカテゴリー ▶ 自作したウィジェットエリア ▶ 追加

# テンプレートとしての最終調整

- リンクの修正

## ツールバーを表示しないようにする

- ユーザー ▶ あなたのプロフィール ▶ サイトを見る時にツールバーを表示する ▶ オフ

## 投稿記事や画像、メニューと言った情報をファイルにエクスポート

- ツール ▶ エクスポート ▶ 全てのコンテンツ ▶ チェックしてダウンロード
- 適当に作ったフォルダへそのファイルを入れる
- 作成したテーマを**zip ファイル**にして同じフォルダに入れる