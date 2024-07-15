# vite + tailwindcss + ペライチhtml

vite の設定の練習を兼ねて。

## vite プロジェクト作成、初期設定

```shell
npm create vite@latest

# プロジェクトに移動し、package.json に書かれたパッケージをインストール
cd つくったプロジェクトディレクトリ
npm i

# tailwind に必要なものを導入し、コンフィグファイルを生成
# postcss は vite に入っているので不要。
npm i -D tailwindcss autoprefixer
npx tailwindcss init
touch vite.config.js
```

## tailwind.config.js の編集

`content` 配列の要素に `index.html` を追加

```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: ['index.html'],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

## vite.config.js の編集

```javascript
import { defineConfig } from 'vite'
import path from 'path';
import tailwindcss from 'tailwindcss';
import autoprefixer from 'autoprefixer';

export default defineConfig({
  css: {
    postcss: {
      plugins: [
        tailwindcss,
        autoprefixer
      ]
    }
  }
});
```

もし vite.config.**ts** を使うなら、`@types/node`も入れておく。
`path` が見つからない、と言われてしまう。

```bash
npm i -D @types/node
```

## src/ 内の編集

いったん src/ 内のファイルを全削除。
main.ts と style.css を再作成。

```bash
rm ./src/*
echo import \'./style.css\' > ./src/main.ts
echo -e "@tailwind base;\n@tailwind components;\n@tailwind utilities;" > ./src/style.css
```

### src/main.ts 確認

```typescript
import './style.css'
```

### src/style.css の確認

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

## index.html の編集

内容は何でもよい。

最低一つは tailwind css のクラスを使っておくとよい。
tailwind が効いているか確認できるし、`npm run dev`したとき、utility classes がひとつもありません、と怒られずにすむ。

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite + TS</title>
    <script type="module" src="/src/main.ts"></script>
  </head>
  <body>
      <div class="container mx-auto">
        <h1 class="text-red-900">見出し</h1>
        <p>本文</p>
      </div>
  </body>
</html>
```

## 開発開始

```bash
npm run dev
```

以上。
