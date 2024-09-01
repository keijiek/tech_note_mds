# データベースの準備 | Laravel11 インストール

## この章で用意するもの

- このアプリ専用に`データベース`を新規作成
- `ユーザー`を`パスワード`付きで作成し、上記`データベース`への全権限を付与
- データベースサーバーの`ポート番号`を確認

Laravel を開発環境にインストールする前、あるいは、本番へデプロイする前に行う。

Laravel をインストールした後、 `.env` ファイルのデータベースに関する項目に、上記の情報を書き込むことになるので、何らかの方法で覚えておく。

---

## データベースサーバーでの操作

下記は、OS が WSL の`ubuntu` 、データベースサーバが `mariadb` である場合を想定した操作の例となる。

```bash
# mysql の root でログイン
mysql -u root -p correct_passward

# パスワードを忘れた...、そんなあなたはこのコマンド
sudo mysql -u root
```

データベースサーバで下記を実行し、必要なデータを安全な場所にひかえておく。

### 作業例1

```sql
-- new_db_name というデータベースを作成。(同名データベースが存在すればエラー出力)
CREATE DATABASE new_db_name;

-- localhost に new_user_name というユーザーを新規作成。パスワードは new_password
CREATE USER 'new_user_name'@'localhost' IDENTIFIED BY 'new_password';

-- 上記で作ったデータベースに対する全権限を、上記で作ったユーザーに与える。
GRANT ALL ON new_db_name.* TO new_user_name@localhost

-- ポート番号の確認
SHOW VARIABLES LIKE 'port';
```

### 作業例2

Laravel の学習過程で、なんども DB を作って user に権限を与え、そのたびに設定(`.env`)を書き換える...という作業が面倒な方へ。

次のSQL文なら、(もしあれば)既存 DB の new_db_name を削除したうえで、新たに new_db_name を作ることができる。

```sql
CREATE OR REPLACE DATABASE new_db_name;
```

このとき、削除された new_db_name に対する権限を持つユーザーは、新しい new_db_name に対しても権限を持っていると見なされる。

これなら設定の書き換えもいらない。

学習時や開発時はこれでよいかも。


### 関連SQL文

この作業中に使う可能性が高いSQL文は下記。

```sql
-- データベースの一覧表示
SHOW DATABASES;

-- データベース削除
DROP DATABASE IF EXISTS target_db_name;

-- ユーザーの一覧表示
SELECT user,host FROM mysql.user;

-- ユーザー削除
DROP USER IF EXISTS user1@localhost;
DROP USER IF EXISTS user1@localhost, user2@localhost, user3@localhost;

-- ユーザーの権限を確認
SHOW GRANTS FOR user_name@localhost;

-- 権限削除(DBを消した場合など)
REVOKE ALL PRIVILEGES ON any_db_name.* FROM user_name@localhost;

-- パスワード変更(パスワードを忘れた場合など)
ALTER USER user_name@localhost IDENTIFIED BY 'new_password';
```

※注意:

- 作った database は user (password) と一緒に Laravel アプリに完全に委ねることになる。自分で database を操作してはいけない。

※手抜き

- ローカルの開発環境に限り、ユーザーは使いまわしでいいかも。新規DBの権限だけ与える。
