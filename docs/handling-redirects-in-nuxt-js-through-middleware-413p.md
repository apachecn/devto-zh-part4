# 通过中间件处理 Nuxt.js 中的重定向

> 原文：<https://dev.to/jackabox/handling-redirects-in-nuxt-js-through-middleware-413p>

我最近发表了一篇关于[如何在 Laravel](https://jackwhiting.co.uk/posts/handling-seo-redirects-in-a-laravel-application/) 中处理重定向的文章，因为我也使用 [Nuxt.js](https://nuxtjs.org/) 我认为写一下如何通过纯 Nuxt(没有服务器，没有高级配置)简单地处理重定向会很好。

要做到这一点，我们需要利用**服务器中间件**，你可以从官方的 Nuxt.js 文档中了解更多关于这个[的信息，但简而言之，它允许我们处理没有在文件夹结构中定义的额外路径(例如一个 API)。在我们的例子中，我们将使用 serverMiddleware 来读取重定向，并将我们带到适当的路径。](https://nuxtjs.org/api/configuration-servermiddleware)

我们将创建一个 JSON 文件来保存我们所有的重定向，然后让中间件读取起始目的地并将我们发送到最终目的地。

### 创建 JSON

在您的项目中创建一个新的 JSON 文件，并将其放在您想要的位置。对于本例，将文件放在下面的目的地`/data/redirects.json`。

我们的 JSON 应该包含一个带有`from`和`to`的元素数组，请看下面的例子，并根据自己的需要进行调整。

```
[  {  "from":  "/",  "new":  "/home"  },  {  "from":  "/get-in-touch",  "to":  "/contact"  }  ] 
```

Enter fullscreen mode Exit fullscreen mode

### 创建服务器中间件

接下来，我们需要创建一个文件来处理 JSON 的读取和处理重定向的逻辑。在`/serverMiddleware/redirects.js`中创建一个新文件，代码如下:

```
const redirects = require('../data/redirects.json') // update to your file path

export default function(req, rest, next) {
  // find the redirect if it exists where the from === the requested url
  const redirect = redirects.find(r => r.from === req.url)

  // If it exists, redirect the page with a 301 response else carry on
  if (redirect) {
    res.writeHead(301, { Location: redirect.to })
    res.end()
  } else {
    next()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上述文件检查 URL 是否存在，如果存在，则重定向到目的地。如果没有，Nuxt 应用程序将继续前进并返回请求的页面。

### 添加到 Nuxt.config.js

在一切正常之前，我们需要将新创建的重定向中间件添加到`nuxt.config.js`中。编辑`nuxt.config.js`文件，并将以下内容添加到:

```
...
serverMiddleware: [
    '~/serverMiddleware/redirects.js'
],
... 
```

Enter fullscreen mode Exit fullscreen mode

重新编译代码，检查一切是否正常。

#### 奖金

如果你需要更进一步的重定向，或者如果你不介意使用第三方软件包，在 https://github.com/nuxt-community/redirect-module 有一个很棒的软件包可以为你处理这些。

* * *

喜欢你读的书吗？你可以在[https://jackwhiting.co.uk](https://jackwhiting.co.uk)T3】的我的博客上找到更多文章