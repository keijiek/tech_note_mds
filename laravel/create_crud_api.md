# CRUD なアプリを作り、Read だけは web api のエンドポイントを作る試み

updated at : 2024-08-30

Laravel + Breeze + Livewire(Blade) + tailwindcss + 

## 前口上

レンタルサーバーへのデプロイを想定しているので php で動く必要があり、そのフレームワークとなると Laravel がデファクトスタンダードだから。登場年代が新しく軽量な Flight も面白そうではあったが、ルーティングはともかく認証まわりがよくわからなかったので、まず、情報の多い Laravel で学んでみようという意図もある。

作るものは、管理画面にログインしてCRUD操作ができるウェブアプリ。そのうえに、外部のウェブサイトが投稿データ(全件または個別)を fetch できるように api のエンドポイントも作る。慣れたら、CRUD 全部を外部サイトから行えるような api を作りたい。

---

## 開発環境の準備

### 1. SQL 側の準備

Laravel の `.env` へ設定を書き込むために、次のものを作成・確認しておく必要がある。

- このアプリ専用に新規作成した `database`
- その DB に対する全権限を与えられた `user` とその `password`
- データベースサーバーのポート番号

下記は、OS が WSL の`ubuntu` 、データベースサーバが `mariadb` を想定した操作の例。

```bash
# sudo を使て強引に root でデータベースサーバーにログイン。これなら root のパスワードを忘れてもダイジョウブ
sudo mysql -u root
```

下記を実行し、必要なデータを安全な場所にひかえておく。

```sql
-- データベースの新規作成
CREATE DATABASE new_db_name;

-- ユーザーの新規作成、(既存ユーザーを用いてもよい)
CREATE USER 'new_user_name'@'localhost' IDENTIFIED BY 'new_password';

-- 上記で作ったデータベースに対する全権限を、上記で作ったユーザー(又は既存ユーザー)に与える。
GRANT ALL ON new_db_name.* TO new_user_name@localhost

-- ポート番号の確認
SHOW VARIABLES LIKE 'port';
```

この作業中に関連してよく使うSQL文も載せておく。

```sql
-- データベースの一覧表示
SHOW DATABASES;

-- データベース削除
DROP DATABASE target_db_name;

-- ユーザーの一覧表示
SELECT user,host FROM mysql.user;

-- 権限確認
SHOW GRANTS FOR user@localhost;

-- 権限削除(DBを消した場合など)
REVOKE ALL PRIVILEGES ON any_db_name.* FROM user@localhost;

-- パスワード変更(パスワードを忘れた場合など)
ALTER USER user@localhost IDENTIFIED BY 'new_password';
```

※注意:

- 作った database は user (password) と一緒に Laravel アプリに完全に委ねる。自分で database を操作してはいけない。
- これは開発環境用なので、本番に移行する時は、あらためて本番用データベースを用意する必要があるので注意。


### 2. Laravel をインストール

[Laravel公式: Creating a Laravel Project](https://laravel.com/docs/11.x/installation#creating-a-laravel-project) が公式ドキュメント

方法は2つある。ここでは前者を用いる。

- `composer create-project` コマンドを使う(こちらを使う)
- `laravel` インストーラを使う

この後の composer による操作の前提として、まずバージョンを確認しておく。

```bash
# composer の確認(V は大文字)
composer -V

# 出力
Composer version 2.7.1 2024-02-09 15:26:28

# bun.js の確認(v は小文字)
bun -v

# 出力
1.1.26

# または node + npm
node -v
```


#### 2.1. composer create-project コマンド

`bun.js` のインストールは[bun.js 公式サイト](https://bun.sh/)ですぐ分かるので、入れておく。`npm`を使う人はそれで読み替える。


```bash
composer create-project laravel/laravel new_project_name

# インストール後の操作
# プロジェクトに移動
cd new_project_name

# バック側のパッケージを vendor にインストール
composer install

# フロント側のパッケージを node_modules にインストール
bun install
# または
npm install
```

基本的に、この方法でインストールしたことを前提に話をすすめる。

しかし、下記のインストーラを使う方法だと、認証用のライブラリ選択と、それに伴うフロント側技術の選択などの選択肢を知ることができるので、勉強になるので、紹介する。

#### 2.2. laravel インストーラを使う方法

laravel インストーラによるインストールは読み飛ばしてよい。  
第三章以降は、上記の `composer create-project` でインストールしたものとして作業を進める。

##### 2.2.1. laravel インストーラの導入

```bash
# インストーラを導入。
composer global require laravel/installer
```

composer のバージョンが上述である場合、`~/.config/composer/vendor/laravel/installer/bin/` に laravel インストーラがインストールされる。

かつては `~/.composer/vendor/bin/` にインストールされたようで、それを前提とした記事もたくさんあるため注意。

##### 2.2.2. laravel インストーラに PATH を通す

`~/.bashrc` の末尾あたりに次を追加。laravel インストーラが存在する bin ディレクトリを指定している。

```bash
# laravel installer path
export PATH=$HOME/.config/composer/vendor/laravel/installer/bin:$PATH
```

```bash
# 設定読み直し
source ~/.bashrc

# 確認。上記の設定に準じた laravel までのパスが表示されればOK
which laravel
```

##### 2.2.3. laravel インストーラを動かす

今から作る新規プロジェクトの親となるディレクトリに移動し、次を実行。

```bash
laravel new hoge_project
```

対話型のインストールが始まる。  
認証用ライブラリと、それに伴うフロント側の選択肢を知ることができるので勉強になるかも。


---

## 3. .env の編集

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

データベースの設定箇所を探して、控えておいたデータを書く。

```bash
DB_CONNECTION=sqlite
# DB_HOST=127.0.0.1
# DB_PORT=3306
# DB_DATABASE=laravel
# DB_USERNAME=root
# DB_PASSWORD=

# ↓ コメントインしてデータを入力

DB_CONNECTION=mysql # sqlite から変更
DB_HOST=127.0.0.1
DB_PORT=3306 # ポート番号。mariadb の場合大抵は3306
DB_DATABASE=new_db_name # 新しいデータベースの名前(空のデータベースであること)
DB_USERNAME=new_user_name # 上記データベースについて全権限を持つユーザー名
DB_PASSWORD=new_password # そのユーザーのパスワード
```

下記コマンドを実行し、laravel にデータベースを初期化させ、既存の migration ファイル群をもとにテーブルを編集させる。

```bash
php artisan migrate
```

※ここでエラーがでる場合、エラーメッセージをよく読んで欲しいが、多くの場合、.env の設定が間違っているか、空っぽではないデータベースを紐づけたか。

---

## 4. 認証用に breeze 導入

```bash
# breeze のインストーラのようなものを導入
composer require laravel/breeze --dev

# インストール
php artisan breeze:install

# いちおう打っておく。
php artisan migrate
npm i
```

```bash
# フロント側のリソースを持たせるライブサーバーを起動
bun run dev

# laravel アプリが動くサーバーを起動
php artisan serve
```

[http://127.0.0.1:8000/](http://127.0.0.1:8000/) をブラウザで見て、アカウント作成とログインを試す。

---

## 5. ルーティング
