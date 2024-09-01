# php, composer, maradb, bun または node のインストール

Laravel での開発に備え、開発環境に次のものをインストールする。

なお、OSは ubuntu 、ウェブサーバーは apache2 を想定している。OS が違う人はパッケージマネージャーの部分などを読み替えて。

- **php** : laravel11 は php8.2 以上が必要。
- **phpモジュール** : 必要モジュールが揃っていないと正常に動かない。エラーで不足を指摘されたら必ずインストール
- **composer** : php のパッケージマネージャ。laravel のインストールをはじめ、プロジェクトに必要なものをインストールするのに使用。複数の開発環境間でパッケージを統一したり、本番環境用のパッケージ群をコマンド一つでインストールしたり、できる。
- **mariadb** : データベースサーバー。laravel11 は主要なデータベースには対応しているので何でもよいが、今回は x-server レンタルサーバーへのデプロイを目指すので、同じ mariadb にした。
- **node/bun** : JavaScript のランタイム。Laravel を使おうが使うまいが、フロント側の開発に必須。両方入れておけばよいと思うが、片方を選ぶなら node 。JS 向けのパッケージ管理用コマンドも付いており(node は`npm`, bun は `bun`)、言語は異なるが上記の composer と使い方が似ている。

---

## OS とウェブサーバー

OS は wsl の ubuntu24 、ウェブサーバーは apache2 を想定している。

マイクロソフトストアから wsl 用の ubuntu をインストールすれば、両方とも簡単に手に入るはず。

### ubuntu を最新状態に

この後に続く作業の前に、次のコマンドを打って最新状態にしておく。  

```bash
sudo apt update && sudo apt upgrade -y
```

---

### mariadb, php, composer インストール

存在チェック

```bash
mysql -V
php -v
composer -V
```

インストールされていないものだけをインストール。OS が違う人(ubuntuではない人)は、その OS のインストール方法に読み替えて。

```bash
sudo apt install mariadb-server
sudo apt install php
sudo apt install composer
```

あらためて確認

```bash
mysql -V
php -v
composer -V
```

---

## php のモジュール

Laravel のインストール中、php モジュールが足りないことによるエラーが出る可能性がある。
その場合、入っている php モジュールの確認や、パッケージマネージャ(apt)による検索機能を利用しながら、不足する php モジュールをインストールしていく。

一例として、私の環境で要求されたモジュールは次。

- mbstring
- mysql
- xml

---

### 入っているモジュールの確認

いま入っている php 用のモジュールは次のコマンドで確認できる。

```bash
php -m
```

また、php に関する詳しい情報は次で確認できる。長いので、とくに見たい情報が決まっているなら grep

```bash
php --ini

# grep 例
php --init | grep 8.3
```

---

### apt search でモジュールを探す

エラーで要求されたモジュール名などを使って、モジュールが存在するかどうか検索する。  
検索ワードや検索方法は、おのおの頭を使う。

このとき、行末に `[installed]` などと表示されるものはインストール済み

```bash
# 検索例
apt search php-
apt search mbstring

# 複数ワードでも検索できる。半角スペースで区切る
apt search php- mbstring
```

---

### apt show で特定モジュールを詳しく調べる

上記の検索で見つかったモジュールを、念のため詳しく調べる。  
間違って変なものをインストールしたくないので。

```bash
# 例。正式なモジュール名は、頭に "php-" と付くのが通例である模様。
apt show php-mbstring
apt show php-mysql
apt show php-xml
```

---

### sudo apt install でインストール

インストールするものが決まったら、次のコマンドで

```bash
# 例。複数ある場合、半角スペースで区切る
sudo apt install php-mbstring php-mysql php-xml
```

---

## JavaScript のランタイム(Node.js)をインストール

Laravel でフロント側(ブラウザから見るページ)を作るとき、***vite.js*** を中心としたフロントエンド開発の技術が使われる。  
それらを動かすには、JavaScript のランタイムを導入する必要がある。なお、JS用のパッケージ管理ツールも自動で付いてくる。

Laravel 用の composer パッケージをインストールするとき、そのパッケージのインストーラが npm を使う時があるようだ。よって、 `Node.js` は導入必須と思われる。breeze の Blade + Alpine オプションを選んだ時、そうなった。

Node.js のインストールにはバージョン管理ツールの使用が常なので、次の2つのうち、どちらかの方法で導入しておく。

- [nvm で node をインストール](../node/install_node_via_nvm.md)
- [volta で node をインストール](../node/install_node_via_volta.md)

この後は余談だが、JSのランタイムには、後発の Deno や Bun がある。とくに Bun.js は、Node.js と同様に node_modules を使えるため、Node.js からスムーズに乗り換えられるし、しかも圧倒的に処理が早い。近ごろは私も Bun.js を使うようになった。

Laravel の開発中、npm と bun は特に問題なく並存できている。フロント側のパッケージに関わる事を自動処理するときに Laravel が npm を勝手に呼び出すが、それは新しい composer のパッケージを入れる時などで、頻度はきわめて低い。Laravel から npm で何かをインストールされるたび、 `bun i` を実行しなおして `bun.lockb` にも変更を反映させることで対処している。

Bun.js の導入方法は次。

- [Bun.js をインストール](../node/install_bun.md)
