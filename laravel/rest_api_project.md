# Laravel で rest api を作る試み

## 前提

- 必要な php モジュールは導入済み
- node か bun が導入済み
- mariadb を使用

## 初期設定

### データベースの用意

```bash
# mariadb (sqlサーバー)にログイン
sudo mysql -u root
```

sqlサーバーにログイン後、下記。
データベース名、ユーザー名、パスワード、は何らかの方法で覚えておく。後で `.env`ファイルの設定値に使う。

```sql
-- ユーザーの新規作成
create user 'new_user_name'@'localhost' identified by 'anypassword';

-- データベースの新規作成
create database new_db_name;

-- 上記で作ったデータベースに対する全権限を、上記のユーザーに与える。
grant all on new_db_name.* to new_user_name@localhost
```

この作業中に関連してよく使うSQL文。

```sql
-- データベースの一覧表示
show databases;

-- ユーザーの一覧表示
select user,host from mysql.user;

-- 権限確認
show grants for usre@localhost;

-- パスワード変更(パスワードを忘れた場合)
set password for existing_user_name@localhost = 'new_password';
```

---

### laravel 11 のインストール

```bash
composer create-project laravel/laravel new_project_name

# インストール後
cd new_project_name

npm i
# または
bun i
```

### .env

`.env`ファイルの3つの設定項目を書き換える

```bash
APP_TIMEZONE=UTC
# ↓
APP_TIMEZONE=Asia/Tokyo

APP_LOCALE=en
# ↓
APP_LOCALE=ja

APP_FAKER_LOCALE=en_US
# ↓
APP_FAKER_LOCALE=ja_JP
```

### データベース接続

`.env`の下記の設定を書き換え。

```bash
DB_CONNECTION=mysql # 変更
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=データベース名 # 上記で新規作成したデータベース名
DB_USERNAME=ユーザー名 # 上記DBへの権限を与えたユーザー名。@localhost はいらない。
DB_PASSWORD=パスワード # 上記ユーザーのパスワード
```

その後、下記を実行。

```bash
php artisan migrate
```

私がここでエラーが出した時は、php モジュールが足りない、または、データベースの設定が正しくない、という原因があった。

---

## 必要なものをインストール

### Fortify

```bash
composer require laravel/fortify
php artisan fortify:install
php artisan migrate
```
