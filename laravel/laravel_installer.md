# laravel インストーラーのインストール

updated at : 2024-08-30

## 前提

```bash
composer -V

# 出力
Composer version 2.7.1 2024-02-09 15:26:28
```

## laravel インストーラの導入

次のコマンドを打つ。  

```bash
composer global require laravel/installer
```

`~/.config/composer/vendor/laravel/installer/bin/` に laravel インストーラがインストールされる。

古いバージョンの `composer` では `~/.composer/vendor/bin/` にインストールされたようで、それを前提とした記事もたくさんあるため注意。

## laravel インストーラに PATH を通す

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

## laravel インストーラを動かす

今から作る新規プロジェクトの親となるディレクトリに移動し、次を実行。

```bash
laravel new hoge_project
```

対話型のインストールが始まる。
