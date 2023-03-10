# 如何通过 BootstrapCDN 将您的 Angular 应用程序链接到 Bootstrap

> 原文：<https://dev.to/josylad/how-to-link-your-angular-app-to-bootstrap-via-bootstrapcdn-12fh>

Angular 无疑是一个很好的前端框架，如果您正在构建大型/企业项目，它将非常有用，angular CLI 还为您提供了一个快速启动，允许您尽快开始构建您的项目。

嗯，这篇文章实际上不是关于 angular 框架本身，而是关于用 Bootstrap 扩展 angular 的功能。

### 什么是 Bootstrap？

> “Bootstrap 是一个免费的开源 CSS 框架，旨在进行响应迅速、移动优先的前端 web 开发。它包含基于 CSS 和 JavaScript 的设计模板，用于排版、表单、按钮、导航和其他界面组件。”

简单来说，Bootstrap 是一个 CSS 框架，可以帮助你轻松构建漂亮的网站。

**在你的应用中有两种主要的使用 Bootstrap 的方法，它们是:**

1.  将引导编译的 CSS 和 Javascript 源代码下载到您的本地机器。
2.  使用 BootstrapCDN。

在本文中，我们将重点讨论后一种方法，即使用引导 CDN 链接。

### 如何在 Angular Apps 中集成 Bootstrap CDN

#### **第一步。**

进入自举网站([https://getbootstrap.com](https://getbootstrap.com/))，点击**“开始”**按钮。这将带您进入启动页面。

[![](img/e7e195dc466b9667b0e05bc7262562c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--77dB8fwf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A7DlRffUtj5BKy5vJkN6dpQ.png)

#### **第二步。**

在入门页面上，稍微向下滚动到“快速入门”部分，在那里可以找到 BootstrapCDN CSS 链接和 JS 链接。

[![](img/4fa9abfada41b4c7545441aa3b3151cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uLVDI4pJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AjQpV-kRp6CjDBVtj_44utQ.png)

复制提供的 CSS 链接，**确保复制正确**。

#### **第三步。**

在你最喜欢的代码编辑器(Atom，VS code，Sublime 等)中打开你的 Angular 应用。

在你的 angular App 根文件夹(src/app)中寻找 **"index.html"** 并打开。

[![](img/d5e7e3c4492792e1c5723a9c781d161c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y-VwOONd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/904/1%2Ah-M2r9_BqY5WFVfdMLYOyw.png)

#### **第四步。**

在 index.html 文件中找到结尾头标签( **< /head >** )，将你从 Bootstrap 网站获得的链接粘贴到结尾头标签的正上方。

[![](img/c86d8a974088be6032b302ef2c58c3ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V3W_TF3x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUHolIv7Y8p29mzw_C3zARA.jpeg)

**注意:**我建议你集成引导 CSS 链接和 JS 链接，因为一些引导 CSS 函数依赖于 Javascript/Jquery 来工作。

#### **第五步。**

在你的应用程序中添加一些引导样式和类来测试它是否有效。在 Bootstrap 网站上可以找到很多款式。

#### **第六步。**

没什么可做的了。

现在，您应该已经成功地将 Angular 应用程序链接到 Bootstrap。

### 终注。

如果你正确地遵循了上面的步骤，你的 Angular 应用程序应该可以很好地与 Bootstrap 一起工作；如果不起作用，请再次执行这些步骤，检查您是否遗漏了什么，并确保您在应用程序中复制并粘贴了正确的引导 CDN 链接。

如果您发现这很有帮助，请与您的朋友/小组分享。T3】

* * *