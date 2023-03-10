# Plant:用于 Node.js 和浏览器的 HTTP2 和 P2P web 服务器。

> 原文：<https://dev.to/rumkin/plant-small-smart-transport-agnostic-http-2-ready-js-web-server-which-can-work-in-browser-4nno>

Plant 是一个新的兼容 WebAPI 的 HTTP2 web 服务器。它与传输无关，高度模块化，默认情况下非常安全，并且很小:工厂的大小是 8 KiB +可选节点。js HTTP 传输是 38 KiB (minified，gzipped)；分别为 74 个 KiB 和 125 个 KiB(未混合、未压缩)。

工厂设计采用尖端技术，降低复杂性，使服务器便于携带。这种可移植性使您能够只使用文本编辑器在浏览器中编写和测试服务器端 API。工厂有额外的包，如 http 适配器、路由器和 http 传输包。

```
 const Plant = require('@plant/plant')
    const {createServer} = require('@plant/http')

    const plant = new Plant()

    plant.use('/greet', ({res}) => {
        res.body = 'Hello, World!'
    })

    createServer(plant)
    .listen(8080) 
```

# 浏览器内示例

这是一个非常简单的例子来说明它是如何工作的。它只是将请求呈现到内嵌的 iframe 中。它不模拟浏览器。它的目标是展示如何产生请求并模拟与工厂的网络连接。

[Codesandbox](https://codesandbox.io/s/plant-b8nft) [预览](https://b8nft.csb.app/)

# 详细信息

**HTTP/2-就绪**

工厂可以使用 HTTP/2 资源推送机制将响应推送到客户端。

```
 plant.use(({res}) => {
        res.push('/js/index.js')
        res.push('/css/style.css')

        res.html('<!DOCTYPE html><html><head>...')
    } 
```

**兼容 web API**

像请求、响应、头和流这样的对象拥有与 WebAPI 中已经存在的相同或熟悉的接口。工厂的请求和响应是从客户端镜像的，这就是为什么请求对象有响应的方法`json()`。

```
 plant.use(({req, res}) => {
      req.url.pathname // "/"
      req.headers.get('content-type')
      res.headers.set('content-length', 5)
      res.body = 'Hello'
    })

    // Retrieve JSON with one single command
    plant.use('/echo', async ({req, res}) => {
      const body = await req.json()

      res.json(json)
    }) 
```

工厂正在使用 ReadableStreams 而不是节点流。这就是为什么它可以在浏览器中无缝工作。例如在 ServiceWorker 中。

**运输不可知**

Plant 没有与 Node.js http 模块服务器紧密耦合，相反，Plant 将它用作外部依赖项。您可以轻松创建自己的交通工具。这就是为什么你可以通过任何东西传递请求:WebSockets，MessageChannel，raw TCP，WebRTC 甚至电子邮件(为什么不可以)。它使事情变得非常简单，尤其是你的测试。

```
 const Plant = require('@plant/plant');
    const {createServer} = require('@plant/http2');

    const plant = new Plant();
    plant.use(({res, socket}) => {
      res.body = 'Hello, World!'
    })

    createServer(plant, {
      key: '...', 
      cert: '...',
    })
    .listen(443) 
```

手动创建请求:

```
 const plant = new Plant()

    plant.use(({res}) => {
        res.body = 'Hi'
    })

    const url = new URL('http://localhost:8080/')

    // Create HTTP context's params
    const req = new Plant.Request({
      url,
    });
    const res = new Plant.Response({
      url,
    });

    // Request peer. Peer represents other side of connection.
    const peer = new Plant.Peer({
      uri: new Plant.URI({
        protocol: 'ws:',
        hostname: window.location.hostname,
        port: window.location.port,
      }),
    });

    // Create connection socket
    const socket = new Plant.Socket({
        peer,
        // If socket allows write upstream, then onPush method could be defined to handle pushes.
        // onPush should return Promise which resolves when response sending completes.
        onPush(response) {},
    });

    const handleRequest = plant.getHandler()

    handleRequest({req, res, socket})
    .then(() => {
        // Request handled. All requests (even faulty) should get there.
    }, (error) => {
        // Something went wrong
    }) 
```

**模块化**

工厂正试图在模块之间划分职责，并且不扩大自己的规模。所有的东西，尤其是与传输相关的，都被移出了服务器包，可以用您自己的代码来替换。

附加套件:

| 超文本传送协议（Hyper Text Transport Protocol 的缩写） | Node.js 本机 http 模块传输 |
| --- | --- |
| https | Node.js 本机 https 模块传输 |
| http2 | Node.js 本机 http2 模块传输 |
| https2 | 使用 TLS 传输的 Node.js 本机 http2 模块 |
| 路由器 | 路由器包 |

**默认安全**

工厂正在使用开箱即用的最严格的内容安全政策。这是唯一一个把安全放在第一位而不牺牲安全的网络服务器。这个策略不允许网页做任何事情，甚至运行一个单独的 JS。Content-Security-Policy 头的缺省值是非常否定的，应该在生产中使用，以保护客户端和服务器免受事故的影响。开发人员应该指定他们的网站需要的确切权限。

```
 const plant = new Plant({
      csp: Plant.CSP.STRICT,
    }) 
```

对于发展应该采用`Plant.CSP.LOCAL`政策。

# 路由器示例

```
const Plant = require('@plant/plant')
const Router = require('@plant/router')
const {createServer} = require('@plant/http')

// User API router
const router = new Router()

router.post('/', () => {})
router.get('/:id', () => {})
router.put('/:id', () => {})
router.delete('/:id', () => {})

plant.use('/api/users/*', router)

createServer(plant)
.listen(8080) 
```

# 参考文献

[Github](https://github.com/rumkin/plant) [NPM](https://www.npmjs.com/package/@plant/plant)

# P.S

我是这个软件包的作者，所以你可以 AMA。此外，通知我语法错误。我会非常感激。