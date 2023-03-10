# 从盖茨比身上移除 React

> 原文：<https://dev.to/itmayziii/removing-react-from-gatsby-20mi>

最近在我同事的帮助下，我发布了两个新的盖茨比插件，使得从盖茨比上移除 React 变得容易多了。你现在可能正通过你的显示器尖叫着**为什么你要删除 REACT** 我想给你指出我的另一个[帖子](https://dev.to/itmayziii/better-performance-using-dynamic-code-splitting-in-gatsby-with-loadable-components-6am)。这篇文章解释了我们经历的许多麻烦，以使我们的应用程序的包大小更小，但最终这些优化不足以满足我们的要求。我工作的公司需要一个非常快速的网站，在旧的移动设备上运行良好，React 对我们的需求来说只是太多的 Javascript。

不管怎样，这是 2 个插件。

1.  [移除 Javascript 插件](https://www.npmjs.com/package/gatsby-plugin-no-javascript)——按照它说的做，移除盖茨比生成的 Javascript 文件，但不是*所有的* javascript。

2.  添加 Webpack 入口 -添加一个 webpack 入口点，这样你仍然可以在你的页面上包含一些没有被 React 的 javascript。这个入口点像往常一样通过 Gatsby webpack 配置，并将被自动散列并添加到您的 HTML 中。

我鼓励任何想尝试这些插件的人首先阅读 NPM 页面。通过这两个插件的组合，我们能够将页面上的 Javascript 代码从 190KB 减少到 20KB 左右，从而实现了巨大的性能提升。

### 旁注

移除 React 的一个好处是，您的 React 代码简单地变成了仅在服务器上运行的视图层，并且您的所有组件都变成了没有状态、生命周期或事件管理的简单函数。**我不建议使用这些插件，除非你的 javascript 包大小对你来说超级重要**这些插件违背了 Gatsby 希望你使用 Gatsby 的方式(虽然这些插件不是 hacks，只是实现了 Gatsby 的 API)。