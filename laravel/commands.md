
# migration

## migration 実行

migration ファイルを作って編集したら、次を実行してデータベースに反映させる

```bash
php artisan migrate
```

## migration 状態確認

```bash
php artisan migrate:status
```

---

## モデルと migration ファイルを作成

```bash
php artisan make:model Post -m
```

- `-m` : migrationふぁいるを作成

---

## 既存テーブルを変更を加える場合

`--table` に既存テーブル名を入力

`add_user_id_column_to_posts_table`は任意だが、施す変更内容が分かる名前を。

```bash
php artisan make:migration add_user_id_column_to_posts_table --table=posts

# 出力
INFO  Migration [database/migrations/2024_09_04_073522_add_user_id_column_to_posts_table.php] created successfully.
```

作られたphpファイルを開いて編集

```php

```
