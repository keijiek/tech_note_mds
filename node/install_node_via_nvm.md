# nvm で Node をインストール

## 1. 前置き

### 1.1. NodeJS のインストールにはバージョン管理ツールを使う

どのOSを使うにせよ、NodeJS インストール手順を抽象化すると次のようになる。

1. まず、Node用の「バージョン管理ツール」をインストール
1. 次に、「バージョン管理ツール」を用いて NodeJS をインストール

NodeJS を導入したいなら、いくつか存在するNode用バージョン管理ツールのうち、どれかを選んで用いるのが望ましい。

### 1.2. Node用バージョン管理ツール、nvm

いくつか存在するバージョン管理ツールのうち、もっとも高い人気を誇る `nvm`(Node Version Manager) を用いる。今回は、記事執筆時点で最新の v0.39.1 を使用。

次に挙げるwebページが`nvm`のリポジトリで、インストール方法をはじめ、詳しい使用法など、必要な情報は全て載っているはず。本稿は、そのページのドキュメントから、私が必要な部分を選んで書き直しただけのものだ。
[nvm: Node Version Manager (https://github.com/nvm-sh/nvm)](https://github.com/nvm-sh/nvm)

### 1.3. 用いる環境

次の様な状態の WSL2(Ubuntu) にインストールする。

powershell から。

```bash
wsl --status

# 出力
既定の配布: Ubuntu
既定のバージョン: 2
Linux 用 Windows サブシステムの最終更新日: 2021/11/30
WSL の自動更新が有効になっています。
カーネル バージョン: 5.10.60.1
```

ubuntu から。

```bash
cat /etc/issue

# 出力
Ubuntu 20.04.3 LTS \n \l
```

---

## 2. nvm インストール

### 2.1. cURL(curl) の存在確認、無ければインストール

curl コマンドを利用するので、まずコマンドの存在を確認し、無ければインストールする。

```bash
command -v curl  # curl のパスを確認

# 出力
/usr/bin/curl      # 存在するなら左記のようにコマンドのパスが出力される。
                   # 何も出力されなければ下記の様にインストール
```

```bash
sudo apt install curl  # もし存在しなかったらインストール
```

### 2.2. スクリプトによるインストール(アップデート)

[GitHub にある nvm のリポジトリ](https://github.com/nvm-sh/nvm)の、[Installing and Updating](https://github.com/nvm-sh/nvm#installing-and-updating)という項目の一番最初には、`install.sh`(インストール・スクリプト)によるインストール方法が説明されている。
ちなみにアップデートも同じ方法で可能。

具体的には次の様なコマンドでスクリプトを実行する。ただし、バージョンを表す文字列部分(ここではv0.39.1)が、バージョンアップに合わせて変わっていくと思われるので、最新のコマンドを知るには[リポジトリに示されているもの](https://github.com/nvm-sh/nvm#install--update-script)を確認する必要がある。

```bash
# インストールコマンド例
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```

### 2.3. nvm の存在確認

```bash
source ~/.bashrc # nvm インストールスクリプトが変更した .bashrc を再読み込み
command -v nvm   # nvm コマンドのパス確認。パスが出力されればOK
```

### 2.4. nvm のインストールで生じる変化

2.2における`install.sh`の実行により次の変化が生じている。

- `~/.nvm/`ディレクトリが作られ、そこに nvm リポジトリが clone される。なお、今後`nvm`を使ってインストールする NodeJS は、`~/.nvm/`の下にバージョン単位で別々にインストールされる。
- `~/.bashrc`の末尾に次の3行が追加される

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

ちなみに、3行目により、nvm の各種コマンド入力を tab で補完できるようになっている。

### 2.5. nvm のアンインストール

`nvm`のインストールによって生じた変化を取り消せば`nvm`をアンインストールできる。具体的には次の2点を行う:

1. `.nvm`ディレクトリの削除 (nvm でインストールされていた node がすべて消える)
2. `.bashrc`に追加された3行を削除

```bash
$ rm -rf "$NVM_DIR"  # $NVM_DIR は ~/.nvm のことであるはず
$ vim ~/.bashrc      # 下記の3行を消すため、.bashrc を編集
```

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```

## 3.0 nvm で NodeJS インストール

### 3.1. 前提知識(バージョンのエイリアス等)

#### 3.1.1. バージョンの指定方法とエイリアス

nvm における NodeJS のバージョンは、`16.13.0`のように数値で表すほか、次の様にエイリアスで表すこともできる。

```bash
node   # その時点での最新バージョンを表すエイリアス
lts/*  # その時点での LTS の最新バージョンを表すエイリアス
--lts  # nvm コマンドの引数にこれを使うと、上と同じく LTS の最新バージョンが指定される。
```

```bash
nvm alias  # バージョンのエイリアス一覧を表示
```

#### 3.1.2. バージョンの一覧表示

```bash
nvm ls         # すでにインストールされているバージョンの一覧
nvm ls-remote  # インストール可能な全バージョンの一覧
```


### 3.2. node のインストール



`nvm install <バージョン指定>`でインストール。

複数のバージョ

また、インストールされる node のバージョンにおける最新の`npm`が、そのバージョンに紐づけられた状態で自動的にインストールされる。

```bash
nvm install node    # 最新バージョンをインストール
nvm install --lts   # LTS の最新バージョンをインストール
nvm install 'lts/*' # 同上
nvm install 16.13.0 # 指定したバージョン(この場合はv16.13.0)をインストール
```

### 3.3. node のアンインストール

```bash
nvm uninstall 16.13.0 # 指定したバージョン(この場合はv16.13.0)をアンインストール
```
ただし、後述する「current バージョン」に指定されているバージョンは、uninstall できない。

## 4.0 nvm によるバージョン管理

### 4.1. current バージョンの確認

`nvm current`コマンドで現在使用中の node のバージョン(=current バージョン)が出力される。

```bash
nvm current  # current バージョン(いま使用中であるnodeのバージョン)の確認
# 出力例
v16.13.0

node -v      # node の方からも確認
# 出力例
v16.13.0
```

current バージョンを変えるには `nvm use` コマンドを使う。
また、`.nvmrc`ファイルと組み合わせれば、プロジェクト単位のバージョン管理を便利に行うことができる。
以下に詳述。

### 4.2. nvm use コマンドによるバージョン切り替え

`nvm use <バージョン指定>` で、インストール済みバージョンの中から新たな current バージョンを指定する。  
`npm`のバージョンも、current バージョンに紐づけられたものに自動的に切り替わる。

```bash
nvm use node    # 最新バージョンを current バージョンにする
nvm use --lts   # 最新の LTS を current バージョンにする
nvm use 16.13.0 # 指定したバージョン(この場合はv16.13.0)を current バージョンにする
```

### 4.3. nvm install による自動的なバージョン切り替え

なお、`nvm install`を行うと、インストール終了時、今インストールしたバージョンが current に指定される。

```bash:ubuntu; nvm install による自動的なバージョン切り替え
nvm install 16.13.1                # v16.13.1 をインストールすると...

# 出力例
Downloading and installing node v16.13.1...
Downloading https://nodejs.org/dist/v16.13.1/node-v16.13.1-linux-x64.tar.xz...
#################################(略)################################### 100.0%
Computing checksum with sha256sum
Checksums matched!
Now using node v16.13.1 (npm v8.1.2)  # v16.13.1 が自動的に current になる。
```

### 4.4. .nvmrc を使ったプロジェクトごとのバージョン管理

とあるディレクトリに`.nvmrc`というファイルを置き、それに node の特定のバージョンを記述しておく。
そうした時、そのディレクトリで`nvm install`や`nvm use`をバージョン指定なしで使うと、`.nvmrc`の記述を参照して自動的にコマンドの対象となるバージョンが指定される。

この仕組みを利用することで、特定バージョンを記述した`.nvmrc`をプロジェクトルートに置いておけば、プロジェクト単位でのバージョン統一が簡単になる。

#### 手順1 .nvmrc にバージョンを記述

次のようにして特定のバージョンが記述された`.nvmrc`ファイルを新規作成、または上書きできる。上述のエイリアスも使える。

```bash
echo "16.13.0" > .nvmrc   # 16.13.0 と書かれた.nvmrcを作成, 上書きも同様
cat .nvmrc                # 確認
# 出力例
16.13.0
```

#### 手順2 .nvmrc の記述を参照したバージョン切り替え

`nvm install`や`nvm use`をバージョン指定なしで使うと、今いるディレクトリで`.nvmrc`の自動探索が行われる。
もし存在すれば、それに記述された文字列を対象バージョンとしてコマンドが実行される。
ファイルが存在しなかったり、バージョンが不適切だったりすると、エラーになる。

```bash
nvm install  # .nvmrc に記述されたバージョンの node をインストール
nvm use      # .nvmrc に記述されたバージョンの node を current バージョンにする
```

### 4.5. 使用例: プロジェクトDir作成から npm init 開始まで

```bash
mkdir newProject && cd $_   # 新プロジェクト用ディレクトリを作り、入る。
echo "16.13.2" > .nvmrc     # このプロジェクトでは v16.13.2 を使うことにする。
nvm install                 # インストール済みか否かは気にせず install.

# 出力
Found '/home/(略)/newProject/.nvmrc' with version <16.13.2> # .nvmrc が参照された。
v16.13.2 is already installed.       # インストール済みでもエラーにならない。
Now using node v16.13.2 (npm v8.1.2) # 16.13.2 が自動で current バージョンに。
# 出力おわり

npm init -y                 # package.json を作り、開発環境づくりを始めることにする。
```
