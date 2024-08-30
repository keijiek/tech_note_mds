# npm コマンド

よく使うものだけ。

## node プロジェクト開始(package.json 生成)

現在のディレクトリに package.json を作る。

`-y` を付けると、生成時の対話にすべて yes (あるいは何も入力せずエンター)と回答した package.json を作る。
ここでの入力値にあまり意味はないので `npm init -y` だけで O.K.

```bash
npm init -y
npm init
```

## package.json に記されたパッケージをインストール

`npm install` のエイリアスのうち `npm i` が多用される。

```bash
# devDependencies も dependencies もインストール(開発環境用)
npm i
npm install

# dependencies だけをインストール(本番環境用)
npm i --production
npm install --production
```

## npm install <package_name>

- `-D` を付けてインストールしたパッケージは、package.json の `devDependencies` に登録される。開発環境でしか使わないもの。
- それなしでインストールされたパッケージは、package.json の `dependencies` に登録される。本番で使われるもの。

```bash
npm i -D vite 
# vite.js を devDependencies としてインストール。

npm i swiper
# swiper.js (カルーセルのライブラリ)を dependencies としてインストール
```

※ 大抵は、何らかのツール(vite, webpack等)を使い、1つの本番用JSファイルを開発時にビルド・バンドルし、ページ表示時にそれをブラウザに渡すので、ぜんぶ `npm i -D` でインストールすればいいように思う。そうする場合、本番用の `npm i --production` も不要か。

## npm uninstall <package_name>

```bash
npm uninstall
npm remove
npm rm
npm r
npm unlink
npm un
```

## インストールされているパッケージ一覧

```bash
npm ls
npm list

# -g でグローバルインストールの一覧
npm ls -g
npm list -g
```

## パッケージのアップデート

`npm outdated` で、インストールバージョンと現行バージョンを比較し、更新できるものを一覧表示。  
ただし、 `package.json` の記法に制限され、通常はマイナーバージョンのみの更新。  

例：`"vite": "^5.3.2"` とあれば vite は 6.0.0 以上に更新されない。

```bash
# 更新の余地を確認
npm outdated
```

package.json の記法に制限された範囲でバージョン更新。  
ただし、package.json は書き変わらず、あくまでその package.json の範囲での更新となる。

```bash
# 更新
npm update
```

※基本機能だけで最新バージョンへ更新するには、outdated で表示されたパッケージをアンインストールし、もういちど新規インストールする、という方法がある。

※ `npm-check-updates` を使うことで、package.json のバージョン記述を更新できる。らしい。

[https://www.npmjs.com/package/npm-check-updates](https://www.npmjs.com/package/npm-check-updates)
