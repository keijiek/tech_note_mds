# WordPress テーマ作成に、できるだけ低い学習コストで Tailwindcss を導入する手順

## 参考

[https://tailwindcss.com/docs/installation](https://tailwindcss.com/docs/installation)

---

## 前提

- Bun.js がインストール済みであること。
- WSL(ubuntu) の bash か Windows の PowerShell。試したことは無いけど Mac でも同様にできるとは思う。コマンドプロンプトでも出来るんじゃないかとは思う。

未インストールの場合、[Bun.js の導入](#bunjs-の導入) を参照。

または、Node.js で代替できるが、Bun の方が導入難易度が低く、動作速度も速い。

---

## Tailwind 導入

開発するテーマファイルのルートで次を実行。

```bash
bun add -D tailwind
bun tailwindcss init -p
```

### assets ディレクトリ作成

```bash
mkdir -p assets/{tailwind,images,js,css}
```

自分のディレクトリ構成にあわせること。
php ではなく、あまり編集しないファイルは assets に入れておきたい。


### tailwind/input.css

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

### package.json

---

## Bun.js の導入

[公式](https://bun.sh/) のトップページに載っている。

指示されているコマンドを打ち、インストールのメッセージをよく読んで従う。

### Windows の場合

```bash
powershell -c "irm bun.sh/install.ps1 | iex"
```

その後、シェルを再起動。

### Linux / Mac の場合

```bash
curl -fsSL https://bun.sh/install | bash
```
