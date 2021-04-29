###### tags: `WordPress`

# WrodPress でオリジナルテーマを作成する

# テーマの保存場所

- `wp-content` → `themes` の中にテンプレートを入れていく

# WordPress に必要なファイル

1. index.php
2. style.css
3. screenshot.png（必ず必要ではないが、管理画面でテーマを選択する時に表示される画像）

# screenshot.png の適切なサイズ

- 画像名：screenshot
- 画像形式：png、jpg、gif
- 画像サイズ：300×225（2 倍のサイズで高解像度の端末にもきれいに表示させることができる）
- テーマのフォルダの中に入れたら、名前を`screenshot`に変更する

# style.css に決まったコメントを入れる

- 上から、**テーマの名前、テーマの作成者、テーマの説明文**を入れていく
- 決まったコメントを入れなければならない

```css
/*
Theme Name: サンプルブログ
Author: サンプルユーザー
Description: テーマの説明文
*/
```

## その他にもコメントとして以下の内容が記述できる

```css
/*
Theme Name: テーマの名前
Theme URL: テーマのホームページアドレス
Author: テーマの作成者
Author URL: テーマ作成者のホームページアドレス
Description: テーマの説明文
Tag: テーマのタグ
*/
```

# テンプレート作成手順

1. テンプレート用の html ファイルを作成する
2. `index.php`と`style.css`を作成する
3. `index.php`から`header.php` と `footer.php`、`sidebar.php`を作成する
4. 固定ページ`page.php`を作成する
5. 記事一覧ページ`blog.php`を作成する
6. 記事ページ`single.php`を作成する
7. テーマのための関数`function.php`を作成する

- 固定ページ：WordPress の機能で管理画面から好きに自分でページが作れる

# ウィジェット

- 画像やリンクなどを 1 つのパーツとして管理し、ドラッグ&ドロップで並べ替えが出来る

# カスタムフィールド

- 管理画面の固定ページ若しくは投稿ページの、編集画面の下の方に入力フォームが表示される
- 予め決められた箇所に、テキストや画像などを挿入できる
- 並びは変えられない

# その他の共通パーツの読み込み方

切り出すパーツは以下のもの

1. `header.php`：head タグまでの共通のもの（header タグの中身では無いので注意！）
2. `content-menu.php`：ロゴやメニューバーの共通部分
3. `sidebar.php`：サイドバー
4. `footer.php`：フッター

## 注意！

- WordPress では共通パーツは、`header.php`、`footer.php`、`sidebar.php`の 3 つしか切り出せない
- その他のパーツを切り出したい（header タグの中に記述されたナビゲーションバーなど）場合は以下のようなルールが有る
-

## header.php を切り出す

- ここは HTML の情報が書かれたヘッダー部のことで、メニューなどを表示させるヘッダーでは無い

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
- `wp_title();`：タイトルを表示させる WordPress で用意されたメソッド、管理画面から変更出来るようにする
- `wp_head();`：WordPress 管理画面などから設定した内容が反映される
- `body_class();`：body タグの中に WordPress で使うクラス属性が入る
- `get_stylesheet_uri();`：このテーマで使われている style.css を探して、そのパスを自動的に埋め込んでくれる
- `bloginfo(' charset ');`：bloginfo()メソッドは WordPress で設定されている色々な情報を引き出すもの（ここでは文字コードを管理画面から指定できるようにしている）

```html
<!DOCTYPE html>
<html lang="ja">
  <head>
    <!-- bloginfo()メソッドはWordPressで設定されている色々な情報を引き出すもの（ここでは文字コードを指定できるようにしている） -->
    <meta charset="<?php bloginfo(' charset '); ?>" />
    <!-- タイトルを表示させるWordPressで用意されたメソッド、管理画面から変更出来るようにする -->
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

  <!-- bodyタグの中にWordPressで使うクラス属性が入る -->
  <body <?php body_class(); ?>
    >
  </body>
</html>
```

## footer.php を切り出す

- 今回は特に特別な指定なし

```html
    <!-- footer -->
    <footer>
      Copyright <a href="#">サンプルホームページ</a>. All Rights
      Reserved.
    </footer>
  </body>
</html>
```

## footer.php を読み込む

- `index.php`の切り取った箇所に記述する
- `get_footer();`：切り出したフッターを読み込むためのメソッド

```php
<?php get_footer(); ?>
```

- 今回は`footer.php`に特有のメソッドなどは記述しない

## メニュー部分を作成する

- `content-menu.php`を作成する
- `スラッグ名-テンプレート名`という命名ルールに従いファイルを作成する
- テンプレート名は無くても良い
- 読み込みたい箇所でこのメソッドを記述する`<?php get_template_part('スラッグ名', 'テンプレート名');?>`
- 引数のテンプレート名を省略することも出来る
- 該当のテンプレート名のファイルが無かった場合、`スラッグ名.php`が読み込まれる

```php
// content-menu.php
// メニューの内容を管理画面から編集するのは後ほど実装する
<header class="site-width">
  <h1><a href="index.html">サンプルページ</a></h1>
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

- 作った共通パーツは`get_template_part('content', 'menu')`で読み込んでいく

```php
// index.php
<?php  get_header(); ?>

    <!-- ナビメニューの共通パーツを読み込む -->
    <?php get_template_part('content', 'menu'); ?>

// 中略
```

## サイドバーを切り出す

- ブログなどでも使用している共通部分を切り出す
- `sidebar.php`を作成して、共通パーツを切り出す
- `get_sidebar()`で呼び出す
- ウィジェットで管理画面から並び替えたり表示させたりすることが出来る

```php
// single.php

// 中略
<!-- サイドバー -->
<?php get_sidebar(); ?>


```

# 固定ページの作成方法

- ページには**投稿ページ**と**固定ページ**という物がある
- 投稿ページは、ブログなどの時系列があり**更新頻度が高いもの**が向いている
- 固定ページは、固定ページは会社情報などの**更新頻度が低いもの**が向いている
- 投稿ページは**1 種類のデザインテンプレートページ**を用意して使う
- 固定ページは複数の種類のテンプレートが用意できるので、ページの内容によって自由にテンプレートを使い分けれられる
- 固定ページには、**カテゴリ**、**タグ**という情報はつけられない

## 固定ページのテンプレートの使い分け

- トップページ、インフォメーション用のテンプレートや、お問い合わせ用のテンプレートを用意することが出来る

## トップページの固定ページを作成する

- WordPress はトップページを読み込む際に`index.php`よりも`home.php`を優先して読み込むので、トップページの内容は`home.php`に記述する
- 固定ページとして認識させるために下記のようにコメントを記述する

```php
<?php
/*
 Template Name: Home～トップページ～
 */
?>
```

- メニュー ▶ 固定ページ ▶ 新規作成 ▶ 右サイドメニューのページ属性のテンプレートから、追加したテンプレートが表示される

## インフォメーションページを作る

- `map.php`を作成して同様に固定ページ用のコメントを付ける
- 管理画面から動的に表示できるように修正していく
- `the_ID()`：固定ページを作成した際に付与される ID を呼び出している
- `post_class()`：固定ページごとに管理画面から投稿する際に、クラス属性を追加出来るようにしている
- `the_content()`：固定ページの本文を出力
- `get_the_title()`：管理画面からタイトルを出力させる

```php
<?php
/*
Template Name: INFO～インフォメーション～
*/
?>

<?php  get_header(); ?>

    <!-- ナビメニューの共通パーツを読み込む -->
    <?php get_template_part('content', 'menu'); ?>

		<div id="main">

        <!-- blog_list -->
        <section id="map">
            <h1 class="title"><?php echo get_the_title(); ?></h1>
            <div id="content">
                <!-- カスタムフィールドから表示させる関数 -->
                <?php echo get_post_meta($post->ID, 'map', true) ?>
            </div>
        </section>
        <section id="shop_info" class="site-width">
            <?php if(have_posts()) : // WordPressループ開始
                while(have_posts()) : the_post(); // 繰り返し処理開始?>
                    <!-- 管理画面から入力した情報を出力するためのhtml部分 -->
                    <!-- 固定ページのIDをもとにID属性を付けている、固定ページごとにスタイリングを変更することも出来る -->
                    <div id="post-<?php the_ID(); ?>" <?php post_class(); ?> >
                        <?php the_content(); ?>
                    </div>
                <?php endwhile; // 繰り返し処理終了
                else: // ここから記事が見つからなかった時の処理

                // 固定ページにはなにか下の情報が含まれているので、else 以下の記述は必要無いが
                // 念の為追加している
                ?>
                    <div class="post">
                        <h2>記事はありません</h2>
                        <p>お探しの記事は見つかりませんでした。</p>
                    </div>
            <?php endif; // WordPressループ終了 ?>
        </section>

<?php get_footer(); ?>
```

## お問い合わせページ

- ほぼインフォメーション用の固定ページと同じ
- カスタムフィールドから入力する Google Map の入力欄がついていない

```php
<?php
/*
Template Name: CONTACT～お問い合わせ～
*/

?>
<?php  get_header(); ?>

  <!-- ナビメニューの共通パーツを読み込む -->
  <?php get_template_part('content', 'menu'); ?>

  <div id="main">
    <!-- Contact -->
    <section id="contact" class="site-width">
          <h1 class="title"><?php echo get_the_title(); ?></h1>
              <?php if(have_posts()) : // WordPressループ開始
                  while(have_posts()) : the_post(); // 繰り返し処理開始?>
                      <!-- 管理画面から入力した情報を出力するためのhtml部分 -->
                      <div id="post-<?php the_ID(); ?>" <?php post_class(); ?> >
                          <?php the_content(); ?>
                      </div>
                  <?php endwhile; // 繰り返し処理終了
                  else: // ここから記事が見つからなかった時の処理
                  ?>
                      <div class="post">
                          <h2>記事はありません</h2>
                          <p>お探しの記事は見つかりませんでした。</p>
                      </div>
              <?php endif; // WordPressループ終了 ?>
      </section>
    </div>

<?php get_footer(); ?>
```

# 記事一覧ページ

- `single.php`と`loop.php`と`category.php`を作成していく
- 記事一覧ページのファイル名というのは決まっていて、`category.php`というファイルを作る

## WordPress ファイルの読み込み順序

1. `category-slug.php`：スラッグ名がついたファイルがあった場合最初に読み込まれる
2. `category-id.php`：上記のファイルが無かった場合は、ID が付与されたファイルが読み込まれる
3. `category.php`
4. `archive.php`
5. `index.php`

- カテゴリーページでページによってデザインを変えたい場合は、`category-slug.php`のファイルを作ってスラッグ名で指定すると良い
- ID で指定すると投稿したページによって ID が変わってしまうので、`category-id.php`でファイルは作成しない
- 同じデザインで良い場合は`category.php`で作成する

### タグごとの一覧ページを作成した際の読み込み順序

1. `tag-slug.php`
2. `tag-id.php`
3. `tag.php`
4. `archive.php`
5. `index.php`

### 日付ごとの一覧ページの読み込み順序

1. `date.php`
2. `archive.php`
3. `index.php`

### 検索ボックで検索してそのキーワードが入っている記事の一覧ページの読み込み順序

1. `search.php`：このファイルが有った場合、このページのデザインで表示される
2. `index.php`

### まとめ

- 記事一覧はループをしてデータを取得し表示している
- ループ部分だけを共通パーツとして`loop.php`とする場合が多い
- カテゴリーやタグ、日付、検索結果用の一覧ページを作れる
- カテゴリーやタグは更に、そのカテゴリーごとに一覧ページを作れる
- それぞれ作りたい一覧ページによってファイル名を決める
- どのページも同じデザインで良い場合は、`archive.php`や`search.php`を作っておけば良い

## ファイルを切り出していく

- `the_permalink()`：記事へのリンク（パーマリンク）が表示される
- `the_author_nickname()`：記事を投稿したユーザーを表示する
- `the_time("Y年m月j日")`：投稿した日付を表示させる
- `single_cat_title('カテゴリー：')`：カテゴリーを表示させる
- `the_content()`：記事の内容を表示させる
- `function_exists("pagination")`：引数に指定した関数があるか確認
- `pagination($wp_query->max_num_pages)`：

```php
// loop.php
<!-- 記事一覧のループ -->
<?php if(have_posts()): ?>
	<?php while(have_posts()): the_post();?>
		<article class="article-item">
				<h2 class="article-title"><a href="<?php the_permalink(); ?>"><?php the_title(); ?></a></h2>
				<h3 style="font-size:80%;"><?php the_author_nickname(); ?> <?php the_time("Y年m月j日"); ?> <?php single_cat_title('カテゴリー：') ?></h3>
				<p class="article-body">
					<!-- 画像も含めた記事が表示される -->
					<?php the_content(); ?>
				</p>
		</article>
	<?php endwhile; // end while posts ?>

<?php endif; // end if posts ?>
```

# 記事ページ

- 記事詳細ページは`single.php`ファイルが読み込まれる
- コードの書き方は記事一覧ページの内容とほぼ同じ
- 個別ページでは前の記事と次の記事へのページング昨日を付けることができる
- カテゴリーやタグの種類ごとに個別記事ページを作ることも出来るが、その場合は`functions.php`に何行か書く必要がある

## コメントの入力

- 記事の詳細ページでコメント入力欄を表示させるには`comments_template()`という関数を使う

## 記事がなかった場合の処理（これは通常ありえないが念の為）

- 記事がなかった場合の処理で、`get_search_form()`という関数を記述して、`search.php`ファイルを読み込むようにしている

## 記事を動的に表示させる為の処理

− 記事のページは一覧ページと違って 1 つのページしか表示しないので、`while(have_posts()) : the_post();`の部分は必要無いが残していても動作に問題はない

## 記事ページコード

```php
// single.php

<?php  get_header(); ?>

    <!-- ナビメニューの共通パーツを読み込む -->
	<?php get_template_part('content', 'menu'); ?>
		<div id="main">

			<!-- blog_list -->
			<section id="blog" class="site-width">
				<h1 class="title">BLOG</h1>
				<div id="content" class="article">

                    <?php if(have_posts()) : ?>
                    <!-- 記事のページというのは一覧ページと違って1つしか表示しないのでループ処理は必要ないのでwhile文は今回は削る -->
                    <!-- 残していても動作に問題はない -->
                    <?php // while(have_posts()) : the_post();?>

                    <?php the_post();?>

                    <article class="article-item">
                        <h2 class="article-title"><a href="<?php the_permalink(); ?>"><?php the_title(); ?></a></h2>
                        <h3 style="font-size:80%;"><?php the_author_nickname(); ?> <?php the_time("Y年m月j日"); ?> <?php single_cat_title('カテゴリー：') ?></h3>
                        <!-- <img src="" class="article-img"> -->

                        <p class="article-body">
                            <?php the_content(); ?>
                        </p>
                    </article>

                    <!-- 今回は削除 -->
                    <?php // endwhile; ?>
                </div>
            <!-- サイドバー -->
            <?php get_sidebar(); ?>

            <!-- コメント -->
            <?php comments_template(); ?>

            <!-- 記事がない場合の処理 -->
            <?php else : ?>
                    <h2 class="title">記事が見つかりませんでした</h2>
                    <p>検索で見つかるかもしれません</p><br/>
            <?php get_search_form(); ?>
            <?php endif; ?>

            <div class="pagenation">
                <ul>
                    <li class="prev"><?php previous_post_link('%link',  'PREV'); ?></li>
                    <li class="next"><?php next_post_link('%link',  'NEXT'); ?></a></li>
                </ul>
            </div>
        </section>
    </div>
<?php get_footer(); ?>
```

# functions.php ファイルを作成してカスタマイズしていく

- `functions.php`では最後の**php の閉じタグは閉じては駄目！**

## ロゴ画像を動的に表示させる

- `content-menu.php`のロゴの a タグの href 属性の部分に`<php echo home_url(); >`と記述する
- `home_url()`でサイトの URL が取得できるのでそれを`echo`で表示させている
- img タグの src 属性の部分で`<?php header_image(); ?>`とすることで、管理画面から追加した画像が表示れる
- alt 属性は`<?php bloginfo('name') ?>`とすることで**サイトのタイトル**を表示させるようにしている

## カスタムヘッダーを使うための処理

- これを記述することで、外観メニューにヘッダーという項目が追加されている
- デフォルトの画像は用意していないので何も表示されていない
- [add_theme_support 関数のドキュメント](https://wpdocs.osdn.jp/%E9%96%A2%E6%95%B0%E3%83%AA%E3%83%95%E3%82%A1%E3%83%AC%E3%83%B3%E3%82%B9/add_theme_support)

```php
// カスダムヘッダーの画像の設置
$custom_header_defaults = array(
    // テンプレートまでのURLが出力され、その中の/images/headers/logo.pngを取ってきている
    'default-image' => get_bloginfo('template_url').'/images/headers/logo.png',
    'header-text' => false, // ヘッダー画像上にテキストをかぶせる
);

// カスタムヘッダーを有効化する
add_theme_support('custom-header', $custom_header_defaults);
```

## カスタムメニューを作成する

- カスタムメニューを表示させる箇所に`wp_nav_menu()`という関数を使う
- 引数には連想配列の形式でパラメーターを渡す
- [wp_nav_menu 関数のドキュメント](https://wpdocs.osdn.jp/%E9%96%A2%E6%95%B0%E3%83%AA%E3%83%95%E3%82%A1%E3%83%AC%E3%83%B3%E3%82%B9/wp_nav_menu)

```php
// content-menu.php
<nav id="top-nav">
  <?php
    wp_nav_menu(array(
      'theme_location' => 'mainmenu',
      'container' => '', // メニューを囲うタグを設定できる
      'menu_class' => '', // 生成されるメニューのHTMLにクラスを指定できる
      'items_wrap' => '<ul>%3$s</ul>' // 生成されるHTMLのフォーマットを指定できる
    ));
  ?>
</nav>
```

## カスタムメニューを使えるようにする

- カスタムメニューを表示させる位置と、管理画面に表示させる位置の名前を指定する
- `functions.php`に、`register_nav_menu`という関数を追加する
- 第一引数にさっき`wp_nav_menu関数`で指定した、`'theme_location' => 'mainmenu'`の value の方（`mainmenu`）を指定する
- 第二引数は管理画面で表示される名前を指定する
- [register_nav_menu 関数のドキュメント](https://wpdocs.osdn.jp/%E9%96%A2%E6%95%B0%E3%83%AA%E3%83%95%E3%82%A1%E3%83%AC%E3%83%B3%E3%82%B9/register_nav_menu)

```php
// functions.php

// カスタムメニューを使用
register_nav_menu('mainmenu', 'メインメニュー');
```

## カスタムメニューを実際に表示させる

- 外観 ▶ メニューから任意の名前でメニューを作成することが出来る
- 固定ページのリンクや自分で URL を指定する**カスタムリンク**なども作成できる
- 作ったメニューの表示させる箇所を指定するには、**位置の管理**タブから指定する
- テーマの位置の欄に`register_nav_menu関数`の**第二引数に指定**した、メニューの名前が表示される

## ページネーション

- `functions.php`にページネーションを表示させる関数を記述する
- 関数の引数には WordPress 側で自動的に計算された全体のページ数を渡している

```php
// functions.php

function pagination($pages = '', $range = 2) // 第二引数は一覧に何ページ表示させるか指定している
{
    // 表示するページ数（5ページ表示）
    $showitems = ($range * 2) + 1;

    // 現在のページの値($pagedはWordPress側で用意されているグローバル変数)
    global $paged;
    // デフォルトのページ
    if( empty($paged) ) $paged = 1;

    // 呼び出し側の関数で引数が空だったらこの中の処理が走る
    if( $pages == '' ){

        global $wp_query; // WordPress側で用意されているグローバル変数

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

## ブログ一覧ページを表示させる

- 複数ダミー記事を作っておく
- 外観 ▶ メニュー ▶ カテゴリー ▶ 未分類を追加
- メニューの名前をブログへ変更

## カスタムフィールド

- カスタムフィールドを作るには以下の関数を作成する
  - 管理画面にフォームを表示する関数
  - フォームをどこに表示するか設定する関数
  - フォームに入力された情報をデータベースへ保存する関数
- カスタムフィールドは投稿ページか固定ページに表示させることが出来る
- カスタムフィールドに入力した情報は、各投稿・固定ページ毎に情報を保存しておくことが出来る
- `get_post_meta`：投稿のカスタムフィールドの値をデータベースから取得
- [get_post_meta 関数のドキュメント](https://wpdocs.osdn.jp/%E9%96%A2%E6%95%B0%E3%83%AA%E3%83%95%E3%82%A1%E3%83%AC%E3%83%B3%E3%82%B9/get_post_meta)

```php
// functions.php

// 投稿ページへ表示するカスタムボックスを定義する
// カスタムフィールドを作成するには add_action関数 を使ってこれから使うカスタムフィールドを登録していく
// 第一引数：必ず admin_menu とする
// 第二引数：これから作る関数名を指定。カスタムフィールドの設定情報を定義するための関数
add_action('admin_menu', 'add_custom_inputbox');

// 追加した表示項目のデータ更新・保存のためのアクションフック
// データベースに登録する関数も同様に作成する。第一引数は必ず`save_post`とする
add_action('save_post', 'save_custom_postdata');

/*======================================================
入力項目がどの投稿タイプのページに表示されるのかの設定
========================================================*/
function add_custom_inputbox()
{
    // 第一引数：編集画面のhtmlに挿入されるid属性
    // 第二引数：管理画面に表示されるカスタムフィールド名
    // 第三引数：メタボックスの中に出力される関数名
    // 第四引数：管理画面に表示するカスタムフィールドの場所（postなら投稿、pageなら固定ページ）
    // 第五引数：配置される順序
    // ABOUT入力欄（home.php）
    add_meta_box('about_id', 'ABOUT入力欄', 'custom_area', 'page', 'normal');
    // RECRUIT入力欄（home.php）
    add_meta_box('recruit_id', 'RECRUIT入力欄', 'custom_area2', 'page', 'normal');
    // MAP入力欄（map.php）
    add_meta_box('map_id', 'MAP入力欄', 'custom_area3', 'page', 'normal');
    // トップバナー入力欄（home.php）
    add_meta_box('top_img_id', 'トップ画像URL入力欄', 'custom_area4', 'page', 'normal');
}

// home.phpのABOUTの入力フォームを表示させる為の関数
function custom_area()
{
    // カスタムフィールドを作る関数の中では必ずグローバル関数を使う
    global $post;

    // 入力フォーム用のHTMLを表示させている
    // get_post_meta($post_id, $key, $single)
    // 第一引数：カスタムフィールドを取得したい投稿のID
    // 第二引数：取得したいキー名の文字列
    // 第三引数：真偽値を指定。（初期値：false）trueをセットした場合文字列を返す。falseをセットした場合カスタムフィールドの配列を返す
    echo 'コメント ：<textarea cols="50" rows="5" name="about_msg">'. get_post_meta($post->ID, 'about', true) .'</textarea><br>';
}

// home.phpのRECRUTの入力フォームを表示させるための関数
function custom_area2()
{
    // カスタムフィールドを作る関数の中では必ずグローバル関数を使う
    global $post;

    echo '<table>';
    for($i = 1; $i <= 8; $i++){
        echo '<tr><td> RECRUIT INFO ：'.$i.'</td><td><input name="recruit_info'.$i.'" value="'.get_post_meta($post->ID, 'recruit_info'.$i, true).'"></td></tr>';
    }
    echo '</table>';
}

// INFOページのMAPフィールドの入力フォームを表示させるための関数
function custom_area3()
{
    // カスタムフィールドを作る関数の中では必ずグローバル関数を使う
    global $post;

    echo 'コメント ：<textarea cols="50" rows="5" name="map">'. get_post_meta($post->ID, 'map', true) .'</textarea><br>';
}

// home.phpのトッページ画像を表示させるための関数
function custom_area4()
{
    // カスタムフィールドを作る関数の中では必ずグローバル関数を使う
    global $post;

    echo 'トップ画像URL ：<input type="text" name="img-top" value="'.get_post_meta($post->ID, 'recruit_info'.$i, true).'"> ';
}
```

## カスタムフィールドに入力された情報をデータベースに保存する関数を作成する

- `save_custom_postdata($post_id)`：引数にはカスタムフィールドを入力した固定ページの ID が入ってくる

```php
// functions.php

/*======================================================
投稿した内容をデータベースに保存する為の処理
========================================================*/
function save_custom_postdata($post_id)
{
    $about_msg = '';
    $recruit_data = '';
    $map_data = '';
    $img_top = '';

    // カスタムフィールドに入力された情報を取り出す
    if( isset($_POST['about_msg']) ){
        $about_msg = $_POST['about_msg'];
    }

    // カスタムフィールドに入力された情報とデータベースの情報を比較して
    // 内容が変わっていた場合、保存していた情報を更新する
    if( $about_msg != get_post_meta($post_id, 'about', true) ){

      // データベースの情報を更新する処理
      update_post_meta($post_id, 'about', $about_msg);
    }elseif( $about_msg = ''){

      // 入力された情報が空だった場合に、データベースの情報も空にするための処理
      // 第三引数にはデータベースの情報を指定する
      // ある固定ページIDでキーがaboutの情報を持っているものを削除しなさいという記述
      delete_post_meta($post_id, 'about', get_post_meta($post_id, 'about', true));
    }

    // RECRUITの入力フォーム
    // ループ処理で動的に入力フォームを生成
    for($i = 1; $i <= 8; $i++){
        if(isset($_POST['recruit_info'.$i])){
            $recruit_data = $_POST['recruit_info'.$i];

        }
        if( $recruit_data != get_post_meta($post_id, 'recruit_info'.$i, true) ){
            update_post_meta($post_id, 'recruit_info'.$i, $recruit_data);
        }elseif( $recruit_data == ''){
            delete_post_meta($post_id, 'recruit_info'.$i, get_post_meta($post_id, 'recruit_info'.$i, true));
        }
    }

    // MAPの入力フォーム
    if( isset($_POST['map']) ){
        $map_data = $_POST['map'];
    }
    // 内容が変わっていた場合、保存していた情報を更新する
    if( $map_data != get_post_meta($post_id, 'map', true) ){
        update_post_meta($post_id, 'map', $map_data);
    }elseif( $map_data = ''){
        delete_post_meta($post_id, 'map', get_post_meta($post_id, 'map', true));
    }

    // home.phpのトップ画像
    if( isset($_POST['img-top']) ){
        $img_top = $_POST['img-top'];
    }
    // 内容が変わっていた場合、保存していた情報を更新する
    if( $img_top != get_post_meta($post_id, 'img-top', true) ){
        update_post_meta($post_id, 'img-top', $img_top);
    }elseif( $img_top = ''){
        delete_post_meta($post_id, 'img-top', get_post_meta($post_id, 'img-top', true));
    }
}
```

## 作成した固定ページをトップページに設定する

- 外観 → カスタマイズ → ホームページ設定 → ホームページの表示配下のラジオボックスを固定ページにチェック → ホームページ欄から作成した固定ページ「HOME」を指定

## カスタムウィジェット

- ウィジェットの良いところは、**並び替えができる**ことと、ウィジェット本体を作ってそのエリアに色々なウィジェットを登録することで、**ウィジェット本体の方で持っている HTML のテンプレートを生成できる**こと

## サイドバーウィジェット

- 管理画面のカテゴリー → 自作したウィジェットエリア → 追加

# テンプレートとしての最終調整

- リンクの修正

## ツールバーを表示しないようにする

- ユーザー → あなたのプロフィール → サイトを見る時にツールバーを表示する → オフ

## 投稿記事や画像、メニューと言った情報をファイルにエクスポート

- ツール → エクスポート → 全てのコンテンツ → チェックしてダウンロード
- 適当に作ったフォルダへそのファイルを入れる
- 作成したテーマを**zip ファイル**にして同じフォルダに入れる
