# Laravel プロジェクト作成

updated at: 2024-09-01

## 前提

- WSL(Ubuntu24), apache2, mariadb を想定

```bash
# composer の確認(V は大文字)
composer -V

# 出力
Composer version 2.7.1 2024-02-09 15:26:28

# bun.js の確認(v は小文字)
bun -v

# 出力
1.1.26

# または node
node -v

# 出力
v20.17.0
```

---

## Laravel をインストール

[Laravel公式: Creating a Laravel Project](https://laravel.com/docs/11.x/installation#creating-a-laravel-project) が公式ドキュメント

次のコマンドで、カレントディレクトリ(現在のディレクトリ)に、プロジェクトディレクトリ(new_project_name)が作成される。new_project_name は好きな名前でよい。

```bash
composer create-project laravel/laravel new_project_name
```

インストール後、プロジェクトディレクトリに入り、JS用のパッケージを npm か bun で、php 用パッケージを composer でインストール。インストール済みかもしれないが念のため。

```bash
cd new_project_name

# フロント側のパッケージを node_modules にインストール
bun install
# または
npm install

# バック側のパッケージを vendor にインストール。
composer install
```

---

## .env を編集

### .env が無い場合の対応

`.env.example` をコピーして `.env` を作成する。

```bash
cp .env.example .env
```

### データベースの設定

まっさきに行うべきはデータベースの設定を書くこと。

まず、下記の設定箇所を探す。

```bash
DB_CONNECTION=sqlite
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=laravel
# DB_USERNAME=root
# DB_PASSWORD=
```

`DB_CONNECTION` の値を、用いるデータベースサーバーの名前に書き換える。今回は mariadb。

コメントアウト部分をコメントインし、事前に作っておいたデータベースやユーザーの情報を入力していく。

```bash
DB_CONNECTION=mariadb # sqlite から変更
DB_HOST=127.0.0.1
DB_PORT=3306 # ポート番号。mariadb の場合、大抵は3306だが、いちおう確認しておく。
DB_DATABASE=new_db_name # 新しいデータベースの名前(空のデータベースであること)
DB_USERNAME=new_user_name # 上記データベースについて全権限を持つユーザー名
DB_PASSWORD=new_password # そのユーザーのパスワード
```

### 言語関係の設定

日本語向けに、次の箇所をそれぞれ書き換える。

```bash
APP_TIMEZONE=UTC
# ↓ 書き換え
APP_TIMEZONE=Asia/Tokyo

APP_LOCALE=en
# ↓ 書き換え
APP_LOCALE=ja

APP_FAKER_LOCALE=en_US
# ↓ 書き換え
APP_FAKER_LOCALE=ja_JP
```

いちおう、次のコマンドで自動的に置換・上書きできる。

```bash
sed -i -e "s/APP_TIMEZONE=UTC/APP_TIMEZONE=Asia\/Tokyo/" -e "s/APP_LOCALE=en/APP_LOCALE=ja/" -e "s/APP_FAKER_LOCALE=en_US/APP_FAKER_LOCALE=ja_JP/" .env
```

---

## migration 実行

データベースの接続確認も兼ねて、最初の migration を実行。

```bash
php artisan migrate
```

エラーが出たらメッセージをよく読んで対処。

たいていはデータベースの設定ミス。

参考までに、ここで私が何度もやってしまったミスは次のとおり

- 既に使われているデータベースを登録していた
- `.env` ではなく `.env.example` の方を編集していた
