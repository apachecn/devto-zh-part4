# 完全堆叠:设置 NodeJS + KoaJS

> 原文：<https://dev.to/heymarkkop/fullstacking-setting-up-nodejs-koajs-2jhb>

## 节点

```
sudo apt install curl
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt install nodejs
node --version
npm --version 
```

Enter fullscreen mode Exit fullscreen mode

## KoaJS

`yarn add koa`或`npm install koa`
创建一个文件(koa.js)并粘贴如下:

```
const Koa = require('koa');
const app = new Koa();

app.use(async ctx => {
  ctx.body = 'Hello World';
});

app.listen(3000, () => console.log('Running on http://localhost:3000/')); 
```

Enter fullscreen mode Exit fullscreen mode

检查[http://localhost:3000](http://localhost:3000)
太好了，可以用了！

[(顺便说一下)](https://dev.to/hoverbaum/how-to-add-code-highlighting-to-your-devto-posts-2lp6)