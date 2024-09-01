# 認証用に breeze 導入

```bash
# breeze のインストーラのようなものを導入
composer require laravel/breeze --dev

# インストール
php artisan breeze:install

# 選択肢は、今回はすべてデフォルトで。
# alpine + blade
# darkmode : no
# PHPUnit

# いちおう打っておく。おそらくインストール中に実行済みだが。
php artisan migrate
bun i
```

今後は、次の様に確認用サーバーを起動し、開発を進める。

```bash
# フロント側のリソースを持たせるサーバーを起動。閲覧しなくてよい。
bun run dev

# laravel アプリが動くサーバーを起動。こちらを確認のために閲覧する。
php artisan serve
```

[http://127.0.0.1:8000/](http://127.0.0.1:8000/) をブラウザで見て、アカウント作成とログインを試す。
