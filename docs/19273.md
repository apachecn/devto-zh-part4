# 将 TailwindCSS 与 SvelteJS 一起使用

> 原文：<https://dev.to/muhajirdev/using-tailwindcss-with-sveltejs-2098>

如果你还没有检查过 [TailwindCSS](https://tailwindcss.com) ，你可能想检查一下。我不想在这里重复牛逼🤪

TL；DR 的完整实现可以在我的 Github 上找到[muhajirdev/svelte-tailwind-template](https://github.com/muhajirdev/svelte-tailwind-template)

## 获取苗条默认模板

```
npx degit sveltejs/template my-svelte-project
cd my-svelte-project 
```

Enter fullscreen mode Exit fullscreen mode

## 安装依赖项

```
npm install --save-dev tailwindcss postcss-import @fullhuman/postcss-purgecss postcss rollup-plugin-postcss autoprefixer 
```

Enter fullscreen mode Exit fullscreen mode

## 设置`rollup-plugin-postcss`

```
// rollup.config.js

...
import postcss from 'rollup-plugin-postcss'
...

export default {
    plugins: [
         postcss({extract: true}),
         svelte(...),
         ...
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是最终结果

```
//rollup.config.js

import svelte from 'rollup-plugin-svelte';
import resolve from 'rollup-plugin-node-resolve';
import commonjs from 'rollup-plugin-commonjs';
import livereload from 'rollup-plugin-livereload';
import { terser } from 'rollup-plugin-terser';
import postcss from 'rollup-plugin-postcss'

const production = !process.env.ROLLUP_WATCH;

export default {
    input: 'src/main.js',
    output: {
        sourcemap: true,
        format: 'iife',
        name: 'app',
        file: 'public/bundle.js'
    },
    plugins: [
        postcss({
            extract: true
        }),
        svelte({
            // enable run-time checks when not in production
            dev: !production,
            // we'll extract any component CSS out into
            // a separate file — better for performance
            css: css => {
                css.write('public/bundle.css');
            }
        }),

        // If you have external dependencies installed from
        // npm, you'll most likely need these plugins. In
        // some cases you'll need additional configuration —
        // consult the documentation for details:
        // https://github.com/rollup/rollup-plugin-commonjs
        resolve({ browser: true }),
        commonjs(),

        // Watch the `public` directory and refresh the
        // browser on changes when not in production
        !production && livereload('public'),

        // If we're building for production (npm run build
        // instead of npm run dev), minify
        production && terser()
    ],
    watch: {
        clearScreen: false
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 创造`postcss.config.js`

```
const production = !process.env.ROLLUP_WATCH;
const purgecss = require("@fullhuman/postcss-purgecss");

module.exports = {
  plugins: [
    require("postcss-import")(),
    require("tailwindcss"),
    require("autoprefixer"),
    // Only purge css on production
    production &&
      purgecss({
        content: ["./**/*.html", "./**/*.svelte"],
        defaultExtractor: content => content.match(/[A-Za-z0-9-_:/]+/g) || []
      })
  ]
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 创造`src/main.css`

```
@import "tailwindcss/base";

@import "tailwindcss/components";

@import "tailwindcss/utilities"; 
```

Enter fullscreen mode Exit fullscreen mode

## [中的](#import-raw-srcmaincss-endraw-in-raw-srcmainjs-endraw-)导入`src/main.js`中的`src/main.css`

```
// src/main.js
import App from "./App.svelte";
import "./main.css";

const app = new App({
  target: document.body,
  props: {
    name: "world"
  }
});

export default app; 
```

Enter fullscreen mode Exit fullscreen mode

## Optional

移除`public/global.css`
，同时移除

```
 <link rel="stylesheet" href="global.css" /> 
```

Enter fullscreen mode Exit fullscreen mode

因为我们已经有了来自`tailwind`的 normalize.css

最后，现在你可以写一个顺风类

试试看`<div class="bg-black">test</div>`应该会给你一个黑色背景的 div。

非常感谢您的任何反馈:)

*最初发表于[https://muhajir.dev/writing/using-tailwincss-with-svelte/](https://muhajir.dev/writing/using-tailwincss-with-svelte/)T3】*