# 传统与现代网络开发⚔️

> 原文：<https://dev.to/sunnysingh/traditional-vs-modern-web-development-1em8>

现代网络开发。我们都对它又爱又恨，因为虽然它革新了 web 开发实践，但也使它们变得非常复杂。

我想回顾一下现代 web 开发的几个方面，并解释一下我所能体会到的好处。我还会提到当你仍然想用传统方式做事的时候。

## 📦管理包

**老办法**:手动下载包文件到`vendor`文件夹或者链接 CDN。

**现代方式**:使用像 [npm](https://www.npmjs.com/) 或 [Yarn](https://yarnpkg.com/) 这样的包装经理。

**好处**:能够用`install`、`update`和`remove`命令管理你所有的软件包不仅节省时间，还可以跟踪诸如过期或不安全的软件包之类的东西。一个缺点是这些包最终依赖于大量的文件，所以对于非常简单的站点，你可能仍然认为从 CDN 链接一个包更好。

## 🎮构建用户界面

**老方法**:使用 [jQuery](https://jquery.com/) 手动操作 DOM。

**现代方式**:使用 [React](https://reactjs.org/) 或 [Vue.js](https://vuejs.org/) 用 JavaScript 渲染整个 ui。

好处:将你的用户界面分解成组件和状态可以极大地提高工作效率，还有助于减少错误。我在我的[声明中谈了很多！](https://sunnysingh.io/blog/declarative)文章。像 [JAMStack](https://jamstack.org/) 这样的静态架构对于简化部署也有很大的好处。请记住，虽然大型应用程序变得更容易维护，但手动 DOM 操作仍然非常适合在没有外部依赖性的情况下就地构建一些东西。

## 🎁捆绑代码

**老方法**:连接和缩小工具，手动声明文件如何合并在一起。

**现代方式**:使用`import`语句和一个类似[包](https://parceljs.org/)的捆绑器。

**好处**:虽然这最初是一个巨大的痛点，但是捆绑工具现在已经变得容易使用了。您可以使用包括`import`语句在内的最新特性开始编写 JS 和 CSS，代码会自动转换和打包。这不仅对跨浏览器兼容性很重要，而且对性能也很重要，所以即使在你可以依赖用户使用最新浏览器的情况下，你可能还是想使用 bundler。

额外功能:框架的 CLI 工具为您抽象出捆绑，让您不必担心。参见[创建 React App](https://facebook.github.io/create-react-app/) 、 [Next.js](https://nextjs.org/) 、 [Vue CLI](https://cli.vuejs.org/guide/creating-a-project.html) 和 [Nuxt.js](https://nuxtjs.org/) 。

## 选择的数量呢？🤯

的确，由于我们必须做出大量的决定，现在网络开发被认为是复杂的。这对于任何试图学习所有东西的新手来说都变得特别困难。

然而，我认为每个开发人员都应该具备的一项技能是决定什么是值得学习和使用的。不要立即安装您在一篇中型文章中读到的新框架，除非它为您解决了另一个更受社区支持的框架尚未解决的特定问题。

我希望提供更多的资源来帮助你更明智地做出这些决定。如果你仍然想知道使用哪个 JavaScript 框架，看看我的[为什么每个人都在使用 React？](https://sunnycommutes.fm/episodes/62-why-is-everyone-using-react-K0EAQo)播客插曲。

> 本文原载于[我的简讯](https://sunnysingh.io/news)。