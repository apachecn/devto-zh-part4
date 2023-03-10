# 使用 Node.js 从 Web 检索 JSON 的三种方法

> 原文：<https://dev.to/isalevine/three-ways-to-retrieve-json-from-the-web-using-node-js-3c88>

*封面图片鸣谢: [**猎人 x 猎人**漫画](https://en.wikipedia.org/wiki/Hunter_%C3%97_Hunter)作者富坚义博，由你真正迷上。< 3*

在最近的一次技术挑战中，我被要求构建一个小型 Node.js 应用程序，首先需要从 web 上检索一些 JSON。因为我对 Node.js 还比较陌生，所以我没有意识到 Node.js **本身没有包含 [fetch() API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)** ，我习惯在我的前端 JavaScript 中使用它。

*(我没有意识到的是，fetch()实际上是来自 [`Window`接口](https://developer.mozilla.org/en-US/docs/Web/API/Window)的方法——没有前端，没有窗口！在技术面试开始时意识到的尴尬事情...)*

在通过挑战后，我花了一个周末的时间重构代码，并尝试了一些不同的方法来使用 Node.js 从 web 检索 JSON。**剧透:**node . js 中有一个 [http](https://nodejs.org/api/https.html) / [https](https://nodejs.org/api/https.html) 模块，但也有一些很酷的包[模仿 fetch()](https://www.npmjs.com/package/node-fetch) ，或者[进一步简化请求语法](https://www.npmjs.com/package/request)！

但在我们开始之前，我想介绍一个工具，在我的技术挑战中，我愚蠢地**没有**使用它: [JSONView](https://jsonview.com/) ，一个用于 Chrome 和 Firefox 的超级方便的 web 扩展，它可以在你的浏览器中漂亮地打印 JSON。

# 

我在代码挑战中遇到的一个问题是(由于我自己的错误)不能在我自己的计算机上工作，包括这个扩展。比较以下内容:

[![raw json from reddit](img/10d8ac3849189e592a030e38a23938e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RzH52Szs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/idoe9wtu7rlpahi3r740.png) 
*未格式化的 JSON 来自[https://www.reddit.com/r/popular.json](https://www.reddit.com/r/popular.json)*

对抗

[![formatted json from reddit using JSONView](img/58b33b27c1a84ef07fb6adeff7c76ed0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iPnyHN4B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/eunlo6kn684w6zyl26r6.png) 
*同来自[https://www.reddit.com/r/popular.json](https://www.reddit.com/r/popular.json)的 JSON，与 JSONView* 相当

除此之外，将光标悬停在特定字段上会显示访问该字段的路径:

[![highlighted view in JSONView showing selected json property's path](img/2ae778cca39958db5de5f1579d3a2096.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--27GVN34m--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ws6eijnvpznwuuzqxua.png) 
*光标悬停在“ups”字段上，左下角显示路径*

有了它，解析和访问您需要的数据会变得更快更容易。

# 用[‘节点提取’](https://www.npmjs.com/package/node-fetch)包复制 fetch()

node-fetch 包的功能与您预期的差不多:在 Node.js 中为您提供 fetch()语法

```
const fetch = require('node-fetch');

let url = "https://www.reddit.com/r/popular.json";

let settings = { method: "Get" };

fetch(url, settings)
    .then(res => res.json())
    .then((json) => {
        // do something with JSON
    }); 
```

这里，我们开始通过`require()`导入包，并创建了一个`settings`变量来将我们的 http 方法定义为 Get 请求。从那里，我们使用`fetch(url, settings)`，就像我们在前端一样。像往常一样，我们可以将响应`res`解析为 JSON，然后对它做任何我们需要做的事情。

**注意:**从一些非常基本的基准测试来看，*似乎*节点获取是本文中介绍的三个选项中最快的。下面是每个测试的计时时间(然而，这确实包括运行挑战的其余代码，而不仅仅是 fetch/https/request 本身):

```
fetch: 0.689 seconds
https: 2.827 seconds
request: 3.65 seconds 
```

我很乐意让其他人做更多的测试来验证/否定这一点！如果你是那个人，请在下面随意评论。；)

# 使用 Node.js 提供的 [http](https://nodejs.org/api/https.html) / [https](https://nodejs.org/api/https.html) 模块

Node.js 附带了一对 http/https 模块，在这种情况下，https 模块为 Get 请求提供了一个内置方法。下面是我们要看的代码:

```
const https = require('https');

let url = "https://www.reddit.com/r/popular.json";

https.get(url,(res) => {
    let body = "";

    res.on("data", (chunk) => {
        body += chunk;
    });

    res.on("end", () => {
        try {
            let json = JSON.parse(body);
            // do something with JSON
        } catch (error) {
            console.error(error.message);
        };
    });

}).on("error", (error) => {
    console.error(error.message);
}); 
```

这里还有更多的东西！首先，我们用`require()`导入 https 模块。然后我们可以调用`https.get(url, (res) => {} )`来发起一个 Get 请求。然后，在回调函数体中，我们首先创建一个空字符串`body`，我们将把我们的响应文本(也称为`res`)添加到该字符串中。

从那里，我们有了几个`.on`语法的例子，它将监听几个不同的事件——即`"data"`、`"end"`和`"error"`。

当响应遇到`"data"`时，我们将每个块作为文本添加到我们的主体变量中。一旦我们点击了响应的`"end"`，我们就使用`try / catch`语法尝试将我们主体的文本解析为 JSON，如果不能，就返回一个错误。最后，我们链接另一个`.on`调用来捕捉最初`https.get()`请求的`"error"`。

我发现这种语法相当笨拙和冗长，尽管我确实喜欢**要求`https.get()`使用**的显式错误处理。然而，这个模块比节点获取包慢——参见上面的基准测试结果。

# 用[【请求】](https://www.npmjs.com/package/request)包简化语法

我使用的第三个策略是请求包，目的是简化 Node.js 的 http 请求的语法(通常很冗长)。由于这是一个外部包，所以从用`npm install request`安装开始。

下面是我们要看的代码:

```
const request = require('request');

let url = "https://www.reddit.com/r/popular.json";

let options = {json: true};

request(url, options, (error, res, body) => {
    if (error) {
        return  console.log(error)
    };

    if (!error && res.statusCode == 200) {
        // do something with JSON, using the 'body' variable
    };
}); 
```

哇，那真的很可读！我们来分解一下。和其他例子一样，我们用`require()`导入包，并设置我们的 url 变量。请求包也有一个漂亮的`options`特性，在这里您可以指定很多东西——但是在这里，在设置`{ json: true }`时，我们告诉请求如果没有错误，就自动将响应的主体解析为 JSON(并且我们得到一个 200 状态码)。所以，要访问我们想要的 JSON，只需使用`body`变量！

然而，这种可读性是以速度为代价的。根据上面的基准测试结果，这是最慢的选择，很可能是因为太多的事情发生在幕后。但是，可读性是一流的，配置其他 http 请求就像这个 Get 请求示例一样简单！

# 结论

这个特殊的技术挑战是深入研究 Node.js 的 http 请求的绝佳机会！现在，您应该感觉有了各种工具来应对不同的情况。

正如我上面所说的，我很乐意让另一个人做一些测试/基准测试，并验证或否定我得到的速度测试结果！由于测试对我来说还是相对较新的，我非常想看看其他人如何对这些方法进行基准测试。感谢阅读，欢迎在下方评论！