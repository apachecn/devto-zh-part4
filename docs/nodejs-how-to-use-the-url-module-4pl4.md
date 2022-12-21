# NodeJS:如何使用 URL 模块

> 原文：<https://dev.to/miku86/nodejs-how-to-use-the-url-module-4pl4>

## [T1】简介](#intro)

所以我们[在我们的机器上安装了 NodeJS](https://dev.to/miku86/what-is-nodejs-how-to-install-it-1900) 。

我们也知道如何使用命令行参数。

现在我们想学习如何通过使用 [URL 模块](https://nodejs.org/api/url.html)从命令行处理一个`url`。

## 写一个简单的脚本

*   打开你的终端
*   创建一个名为`index.js`的文件:

```
touch index.js 
```

Enter fullscreen mode Exit fullscreen mode

*   将这段 JavaScript 代码添加到其中:

```
const url = require('url');

const myUrl = process.argv[2];

if (myUrl) {
  const { href, host, pathname, protocol } = new URL(myUrl);

  console.log(`The HREF is: ${href}`);
  console.log(`The Protocol is: ${protocol}`);
  console.log(`The Host is: ${host}`);
  console.log(`The Pathname is: ${pathname}`);
} 
```

Enter fullscreen mode Exit fullscreen mode

注意:我使用最常用的 url 属性来降低这个简单例子的复杂性。要查看所有可用的属性，请阅读 URL 模块的文档。

* * *

## 每行解释

```
// import the url module
const url = require('url');

// read the third argument (= the url ) & save it into a variable
const myUrl = process.argv[2];

// only run this block if the user inputs a third argument
if (myUrl) {
// destructure these specific properties from the URL
  const { href, host, pathname, protocol } = new URL(myUrl);

// log the destructured properties
  console.log(`The Href is: ${href}`);
  console.log(`The Protocol is: ${protocol}`);
  console.log(`The Host is: ${host}`);
  console.log(`The Pathname is: ${pathname}`);
} 
```

Enter fullscreen mode Exit fullscreen mode

有时候可以从[遗留 URL API](https://nodejs.org/api/url.html#url_legacy_url_api) 中看到 [url.parse()](https://nodejs.org/api/url.html#url_url_parse_urlstring_parsequerystring_slashesdenotehost) 的用法。旧版 URL API 已弃用，[不要用`url.parse()`](https://nodejs.org/api/url.html#url_legacy_url_api) ，用`new URL()`。

* * *

## 从终端运行它

*   运行它:

```
node index.js https://miku86.com/articles 
```

Enter fullscreen mode Exit fullscreen mode

*   结果:

```
The Href is: https://miku86.com/articles
The Protocol is: https:
The Host is: miku86.com
The Pathname is: /articles 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 进一步阅读

*   [URL 模块](https://nodejs.org/api/url.html)
*   [URL 属性概述](https://nodejs.org/api/url.html#url_url_strings_and_url_objects)
*   [URL 属性说明](https://nodejs.org/api/url.html#url_class_url)

* * *

## 提问

*   你使用原生的`URL module`还是像[查询字符串](https://www.npmjs.com/package/query-string)这样的库？你为什么用它？