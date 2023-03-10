# 将引导 CDN 链接添加到 Gatsby

> 原文：<https://dev.to/cole_ruche/adding-bootstrap-cdn-link-to-gatsby-59fe>

*我的[博客](https://colesblog.netlify.com/post/Adding-Bootstrap-CDN-in-Gatsby/)的原帖 T3*

这篇文章旨在帮助那些喜欢使用 Gatsby JS 和 Twitter Bootstrap 并且绝对喜欢在一个项目中一起使用它们的开发人员。
你可能会遇到的一个问题是如何将两者联系起来。为此，你有两个选择:你可以使用受支持的包，如 [React Bootstrap](https://react-bootstrap.netlify.com/) 或 [reactstrap](https://reactstrap.github.io/) ，或者你可以在 CDN 中包含到你的应用的链接。

虽然第一个选项非常好，但我觉得用起来有点大材小用，尤其是如果您需要的 Bootstrap 只是有趣的 CSS。非 jQuery 部分和功能，如网格系统或排版。如果是这样，我们将采用第二种选择。这听起来很容易，直到你发现用[启动器](https://www.gatsbyjs.org/starters/)创建的 Gatsby 应用程序没有像用`create-react-app`或任何其他前端项目创建的普通 React 应用程序那样的`index.html`文件。

现在，我们在哪里包括我们的 CDN 链接？没有`html`文件，因此没有`head`标签。这就是本文的目的所在。

还是那句话，我们有两个选择——*嘿！生活充满了选择。*
盖茨比项目附带一个`seo.js`文件，可以在`src/components`中找到。该组件使用[反应头盔](https://github.com/nfl/react-helmet)发挥作用。这使得我们可以很容易地对 gatsby 捆绑我们的应用程序时产生的`html`文件进行修改。我们可以通过这样做来包含 CDN 链接:

```
<Helmet>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous" />
</Helmet> 
```

Enter fullscreen mode Exit fullscreen mode

这是相对容易做到的，应该工作得很好。我以前用过这个，直到我注意到有时由于某种原因，它不起作用——它在大多数时候都起作用——需要用户刷新页面才能起作用。你不能指望用户这样做！

这就引出了第二种解决方法。

当我们用 Gatsby starter 引导一个应用程序时，查看一下为我们创建的项目结构，应该会在根文件夹中显示一个名为`gatsby-browser.js`的文件。
根据盖茨比网站的说法，*“盖茨比希望在这个文件中找到盖茨比浏览器 API 的任何用法(如果有的话)。这些允许自定义/扩展影响浏览器的默认 Gatsby 设置。*
(对我来说)这意味着，我们可以调用 API 并导入影响浏览器的*东西*，比如我们的样式和定制 JavaScript 脚本。这听起来是一个介绍我们 CDN 的好时机，

要做到这一点，我们必须放开我们的 CDN。抱歉，这篇博客的文章可能会误导我们，因为我们将不得不在这里删除我们的 CDN 链接。一切都好。我们必须在项目中安装 Bootstrap。为此，运行:

```
npm install bootstrap 
```

Enter fullscreen mode Exit fullscreen mode

或者

```
yarn add bootstrap 
```

Enter fullscreen mode Exit fullscreen mode

这将在我们的应用程序中安装官方的引导文件夹。接下来，打开`gatsby-browser.js`文件，从文件顶部的 Bootstrap 导入 CSS 文件，就像这样

```
//bootstrap 
import 'bootstrap/dist/css/bootstrap.css'; 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！随意使用你喜欢的引导风格。没有麻烦！

感谢您的关注，希望您能给出反馈，告诉我们这对您有什么帮助，或者您是否有自己的方法将它们联系起来。