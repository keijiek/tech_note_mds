# Volta で Node をインストール

updated_at : 2024-08-30

Node は、バージョン管理ツールを使ってインストールする。  
バージョン管理ツールとして `volta` を使う例を書き残す。

## Volta を導入

[Volta 公式](https://volta.sh/)を参照。

### Linux / macOS

下記コマンドでインストール。アップグレードも兼ねる。  
変更される可能性もあるので、[Volta 公式サイト](https://volta.sh/)のインストールコマンドを確認すること

```bash
curl https://get.volta.sh | bash
```

インストール後、volta へ PATH を通す設定が bashrc に追加されるので、次のコマンドで再読み込み。  
インストール前(アップグレード前)の時点ですでにPATHが通っていた場合は不要。

```bash
source ~/.bashrc
```

その後、path が通ったか確認。`volta　-v`でバージョン出力。`which`コマンドで、対象コマンドのフルパスを出力

```bash
volta -v
which volta
```

---

### Windows

Windows 版もある。[Volta 公式サイト](https://volta.sh/) で推奨されている `winget` によるインストール方法を示す。

```bash
# インストール
winget install Volta.Volta

# アップグレード
winget upgrade Volta.Volta
```

※インストール時に設定されたパスを反映するために、インストール後は***シェルを再起動***

バージョンやパスを確認

```bash
volta -v
gcm volta | fl
```

---

## volta で node をインストール

LTS(長期サポート)の最新バージョンをインストールするには次のコマンド  
たいていこれでよい。

```bash
volta install node
```

バージョンを指定することもできる。

```bash
# メジャーバージョン指定(そのメジャーバージョンの最新版をインストール)
volta install node@20

# バージョン指定
volta install node@20.17.0
```

確認

```bash
node -v
```

## node をアンインストール

volta によってインストールされた node コマンドそのもののパスは `volta which node` でわかる。  
なお、 `which node` (windiws なら `gcm node`)で出力されるのはシンボリックリンクのパスなので注意。

```bash
volta which node

# 出力例
/home/hoge/.volta/tools/image/node/20.17.0/bin/node
```

出力が上記のとおりなら、下記のディレクトリに、これまでインストールしてきた node がバージョン単位で置かれている。

```bash
ls /home/hoge/.volta/tools/image/node

# 出力例
20.16.0  20.17.0
```

出力が上記の通りで、最新の 20.17.0 を使っていくので、不要となった 20.16.0 をアンインストールしたいとする。  
その場合、次のようにディレクトリごと削除することでアンインストールする。

```bash
rm -rf /home/hoge/.volta/tools/image/node/20.16.0
```

Windows (powershell) で上記の手順は次のようになる

```bash
# node のパス確認
volta which node

# 出力例
C:\Users\hoge\AppData\Local\Volta\tools\image\node\20.17.0\node.exe

# 出力例に従うなら、次のコマンドで、インストールされている node 一覧を確認
ls C:\Users\hoge\AppData\Local\Volta\tools\image\node\

# 不要なバージョンのフォルダを削除(20.16.0 を消したい場合)
rm -r -fo C:\Users\hoge\AppData\Local\Volta\tools\image\node\20.16.0
```

以上
