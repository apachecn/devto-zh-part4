# 创建不带 Node.js 的 JS web 服务器

> 原文：<https://dev.to/rumkin/how-to-create-js-webserver-without-node-js-1hic>

现在有很多为 Node.js 创建的 web 服务器，很难说哪一个更好。工程师们正在努力为他们的产品添加一些新的东西，并使用最新的 JS 和 v8 功能来带来更快、更方便和更有效的解决方案。我也在研究一个。是[植物](https://rumkin/plant)。在本文中，我将讲述 Plant 在没有 Node.js 的情况下工作的能力。

Plant 是一个 WebAPI 收费的 web 服务器，这意味着它使用与浏览器相同的接口。此外，它与传输无关，你可以使用任何东西来传递请求:TCP、WebSocket、WebRTC 等。将所有这些结合起来，它可以以同样的方式在浏览器和服务器上工作。这种能力给了我们什么？

*   我们可以只使用代码编辑器和 web 浏览器来开发 web 服务器，使用 DevTool 的调试器而不是 console.log 来进行故障排除，然后只需将其移动到 Node.js。
*   我们甚至不需要安装任何东西。
*   它使得测试更加可靠，编写、运行和理解更加简单。
*   易学:代码被隔离到浏览器中，不受环境影响。
*   真正的跨平台:它在任何地方都以相同的方式工作。

> TL；博士这里是简单演示和复杂演示的[。](https://rumkin.github.io/plant-browser-demo/simple-example.html)

[演示回购](https://github.com/rumkin/plant-browser-demo) [工厂回购](https://github.com/rumkin/plant)

让我们开始吧。你所需要做的就是创建一个简单的 HTML 文件和一个空白页面。

```
<!DOCTYPE html>
<html>
  <head></head>
  <body></body>
</html> 
```

## 导入

要在您的网页中使用 Plant，只需从 unpkg.com:

```
<script src="https://unpkg.com/@plant/plant@2.4.0/dist/plant.js"></script> 
```

放入`<head>`。然后为我们的应用程序创建新的`<script>`到`<body>`元素中。

## 创建服务器

服务器创建类似于 Express’或 Koa’s，但略有不同。

```
// Instantiate server
const plant = new Plant()

// Add handler
plant.use('/index.html', ({req, res}) => {
  res.html('<!DOCTYPE html><html><body>Hello</body></html>')
}) 
```

就是这样。我们创建了一个非常简单的请求处理程序来处理“/index.html”页面。注意，路线非常严格。它可以处理`/index.html`，但不能处理`/`。

## 发出请求

浏览器无法监听请求，我们将在自己身上进行模拟。这段代码可以用来处理 WebSocket 或任何其他传输方式提交的请求，因此它可以被任何东西使用。

这是我们最后的代码，稍后会解释:

```
const handle = plant.getHandler()

const context = createContext('GET', 'https://localhost:8000/index.html')

handle(context)
.then(({res}) => {
  res.status // -> 200
  res.body // -> '<!DOCTYPE html>...
}) 
```

## 创建上下文

工厂上下文是一个简单的对象实例。它要求套接字、请求和响应实例分别作为`socket`、`req`和`res`属性被传递。

```
function createContext(method, url, {headers = {}, body} = {}) {
  const req = new Plant.Request({
    url: new URL(url),
    method,
    body,
    headers,
  })

  const res = new Plant.Response({
    url: new URL(url),
  })

  const socket = new Plant.Socket({
    peer: new Plant.Peer({
      uri: new Plant.URI({
        protocol: 'browser',
        host: navigator.appName,
      })
    })
  })

  return {req, res, socket}
} 
```

套接字需要一个代表连接的另一方的对等实例。对等方应始终提供一个地址，如 URI。URI 在这里使用，而不是标准的网址，因为网址无法与自定义方案。所有这些都需要，我们建议对等体也可以接收连接，因为工厂可以使用支持这种能力的传输。

## 发布请求

为了模拟 POST 请求，我们需要创建 [ReadableStream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream) 实例，并将其传递给请求构造函数。

```
const body = streamFrom('Hello')

new Request({
  method: 'GET',
  url: 'https://localhost:8000',
  body,
})

function streamFrom(value) {
  return new ReadableStream({
    start(controller) {
      // Encode UTF8 into Uint8Array and push it into the stream
      const encoder = new TextEncoder()
      controller.enqueue(encoder.encode(value))
      controller.close()
    },
  })
} 
```

函数`createStream`创建 ReadableStream 实例，并将整个传递的值作为一个块写入其中。

## 结论

现在，您可以将所有这些放在一起，并在自己的浏览器中模拟请求，而无需使用 Node.js。或者，您可以在浏览器中编写并调试 API，然后通过最小的更改将其移动到 Node.js。同样的代码也可以用于测试。

如果你遇到一些困难或者想自己检查，使用[简单的例子](https://rumkin.github.io/plant-browser-demo/simple-example.html)或者更复杂的演示。

## 储存库

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [拉姆金](https://github.com/rumkin) / [植物](https://github.com/rumkin/plant)

### 🌳JS web 服务器负责 WebAPI 和 neat HTTP2 支持

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Plant logo](img/2e5e7f4282570ca21dc8bab7ade8f930.png)](https://raw.githubusercontent.com/rumkin/plant/master/readme.md/packages/plant/dev/cover.png)

# 植物

[![npm](img/ff992d6c107167ab0429bbd5be722f55.png)](https://npmjs.com/package/@plant/plant)[![npm](img/0a3b0607468631aa8a8ffacafb37edf7.png)](https://npmjs.com/package/@plant/plant)[![](img/20364314c46b2c0fc05799dd9bd28775.png)](https://camo.githubusercontent.com/b3bab99d1b46b24e68dc583efbc7d244a8f9a3e9/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f73697a652d384b69422d626c75652e7376673f7374796c653d666c61742d737175617265)

[NPM](https://npmjs.com/package/@plant/plant) [来源](https://raw.githubusercontent.com/rumkin/plant/master/readme.md/packages/plant) [自述](https://raw.githubusercontent.com/rumkin/plant/master/readme.md/packages/plant/readme.md)

Plant 是基于 WebAPI 标准的 HTTP2 web 服务器，采用模块化架构和功能设计。此外，它是纯粹的，耦合度较低。

工厂支持 HTTP 1 和 HTTP 2 协议。但它与传输无关，可以通过 WebSockets、WebRTC 或 PostMessage 在浏览器中正常工作。

## 特征

*   <g-emoji class="g-emoji" alias="cloud" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2601.png">☁️</g-emoji> 轻量级:只有 **8** KiB 被缩小和压缩。
*   ✨ 无服务器就绪:甚至可以在浏览器中工作。
*   <g-emoji class="g-emoji" alias="shield" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6e1.png">面向🛡</g-emoji> 安全:默认使用最严格的[内容安全策略](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) (CSP)。
*   <g-emoji class="g-emoji" alias="triangular_ruler" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4d0.png">📐</g-emoji>基于标准:使用 WebAPI 接口。
*   <g-emoji class="g-emoji" alias="passenger_ship" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f6f3.png">🛳</g-emoji> 传输不可知:没有 HTTP 或平台耦合，船舶请求通过**一切**。

* * *

## 目录

*   [安装](https://raw.githubusercontent.com/rumkin/plant/master/readme.md/#install)
*   [例题](https://raw.githubusercontent.com/rumkin/plant/master/readme.md/#exmaples)
*   [套餐](https://raw.githubusercontent.com/rumkin/plant/master/readme.md/#packages)
*   [内部包装](https://raw.githubusercontent.com/rumkin/plant/master/readme.md/#internal-packages)

## 安装

```
# Install plant web server
npm i @plant/plant
# Install node HTTP2 transport
npm i @plant/http2
```

## 例子

### 你好世界

用 HTTP2 作为传输的 Hello world。

> <g-emoji class="g-emoji" alias="warning" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/26a0.png">⚠️</g-emoji> 注意，默认 CSP 报头值为`default-src localhost; form-action localhost`这将…

</article>

[View on GitHub](https://github.com/rumkin/plant)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [拉姆金](https://github.com/rumkin) / [工厂-浏览器-演示](https://github.com/rumkin/plant-browser-demo)

### 无节点 web 服务器演示

<article class="markdown-body entry-content container-lg" itemprop="text">

# 工厂浏览器演示

这是一个单文件演示 web 应用程序。其目的是展示如何在没有 Node.js 的情况下，仅使用浏览器和代码编辑器来开发具有测试覆盖的全功能 web 服务器。

## 使用

1.  将第[页](https://raw.githubusercontent.com/rumkin/plant-browser-demo/master/readme.md/doc/index.html)保存到磁盘上。
2.  在代码编辑器中打开它。
3.  更新`#Server`和`#Spec`脚本代码。
4.  刷新页面。DevTools 控制台应该包含测试的完整输出。

## 属国

*   [植物](https://github.com/rumkin/plant)。
*   [测试开始](https://github.com/rumkin/testup)。
*   TinyAssert 。

## 结构概述

该示例的代码分为几个脚本。每个脚本包含与特定任务相关的代码。

```
<script id="Server"></script>
<script id="Spec"></script>
<script id="TestupHelpers"></script>
<script id="PlantHelpers"></script>
<script id="Main"></script>
```

其中:

*   `#Server`。您的 http API 代码。
*   `#Spec`。你的测试…

</article>

[View on GitHub](https://github.com/rumkin/plant-browser-demo)