# コマンドラインから .gitignore を自動生成 (gitignore.io)

[gitignore.io](https://www.gitignore.io/) は `.gitignore` ファイルを生成するサービス。

入力欄に、OSやエディターやフレームワークなど(`VisualStudioCode`,`react`,`Windows`等)を入力すると、ふさわしい .gitignore の文字列を得られる。

コマンドラインでも同じことが可能。

---

## 目次

- [コマンドラインを利用する方法](#コマンドラインを利用する方法)
- [ウェブページ上のサービスを利用する方法](#ウェブページ上のサービスを利用する方法)

---

## コマンドラインを利用する方法

- [コマンドラインのドキュメント](https://docs.gitignore.io/install/command-line)

### インストール方法

git コマンドのコンフィグにエイリアスを付加する方法を示す。

```bash
git config --global alias.ignore '!gi() { curl -sL https://www.toptal.com/developers/gitignore/api/$@ ;}; gi'
```

その上で、次の様に使用。複数のキーワードはカンマで区切る。半角スペースは使わず詰める。

```bash
# 標準出力
git ignore VisualStudioCode,react,windows,Node

# .gitignore ファイルへリダイレクト(上書き)
git ignore VisualStudioCode,react,windows,Node > .gitignore

# .gitignore 末尾へ追記する場合
git ignore VisualStudioCode,react,windows,Node >> .gitignore
```

なお、無効なキーワードを与えた場合などのエラーメッセージも、出力された文字列に含まれるので、よく読むこと。

---

## ウェブページ上のサービスを利用する方法

次のページ。

[https://www.gitignore.io/](https://www.gitignore.io/)
