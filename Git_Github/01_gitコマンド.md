###### tags: `gitコマンドまとめ`

# Github とは

- Git リポジトリのホスティングサービス
- コードを Github に保管できる
- ソーシャルコーディングの場であり、オープンソースの開発ができる
- 世界中の開発に参加することができる

# ローカルでの流れ

- リポジトリ：変更履歴を記録する場所
- commit：個人リポジトリに変更履歴を記録
- push：個人リポジトリの変更履歴をサーバ上に記録する（リモーロリポジトリ）

# リモートリポジトリが関係してくる操作

- pull：共有リポジトリの情報をローカルリポジトリに同期
- プルリクエスト：自分が変更したコードを取り込んでもらえるようにリクエストすること

## Git の基本的なフロー

1. ファイルの変更をステージングエリアへ追加する（コミット前の準備）
   - コミットは 1 つの作業ごとに 1 コミットしたほうが管理しやすい
   - わかりやすいコミットメッセージは**何をどう変更したのか記載する**
   - **1 行目：変更内容の要約（タイトルや概要）**
   - **2 行目：空行**
   - **3 行目：変更した理由**
2. ローカルリポジトリにコミット
3. リモートリポジトリへプッシュ

# ローカルリポジトリを作成

```shell
# 初期化コマンド
git init
```

- `.git`ディレクトリが作成される
- この中にファイルの変更履歴の情報が貯蔵されていく

# ステージングエリアへ追加

```shell
# ファイル名を指定するやり方
git add ファイル名

# すべてのファイルをステージングエリアへ追加するやり方
git add .
```

# 削除したファイルをステージングエリアへ追加する

```shell
# ファイル名を記載
git rm ファイル名

# ディレクトリも一緒に削除
git rm -r ディレクトリ名

# すべてのファイルを追加
git rm .

#
git reset HEAD ファイル名

#
git checkout ファイル名
```

# ローカルリポジトリへコミットする

```shell
#
git commit

# -m オプションでコメントを指定
git commit -m 'first commit'

# -v オプションで何の変更したのか確認することもできる
git commit -v
```

# 変更差分を確認

```shell
# ステージとの差分
git diff

# ステージとコミットとの差分
git diff HEAD
```

# コミット履歴の確認

```shell
git log

# 1行で表示する
git log --oneline

# 表示するログ数を指定する（3行を表示）
git log -n 3

# onelineオプションとの組み合わせ（1行で表示して3つの履歴を表示）
git log --oneline -n 3

# ファイルの差分を表示する
git log -p index.html
```

- `git log`中に終了するには**q キーを押す**
- **j で下に移動し、k で上に移動する**

# ローカルリポジトリの状態を確認

```shell
git status
```

# git の管理からファイルを外したい

- git から常に無視されるわけではない
- **.gitignore ファイル**も設定して今後も除外されるようにすること

```shell
git rm --cached
```

# Github のリモートリポジトリを登録する（初回のみ）

- 今後は**origin**という名前でリモートリポジトリへプッシュできる
- エイリアスを作成しているようなイメージ

```shell
git remote add origin 'リモートリポジトリのURL'
```

# リモートリポジトリへプッシュ（マスターブランチの場合）

```shell
git push -u origin master
```

# 新しくブランチを作成

```shell
# ブランチを切る
git checkout -b webpack-dev-server

# ブランチが変更されているか確認
git status

# 後は一連の流れでマージまでしていく
# コミットをステージングエリアへ追加
git add .

# 変更点をレビュー
git diff --cached

# コミットする
git commit -m 'install webpack-dev-server'

# コミットをリモートにプッシュ
git push -u origin HEAD

# マスターブランチに戻る
git checkout -

# --no--ff をつけることで意図的にfast-fowardを行わないコミットをすることができる
git merge --no-ff - -m 'merge branch into master'

# 最後にリモートへプッシュ
git push origin HEAD

```

# 変更ファイルを退避させる

```sh
# 変更ファイルを退避させる
git stash

# 退避させたファイルを見る
git stash list
stash@{0}: WIP on master: d055036 簡単ログインを実装
# stash@{0} に退避している

# 退避させたファイルを適応させる
git stash apply stash@{0}
```
