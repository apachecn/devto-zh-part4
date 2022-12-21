# 使用 Netlify 函数获取外部文件

> 原文：<https://dev.to/abusedmedia/using-netlify-functions-to-fetch-external-files-1a4b>

在我最近的[项目](https://www.svgmator.com/)中，我需要从浏览器中加载外部文件，比如一个远程服务器上托管的 SVG 文件，以便将它们注入 DOM 中进行进一步操作。

这是一个很好的 UX 插件，可以避免用户下载特定的文件，然后上传到 SVGmator 上。提供 URL，软件将能够代表用户在后台下载和上传。

这是一个简单的任务，但是浏览器的安全模型阻止通过 javascript 获取属于不同域的外部文本文件，因此，这个简单的脚本不能开箱即用:

```
fetch('https://www.examples.com/myfile.svg') 
```

## 欢迎 Lambda 函数

如今，找到提供所谓的 lambda 函数的服务是很常见的。简而言之，它们是(通常是 Node.js)脚本，可以通过 API 调用来调用，执行任务并返回结果。你不必处理他们的主机和其他服务器的东西(事实上，他们是无服务器运动的一部分！).

回到我的问题，这里是完成上述需求的代码，在这个例子中使用了 [Netlify 函数](https://www.netlify.com/docs/functions/)(lambda 的 Netlify 版本)。

浏览器需要向 lambda 发出 POST 请求，传递一个参数，比如:

```
const req = await fetch('/.netlify/getsvg', {
   method:'POST',
   body: JSON.stringify({url: 'https://www.examples.com/myfile.svg'})
})
const res = await req.text() 
```

上面的代码(它是 ES6，它需要在一个异步函数中)将得到一个原始的 SVG 代码，最终可以注入到 DOM 中。

lambda 函数是一个 Node.js 脚本，它实际上获取 SVG 并将其作为原始源代码返回:

```
const axios = require('axios')

exports.handler = async (event, context) => {
   const body = JSON.parse(event.body)

   const res = await axios.get(body.url)

   return {
      statusCode: res.status,
      body: res.data
   }
} 
```

大概就这些了。请参考[网络生活文档](https://www.netlify.com/docs/functions/)了解如何使用该服务。

这篇文章最初发表在我的博客上。