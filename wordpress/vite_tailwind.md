## インストール

```bash
bun add -D vite tailwindcss postcss autoprefixer @tailwindcss/typography
```

daisyui を入れる場合

```bash
bun add -D daisyui@latest
```

その他のフロント用ライブラリを併用する例を示すために入れる。

```bash
bun add -D swiper swup
```

## package.json に追加

下記の様に、`scripts` と `type` オブジェクトを追加。

```json
{
  "scripts": {
    "tw": "vite build --mode css --watch",
    "js": "vite build --mode js --watch"
  },
  "type": "module",
  "dependencies": {},
  "devDependencies": {
    "@tailwindcss/typography": "^0.5.15",
    "autoprefixer": "^10.4.20",
    "postcss": "^8.4.47",
    "swiper": "^11.1.14",
    "swup": "^4.8.1",
    "tailwindcss": "^3.4.13",
    "vite": "^5.4.8"
  }
}
```

## tailwindcss init -p

```bash
bunx tailwindcss init -p
```

### tailwind.config.js

- `content` の配列には、監視対象のファイルを glob パターンで指定。
- `plugins` の配列には、使うプラグインを要素に追加。

```js
/** @type {import('tailwindcss').Config} */
export default {
  content: ["./templates/**/*.php"],
  theme: {
    extend: {},
  },
  plugins: [require("@tailwindcss/typography"), require("daisyui")],
};
```

## directories and files

```bash
echo -e "@tailwind base;\n@tailwind components;\n@tailwind utilities;" > ./assets/src/tailwindcss/style.css
echo -e "import"
```

## vite.config.js

tailwind と js を別個にビルドする仕組みに。

```js
import { defineConfig } from "vite";
import path from "path";

export default (mode) => {};
```

## index.js

```js
// import Swiper bundle with all modules installed
import Swiper from "swiper/bundle";

// import styles bundle
import "swiper/css/bundle";

//import swup
import Swup from "swup";

// entry point
document.addEventListener("DOMContentLoaded", () => {
  const swiper = new Swiper(".mainvisuals_carousel0", {});
  const swup = new Swup();
});
```
