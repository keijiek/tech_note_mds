# linux コマンド

## du

```bash
du -hd2 . | sort -h
du -hd2 . | sort -hr
```

---

## command / which

コマンドのパスを知りたい時。

```bash
command -V curl
which curl
```

command はシェル組み込みのコマンドで、which は外部のコマンド。  
command はヒットするものが無いとき何も出力しないが、which はエラーを出力する。
command は、alias なら alias の設定を出力。

### which

```bash
# a オプション
which -a curl

# 該当しうる全てのパスが表示される。使用されているのは一番上に表示されるもの。
/usr/bin/curl
/bin/curl
```

