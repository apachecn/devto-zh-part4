# 哇！Next.js 继续令人印象深刻

> 原文：<https://dev.to/bbarbour/wow-next-js-continues-to-impress-4kom>

当 Next.js 的最新版本 9 发布时，我很兴奋。它带来了一系列功能，使服务器端的反应变得轻而易举。我知道当我第一次开始摆弄 Next.js 时，我很快就被迷住了，但是有些东西很笨重，很难配置。

我很高兴地说，他们通过这次更新提高了标准。我会对此赞不绝口，但你肯定需要看看他们的博客帖子，里面有最新的功能。

[Zeit 的第 9 版博文](https://nextjs.org/blog/next-9)

快速浏览一下——如果您喜欢使用 Typescript，现在已经有了对它的支持。此外，自动静态优化，允许利用服务器端渲染和静态预渲染，使您的网站运行速度极快。不要忘记产品的改进，也要关注开发者的体验。

[![head spinning](img/fdf29070431c14aa3ced584495741583.png)](https://i.giphy.com/media/xUPGcvE4VNePqiexOM/giphy.gif)

说到开发者体验，我想提一下我最喜欢的两个新增功能。

首先是 API 路由。您需要做的就是在 pages 目录中创建一个`/api`文件夹。每个端点都是该目录中的一个 javascript 文件。有了这个功能，您就可以轻松地开始构建您需要的 api 连接到您的数据库，处理 post 请求，以及使您的应用程序工作所需的任何其他东西..

您不再需要构建和启动单独的 Express 服务器。令人兴奋的是，端点文件中唯一需要的是一个简单的处理函数。这反映了 React 其余部分的功能组件风格，让我感觉这是一种无缝的体验。

看起来是这样的:

```
const handler = (req, res) => {
  res.json("message:" "Keep hitting those endpoints baby.")
}

export default handler 
```

Enter fullscreen mode Exit fullscreen mode

Next.js 现在是一个完整的包，从开始到结束——提供尽可能多产和让你的应用程序运行所需的一切。还有一个特性使得创建动态页面就像客户端 React 路由器方法一样快速、轻松。

在 pages 目录中，将文件命名为`blog/[postId].js`。在组件文件内部，使用 Next 的`getInitialProps`获取呈现组件所需的任何数据。

```
const Post = ({ data }) => {
  return <p>Post: {data.title}</p> }

Post.getInitialProps = async ({ query }) => {
  const { id } = query
  const response = await fetch(
    `https://jsonplaceholder.typicode.com/posts/${id}`
  )
  const data = await response.json()
  return { data }
}

export default Post 
```

Enter fullscreen mode Exit fullscreen mode

伙计，我希望这些更新能让你和我一样兴奋。所以我想和大家分享一下。我已经发现自己在构思下一个项目(双关语。)

走出去，做一些酷的东西。

> 注意:我与 Zeit 或 Next.js 团队没有任何关系。我只是他们作品的超级粉丝！