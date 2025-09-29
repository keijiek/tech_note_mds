# Wordpress サイトをコピーする手順 (同じサーバー内)

エックスサーバー社のレンタルサーバー上にて、wordpress サイトのコピーを作成する手順。

大まかには次の手順：

1. ドメインの用意
2. DBの用意
3. Wordpress 本体のコピー
4. wp-config.php を編集
5. wp cli
6. もしあれば追加の設定変更(index.php や .htacceess や .user.ini)
7. ブラウザで閲覧、管理画面にログイン

---

## 1. ドメインの用意(サーバーコンパネ)

1. 新ドメインを作る。取得していなくてよい。
2. 動作確認URLを設定。未取得ドメインの場合。

---

## 2. DBの用意(サーバーコンパネとphpMyAdmin)

1. 空のデータベース(DB)を新規作成 (管理画面にて)
2. 1のDBに対する全権限を持つユーザーを設定 (管理画面にて)
3. コピー元サイト用DBからsqlファイルをエクスポート (phpMyAdminにて)
4. 1の空DBに、2のsqlファイルをインポート(phpMyAdminにて)

---

## 3. Wordpress 本体のコピー (対象ホストにssh接続してから)

- コピー元に無いファイルは、コピー先では削除される。コピーというより同期をとる機能。
- exclude 引数で同期の対象から外す。`--exclude='/.*'` を指定すれば、ルート直下の `.` から始まるファイル(例えば `.user.ini` や `.htaccess` など)は同期をとらず、既存のそれらのファイルを保護することができる。複数を指定する場合、`--exclude=''`を複数設定する。

```bash
# 文法
rsync -av --exclude='除外するファイルパスのパターン' コピー元ディレクトリのフルパス コピー先ディレクトリのフルパス

# 例  : ホームの/hoge.com/public_html/ を、ホームの/fuga.com/public_html/ にコピー。その際、「.」で始まるファイルを除外。
rsync -av --exclude='/.*' ~/hoge.com/public_html/ ~/fuga.com/public_html/

# 例2 : index.html も除外する場合
rsync -av --exclude='.*' --exclude='index.html' ~/hoge.com/public_html/ ~/fuga.com/public_html/
```

---

## 4. wp-config.php を編集

**!! 本体のコピー後は、直ちにこの手順を踏み、データベース接続の設定を変更しなければならない。さもなければ、この後のURL文字列置換で、コピー元のデータベースを変更してしまう !!**

### 1. カレントディレクトリを、操作対象である wordpress 本体のルートに変更

```bash
cd コピー先のWordPress本体のルートのパス
```

「WordPress本体のルートのパス」とは、`wp-config.php`や`wp-content`や`wp-include`などが存在するディレクトリのパス。

### 2. 【重要】 wp-config.php で参照データベースを変更

権限が`400`であることが多いので、確認し、書き込み権限を追加して `600` にしておく。

```bash
# 権限の確認
ll ./wp-config.php

# 出力例(権限400, user の read のみ許可)
-r-------- 1 user_name group_name 4098 Sep 26 11:41 wp-config.php

# 出力例(権限600, user の read と write を許可)
-rw------- 1 user_name group_name 4098 Sep 26 11:41 wp-config.php

# 権限変更(400なら)
chmod 600 ./wp-config.php

# vi で開く
vi ./wp-config.php
```

```bash
chmod 600 ./wp-config.php
vi ./wp-config.php

# 編集後は権限を400に
chmod 400 ./wp-config.php
```

### 3. vi で wp-config.php を編集

#### やること

- `DB_NAME` の値を、手順2で用意した新しいデータベース名に変更。
- `DB_USER`と`DB_PASSWORD`を、上記データベースに権限を持つユーザーのものに変更。

#### vi の操作

- `/` :検索。`/DB_` などと入力すれば`DB_`の文字列を検索。そのとき `n` キーで次のヒット対象へカーソル移動。
- `i` : 対象を見つけたら`i`で編集モードへ移行し、書き換え。
- `(esc キー)` : 編集モードを抜ける。
- `:wq (enter)` : 保存して終了
- `q! (enter)` : 保存せず終了

---

## 5. wp cli でドメイン文字列の置換

### 1. 【重要】これから操作する wordpress サイトが正しい対象であるかを確認

```bash
wp config list
```

- 特に、データベース設定(DB_*)が、コピー先のものになっているかを確認。
- コピー元のDBが参照されていると、この後の工程で、コピー元が変更されてしまうので注意。
- その場合、`wp-config.php` の変更に戻り、`DB_NAME` の値を書き換えること。


### 2. 置換

- 置換文字列を指定するとき、URL末尾の `/` は書かないこと。 `/` を付けると `/` も込みで置換が実行され、不具合のもとになる。
- `http` or `https`、`www`付き or 無し、の、計4通りの置換を試すとよい。
- まずは `--dry-run` 引数を付け、実際の置換を行わせず、データベース上のどのテーブルで何件の置換が起こるかだけを確認する。
- 確認後、`--dry-run` を外して実行する。
- 動作確認URLを使う場合、未取得のドメイン名ではなく、動作確認URLに置換しなければならない。

```bash
# 文法：置換件数の確認
wp search-replace --dry-run '置換元のURL文字列' '置換後のURL文字列'
# 文法：実行
wp search-replace '置換元のURL文字列' '置換後のURL文字列'

# 例：確認
wp search-replace --dry-run 'https://hoge.com' 'https://fuga.com'
wp search-replace --dry-run 'https://www.hoge.com' 'https://fuga.com'
wp search-replace --dry-run 'http://hoge.com' 'https://fuga.com'
wp search-replace --dry-run 'http://www.hoge.com' 'https://fuga.com'

# 例：実行
wp search-replace 'https://hoge.com' 'https://fuga.com'
wp search-replace 'https://www.hoge.com' 'https://fuga.com'
wp search-replace 'http://hoge.com' 'https://fuga.com'
wp search-replace 'http://www.hoge.com' 'https://fuga.com'
```


### wp コマンドのエイリアス

`wp`は、php(cli) で実行される必要があり、正式には `phpのフルパス wpのフルパス` の形で書く。例えば `/usr/bin/php7.4 /usr/bin/wp` のようになる。コマンドの場所は環境によって異なる。

エックスサーバーのレンタルサーバーの場合、[https://www.xserver.ne.jp/manual/man_program_soft.php](https://www.xserver.ne.jp/manual/man_program_soft.php)にphpのパスがリスト化されている。このケースでは cli 版を用いる。

エイリアスを定義しておくことで、`/usr/bin/php7.4 /usr/bin/wp` などと入力しなければならないところが `wp` だけで済むようになる。

エイリアスは、カレントユーザーの`.bashrc`に定義する。

例えば、今の `.bashrc` を次のように確認する。

```bash
cat ~/.bashrc | grep alias

# 出力に次のような一行の有無を確認
alias wp='/usr/bin/php7.4 /usr/bin/wp'
```

`alias wp='/usr/bin/php7.4 /usr/bin/wp'`の一行により、`wp`がエイリアスとして定義されている。

php のバージョンは任意。2025-09-26現在、7系が最も問題無さそうに思える。8でも動くがnoticeやwarningが出やすい。

wp の alias が設定されていない場合、`.bashrc` を編集して追加。

```bash
vi ~/.bashrc
```

編集後、`.bashrc`を再読み込み

```bash
source ~/.bashrc
```

---

## 6. もしあれば追加の設定変更(index.php や .htacceess や .user.ini)

例えば、エックスサーバーのレンタルサーバーで、wordpress のインストール先を、 `public_html/wp_root/` などの一階層低いディレクトリにしている場合、次のように操作し、適切な `public_html/index.php` を作成する。

```bash
# カレントディレクトリが、上記例でいうところの public_html/wp_root/ である場合
cp index.php ../index.php

# public_html/index.php を編集
vi ../index.php
```

変更箇所は次のとおり。

```php
// この行を...
require __DIR__ . '/wp-blog-header.php';

// ↓↓↓

// このように変更。wp_root は実際のディレクトリ名に合わせる。
require __DIR__ . '/wp_root/wp-blog-header.php';
```

---

## 7. 管理画面にログイン

最低でも次のことを行っておく

- 「設定」->「表示」とたどり、「検索エンジンがサイトをインデックスしないようにする」にチェックを入れる
- サイト名の変更(この後の作業でコピー元とコピー先を間違えないよう)