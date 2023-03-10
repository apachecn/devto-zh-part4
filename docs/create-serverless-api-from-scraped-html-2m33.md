# 为 HTML 抓取创建无服务器 API

> 原文：<https://dev.to/spukas/create-serverless-api-from-scraped-html-2m33>

上周我写了一篇关于如何用 Zeit Now 设置无服务器功能的帖子。我想继续关于无服务器功能的话题，并创建一个 API 来提取、转换和加载数据给用户。
比如我挑了`showsrss.info`网站，它没有 API，但是用 HTML 文档响应。

## 选择工具

我们将使用的工具:

*   现在 CLI
*   阿克斯
*   再见

首先，我们需要一个 node.js 的 HTTP 客户端来请求网站。我非常喜欢覆盖几乎所有浏览器和 node.js 的`axios`。
`cheerio`是一个核心 jQuery 特性库，用于解析标记，并提供一个 API 来遍历/操作服务器中的结果数据结构。我们将使用它来抓取 HTML。

另外，如果您想按照这些步骤操作，现在就全局安装 CLI`npm i -g now`。

## 设置项目

让我们从头开始，在终端中初始化项目:

```
mkdir showrss && cd showrss
npm init --yes
npm install axios cheerio 
```

如果你想知道`npm init --yes`是什么意思，它用`Yes`回应所有答案。太好了，我们有了一个只有`package.json`和`node_modules`的新项目。下一步是为用户创建一个 API 端点来发送请求。为了让 Zeit Now 创建一个带有端点的无服务器函数，这个项目必须在`root`目录中有一个名为`api`的文件夹，以及一个文件，其名称将反映端点。再次在终端运行:

```
mkdir api && cd api
touch shows.js 
```

## 创建无服务器功能

文件`show.js`应该导出一个默认函数，该函数接收两个参数`request`和`response`。这些是标准的 HTTP 请求和响应对象，但是 Zeit Now 增加了一些助手。

```
// show.js
module.exports = (request, response) => {
  // send() method can receive a string, object or buffer
  // json() will send only JSON object
  response.send('Hello there!');
} 
```

为了测试端点，使用`now dev`在开发模式下从项目`root`目录构建函数。
如果您在浏览器中通过呼叫`http://localhost:3000/api/shows`向发送请求，您应该会收到一条问候消息。

## 从第三方服务提取数据

调用无服务器函数时，我们可以用`axios` helper 对函数内部的第三方服务`showrss.info`进行请求调用。
夫妻注意点:

*   HTTP 调用需要一些时间，所以我们需要将默认函数声明为一个`async`
*   `axios`返回一个具有字段`data`的对象，实际的响应将存储在该字段中。

```
const axios = require('axios');

module.exports = async (request, response) => {
  const showsResponse = await axios.get('https://showrss.info/browse');
  const htmlData = showsResponse.data;

  response.send(htmlData);
}; 
```

现在，您应该看到一个呈现的 HTML 响应。

## 将 HTML 转换成 JS 对象

在网站响应之后，我们得到一个 HTML 文档。要遍历它并提取每个节目信息:id、标题和到提要的单个 RSS 链接，用`load()`方法将文档加载到`cheerio`中。之后，数据就可以提取了，可以像 jQuery 一样访问。

```
const cheerio = require("cheerio");

...
  const $ = cheerio.load(htmlData);
  const options = $("#showselector option");
  const showList = Object.keys(options)
    .map(key => {
      const show = $(options[key]);
      return {
        id: show.attr("value"),
        title: show.text(),
        rss: `http://showrss.info/show/${show.attr("value")}.rss`
      };
    });

  response.send(showList);
... 
```

现在尝试访问 en endpoint，您应该会收到一个数组，其中包含所有电视节目的列表和一个单独的 RSS 提要链接。

## 部署享受

最后一步是通过在终端中运行`now`来部署无服务器功能。成功部署后，您将获得一个访问链接，在客户机上，您可以获取已经转换的列表。我要求你创建一个新的无服务器函数，它接收节目 id 作为参数，并以该节目的附加数据作为响应。