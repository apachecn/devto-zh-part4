# 🛠用 50 行代码构建了一个节点 web 服务器

> 原文：<https://dev.to/lucsan/bulding-a-node-web-server-in-50-lines-of-code-4hob>

SimpServ 简单服务器是一个，占地面积小，节点 js，web 服务器，在一个 javascript 的页面，这是令人兴奋的。

该代码可在[https://github.com/lucsan/simpserv/blob/master/simpserv.js](https://github.com/lucsan/simpserv/blob/master/simpserv.js)获得

您可以将文件复制到🍰期望的位置，或者如果你喜欢，做整个 npm git 的事情👽克隆或🍴叉子，或 https://github.com/lucsan/simpserv 的🥄勺子。

它的目的是提供一个💨快速和💩用于 js、html 和 css 的脏本地开发服务器。

为此📃我们将需要一些来自基本 nodejs 库的节点包。

```
const http = require('http')
const url = require('url')
const fs = require('fs') 
```

因此你需要在你的机器上安装 ***nodejs*** 。我会让你享受锻炼的乐趣😱怎么做。

文件中有简单的配置，

```
const config = {
  port: '8080', // And any port you like.
  root: '', // If you want to server files from a different location to where you started node.
  autoload: true, // Auto loads a window in the default browser.
  index: 'index.html', // You need an index.html in your root.
} 
```

这🚂当使用节点或 npm 通过命令行调用 ***start()*** 时,“引擎”启动。

##### 📄例如:

`node simpserv.js`，或者(如果你有 package.json) `npm run serv`

启动功能非常简单。服务器(`http.createServer().listen()`)被创建并被告知专注地监听浏览器。

将`onRequest()`函数轻轻地注入到`createServer`中，使`request.on`事件处理程序可用，它急切地等待从它喜欢的浏览器接收输入(即:一个 uri)。

```
function start () {
  http.createServer(onRequest).listen(config.port)
  console.log('SimpServ running')
  function onRequest(request, response) {
    request.on('error', function(err){ console.log('err ', err) })
    router(request, response)
  }
} 
```

每次 ***request.on*** 事件接收到一个新的 uri 时，它会急切地将它传递给路由器函数。

路由器功能超级强大，可以处理所有其他事情。在一个更大的项目中，这可能会作为一个模块包含在内，无论如何，这就是...

```
const router = (request, response) => {
  let filename = url.parse(request.url).pathname
  filename = filename.replace('/', '')
  if (filename == '') filename = config.index
  let path = (config.root == '' ? filename : `${config.root}/${filename}`)
  let bits = path.split('.')
  let ext = bits[bits.length - 1]
  let ct = 'text/html;'
  if (ext == 'css') ct = 'text/css;'
  if (ext == 'js') ct = 'text/javascript;'

  fs.readFile(path,  (err, page) => {
    if (err) console.log(err)
    response.writeHead(200, { 'Content-Type': `${ct}charset=utf-8` })
    if (page) {
      response.write(page)
    } else {
      response.write('Error')
    }
    response.end()
  })
} 
```

在前几行中，我们从根 uri 中提取路径的本质，

即:root = / or ' '， ***/*** 之后的任何东西都被认为是路径。

例如:
[https://localhost/my project/](https://localhost/myproject/)=/或者“

[https://localhost/my project/index . html](https://localhost/myproject/index.html)= index.html

[https://localhost/my project/some/place](https://localhost/myproject/some/place)= some/place

如果路径为空，我们将使用 index.html 作为默认文件，但是您可以将它设置为您喜欢的任何文件。

接下来，我们检查扩展类型的路径。在这种情况下，我们只关心 css 和 js，这一点也不关心我们，但是，其他扩展也可以考虑(例如:php)。
将所需的 MIME 类型插入 ***ct***

最后 **readfile** 被调用，传入路径，它服务于被引用的文件(应该在，*或相对*，到运行文件夹的根或*节点)，如果它能找到它，否则，报错。🤖不计算。*

这段代码:

```
if (process.platform == 'win32') {
  require('child_process')
    .exec(`start http://localhost:${config.port}`)
} 
```

在默认浏览器中打开一个页面，指向 **/** 或 **/index.htm** ，它只在 Windows 上工作，但你可以在其他操作系统上找到等效的。这是一个很好的尝试，虽然从技术上来说并不是简单服务器的一部分。

正如你所看到的，你可以很容易地开始修改这些代码，以达到各种目的(php，data endpoint)，或者使其更全面(和复杂)，或者使其适应特定的目的(就像🖌illustrated 现有的代码一样)。

下面是响应 API 端点的代码片段:

```
if (path.indexOf('/customer') > -1) {
  if (path.indexOf('/name') > -1) filepath = '/customerName.html'
  if (path.indexOf('/address') > -1) filepath = '/customerAddress.html'
} 
```

此处插入:

```
if (ext == 'js') ct = 'text/javascript;'
  // --- Insert filepath adapters here --- //
fs.readFile(path,  (err, page) => { ... 
```

现在，当您访问 **/customer/name** 时，会返回文件 customerName.html，但这也可能是一些 json 数据，或者**文件路径**可以解析为从函数返回的 html。

```
 filepath = myDataHtml('getSome') 
```

🏄‍Surfs 起来了，伙计们🌊