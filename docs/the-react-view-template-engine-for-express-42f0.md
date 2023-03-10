# Express 的 React 视图模板引擎

> 原文：<https://dev.to/saltyshiomix/the-react-view-template-engine-for-express-42f0>

## TLDR

*   将服务器数据传递给 React 客户端`props`
*   因为这是一个视图模板引擎，所以结果可以通过搜索引擎搜索，如谷歌(是的，我们使用服务器端渲染)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[saltyshiomix](https://github.com/saltyshiomix)/[react-SSR](https://github.com/saltyshiomix/react-ssr)

### 将 SSR 作为视图模板引擎

<article class="markdown-body entry-content container-lg" itemprop="text">

## 概观

*   SSR(服务器端渲染)作为视图模板引擎
*   动态`props`
    *   将服务器数据传递给 React 客户端`props`
    *   适合…的
        *   管理面板
        *   博客
*   开发者体验
    *   webpack 和 babel 的零配置
    *   HMR(热模块替换)既有脚本又有风格时`process.env.NODE_ENV !== 'production'`
    *   内置 Sass (SCSS)支持

## 利弊

### 赞成的意见

因为它只是一个视图模板引擎:

*   它不需要任何 API，我们所要做的就是将服务器数据传递给客户端
*   它支持多种引擎，如`.hbs`、`.ejs`和`.(ts|js)x`
*   我们可以像往常一样使用 [passport](http://www.passportjs.org) 认证

### 骗局

*   它的性能不是很好，因为它在每个请求上都组装了整个 HTML
*   它不支持**客户端路由**

## 使用

### 使用@react-ssr/express

安装它:

```
$ npm install --save @react-ssr/core @react-ssr/express express react react-dom
```

Enter fullscreen mode Exit fullscreen mode

并将脚本添加到 package.json 中，如下所示:

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/saltyshiomix/react-ssr)

## 快速启动

### 安装

```
$ npm install --save @react-ssr/express express react react-dom 
```

Enter fullscreen mode Exit fullscreen mode

### 填充`package.json`

```
{  "scripts":  {  "start":  "node server.js"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 写`server.js`

```
const express = require('@react-ssr/express');
const app = express();

app.get('/', (req, res) => {
  const user = { name: 'World' };
  res.render('index', { user });
});

app.listen(3000, () => {
  console.log('> Ready on http://localhost:3000');
}); 
```

Enter fullscreen mode Exit fullscreen mode

### 实现`views/index.jsx`

```
import React from 'react';

export default function Index(props) {
  return `Hello ${props.user.name}!`;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 运行服务器

```
$ npm start 
```

Enter fullscreen mode Exit fullscreen mode

你会看到`Hello World!`

## 深潜

### 1。寄存器`jsx`和`tsx`

来源: [register.ts](https://github.com/saltyshiomix/react-ssr/blob/master/packages/express/lib/register.ts)

```
const ENGINE: 'jsx' | 'tsx' = getEngine();
app.engine(ENGINE, renderFile);
app.set('views', resolve(process.cwd(), viewsDir));
app.set('view engine', ENGINE);
app.use(express.static(distDir)); 
```

Enter fullscreen mode Exit fullscreen mode

### 2。渲染文件(服务器端渲染)

来源: [render.tsx](https://github.com/saltyshiomix/react-ssr/blob/master/packages/express/lib/render.tsx)

```
import { renderToString } from 'react-dom/server';

let html: string = '<!DOCTYPE html>';

let Page = require(file); // `file` is a React function component
Page = Page.default || Page;

html += renderToString(
  <Html script={`${hash}.js`}>
    <Page {...props} />
  </Html>
);

return html; 
```

Enter fullscreen mode Exit fullscreen mode

### 3。捆绑脚本并编写输出

来源: [render.tsx](https://github.com/saltyshiomix/react-ssr/blob/master/packages/express/lib/render.tsx)

```
import fs from 'fs';
import template from 'art-template';
import webpack from 'webpack';

const { ufs } = require('unionfs');
const MemoryFileSystem = require('memory-fs');
const template = require('art-template');

const cwd: string = process.cwd();

template.defaults.minimize = false;

const mfs = new MemoryFileSystem;
ufs.use(mfs).use(fs); // union memory-fs and fs!

// write file in the server memory
mfs.mkdirpSync(resolve(cwd, 'react-ssr-src'));
mfs.writeFileSync(resolve(cwd, `react-ssr-src/entry.jsx`), template(resolve(__dirname, '../page.jsx'), { props }));
mfs.writeFileSync(resolve(cwd, `react-ssr-src/page.jsx`), template(file, props));

// compile in the server memory!
const compiler: webpack.Compiler = webpack(configure(hash, ext, distDir));
compiler.inputFileSystem = ufs;
compiler.outputFileSystem = mfs;
compiler.run((err: any) => {
  if (err) {
    console.error(err.stack || err);
    if (err.details) {
      console.error(err.details);
    }
    return;
  }
});

// read the results from memory file system
// write the results to the real file system
await outputFileSync('result.js', mfs.readFileSync(cache).toString()); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

最终输出的 html 是这样的:

```
<!DOCTYPE html>
<html>
  <body>
    <div id="app">
      <p>Hello World!</p>
    </div>
    <script src="/c834ab9b47260a08d695f59ba1a5b24d.js"></script>
  </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

## 结局

但是我爱用 [NEXT.js](https://nextjs.org) ！哈哈