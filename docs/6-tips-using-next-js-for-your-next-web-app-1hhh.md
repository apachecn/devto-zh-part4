# 6 个技巧:为你的下一个 web 应用程序使用 Next.js

> 原文：<https://dev.to/jlei523/6-tips-using-next-js-for-your-next-web-app-1hhh>

想搭建一个高性能的 React app，Next.js 必不可少。

它正被用来驱动我的一些应用程序，如数据驱动的医疗社区 [Medtally](https://medtally.com) 和香港房产自动估价工具 [True Home](https://truehome.hk) 。

在这个过程中，我学到了一些技巧，并发现了一些对初学者有用的框架“陷阱”。事不宜迟，让我们开始吧:

### 1。您需要缓存来自 getInitialProps 的数据，否则浏览器的后退按钮行为将会中断。

用于获取页面数据，但当用户按下浏览器上的后退按钮时也会触发。这导致浏览器滚动到您先前离开的位置，但是没有来自`getInitialProps`的远程数据需要呈现。你可以在这里阅读更多关于这个问题的[。](https://github.com/zeit/next.js/issues/3303)

要解决这个问题，您需要在获取数据后在客户端缓存数据。这里有一个快速简单的方法:

```
 let cache = {};

class App extends React.Component {
  static async getInitialProps({ req, query, asPath, pathname }) {

    let data;
    //if data is in cache then use the cache
    if (cache[someID]) {
      data = cache[someID]
    } else {
      //if not, then fetch from server
      data = await fetch(`someAPI`);
    }

    return {
     data: data
    }

  render() {
      //check if client, if so store the data in the cache. 
      //If you don't do this check, there will be a separate cache stored on the server since Next.js does server side rendering as well.
      if (process.browser) {
        cache[someID] = this.props.data;
      }

      render (){

       //your components
      }
    }
  } 
```

你也可以用 Redux 来做这个[。](https://stackoverflow.com/questions/42980613/how-do-i-implement-caching-in-redux)

### 2。使用 Next.js 的链接组件，而不是 Router.push，这样谷歌就可以抓取你的网址

谷歌的爬虫看不到这样写的链接:`<div onClick={handleClickWithRouter}>Go to About Page!</div>`

所以如果可能的话，避免用 Next 的路由器写链接。而是像这样使用 Next 的`<Link>`组件:

```
import Link from 'next/link'

function Home() {
  return (
    <div>
      Click{'  '}
      <Link href="/about">
        <a>here</a>
      </Link>{' '}
      to read more
    </div>
  )
}

export default Home 
```

### 3。与语义 UI React 相比，Next.js 与材质 UI React 配合得更好

如果你试图在材质 UI 和语义 UI React 组件库之间做出选择，那么你应该选择材质 UI。

语义 UI 的响应式组件并不是以一种与 Next.js 的服务器端呈现很好地工作的方式构建的，因为它们寻找服务器上不可用的浏览器的`window`对象。

如果一定要用语义 UI，可以按照这个 GitHub [票](https://github.com/Semantic-Org/Semantic-UI-React/issues/3361)一起黑。

另一方面，Material UI 的响应组件只使用媒体查询，这意味着您的组件应该在服务器和客户端上以相同的方式呈现。

如果我说服了你，那就去 Material UI 的 [Next.js 示例](https://github.com/mui-org/material-ui/tree/next/examples/nextjs)开始吧。

### 4。如果您使用同构解锁来获取数据，您需要提供绝对 URL

Next.js 的创建者推荐了一个名为`isomorphic-unfetch`的库来获取数据。它的包比`axios`小得多，在客户机和服务器上都运行良好。

然而，同构-解锁需要一个绝对的 URL，否则将会失败。我假设这与执行代码的不同环境(客户机和服务器)有关。在这种情况下，相对 URL 不够明确和可靠。

您可以像这样从 getInitialProps 构造一个绝对 URL:

```
class App extends React.Component {
  static async getInitialProps({ req, query, asPath, pathname }) {

    let baseUrl = req ? `${req.protocol}://${req.get("Host")}` : "";

    let data = await fetch(baseUrl + 'relativeURL')

    return {
      data: data

    }

  } 
```

1.  如果你想要最漂亮的网址，把你的网址储存在数据库里

网址应该很漂亮，这样人们在谷歌上看到它们时就会想点击它们。

您通常希望避免 URL 中的数据库表 id 是这样的:`/post/245/i-love-nextjs/`。

理想情况下，你的 URL 应该是这样的:`/post/i-love-nextjs`。

但是这个 URL 缺少从数据库获取数据所需的 id 245。

要解决这个问题，您需要将 URL 存储在数据库中，如下所示:

| 身份证明（identification） | 标题 | 内容 | 全球资源定位器(Uniform Resource Locator) |
| --- | --- | --- | --- |
| Two hundred and forty-five | 我爱 Next.js | 因为... | /post/i-love-nextjs |

在服务器上，当有人请求 mywebsite.com/post/i-love-nextjs.时，编写代码使用 URL 代替文章 id 获取数据

```
server.get('/post/:slug', async (req, res) => {
    const actualPage = "/post";

    //get the data using '/post/i-love-nextjs' as the id
    let data = await fetchDataWithURL(req.params.slug)

    const postContent = { data: data  };
    app.render(req, res, actualPage, postContent);
  }); 
```

如果你需要一个库来将文本转换成 URL，我推荐`slugify`。
如果您有许多 url，并且您使用的是关系数据库，那么您应该考虑为 URL 列添加一个索引，以便您的查找查询运行得更快。

1.  在 Next.js 中使用 Material UI 时，CSS 会中断生产，但不会中断开发

```
function createPageContext() {
  return {
    theme,
    sheetsManager: new Map(),
    sheetsRegistry: new SheetsRegistry(),

    //add this to fix broken css in prod
    generateClassName: createGenerateClassName({
      productionPrefix: "prod"
    })

  };
} 
```

如果您遇到这种情况，请尝试将这一行添加到 getPageContext.js 文件中:

就是这样！