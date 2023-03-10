# 如何将您的 Angular 应用程序链接到 MDBootstrap

> 原文：<https://dev.to/josylad/how-to-link-your-angular-app-to-mdbootstrap-1and>

Angular 是最好的框架之一，当它与各种第三方工具和框架(如 MDBootstrap)集成时，可以用来做更多令人惊叹的事情。

在我们继续之前，我想让您知道 Bootstrap 和 MDBootstrap 之间有一点细微的区别。

### 什么是 MD 自举

MDBootstrap 简单来说就是材料设计 Bootstrap 它是原始引导 CSS 框架的扩展版本。这是灵感来自材料设计的 Bootstrap 版本。

***MDBootstrap 将 Bootstrap 向前推进了一步，提供了更多令人敬畏的样式和 CSS 效果，可以集成到您的 Angular 应用程序中。*T3】**

就我个人而言，我是 MDBootstrap 的忠实粉丝，我经常在我的项目中使用它。

与原始引导程序一样，MDBootstrap 有许多方法可以将其集成到您的应用程序中，其中一些方法是:

1.  将 MDBootstrap 下载到您的本地计算机，并将其包含在您的 App 文件夹中。
2.  使用 MDBootstrap CDN 链接。

**注意:** MDBootstrap 基于 Bootstrap 框架构建，因此，如果您将 MDBootstrap CDN 集成到您的应用中，原始 Bootstrap 中的所有特性、风格和功能都可以开箱即用。

在本文中，我们的重点是在我们的 Angular 应用程序中使用 MDBootstrap CDN。

### 如何在 Angular Apps 中集成 MDBootstrap CDN

#### **第一步。**

进入 MDBootstrap 网站([https://mdbootstrap.com/](https://mdbootstrap.com/))，点击页面顶部的**“入门”**按钮。这将带您进入 MDbootstrap 入门页面。

[![](img/5f10584d3b84d70f18e4177ee6e50c0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pKbN2VdA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AzAO1eZeDWgxKE47HJApD0A.png)

#### **第二步。**

在入门页面上，向下滚动到“ **Get it** ”部分，您将在其中找到各种 MDBootstrap 集成方法。点击 **GITHUB，CDN，NPM** 版本。

[![](img/eb2fd737737885ac4968f7c41a28277e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--agHk3V03--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aul5aDGJgKRSWcTVDqtR96w.png)

#### **第三步。**

单击上面的按钮后，您将看到一个弹出窗口，要求您选择所需的集成方法，只需**选择 CDN 链接**，这将带您进入一个新页面，您将在其中获得 MDBootstrap CDN 链接。

[![](img/bb57b1a8d381848b695dfbd93d30983d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SO4KpU0A--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2ATZULXJQ9ZMX09hGYxByLEQ.png)

#### **第四步**

在 MD Bootstrap CDN 页面上，向下滚动一点，找到 MDBootstrapCDN CSS 链接和 JS 链接。

[![](img/c80f8f24e931769ddf381f978270ddde.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gjOniTNY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2APJapmDsgM3McP4xl6fWqbw.png)

复制提供的 CSS 链接，**确保正确复制所有链接**。

#### **第五步。**

在你最喜欢的代码编辑器(Atom，VS code，Sublime 等)中打开你的 Angular 应用。

在你的 angular App 根文件夹(src/app)中寻找 **"index.html"** 并打开。

[![](img/d5e7e3c4492792e1c5723a9c781d161c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y-VwOONd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/904/1%2Ah-M2r9_BqY5WFVfdMLYOyw.png)

#### **第四步。**

在**index.html**文件中找到结束头标签( **< /head >** )，将你从 Bootstrap 网站得到的链接粘贴到结束头标签的正上方。

**注意:**我建议您集成 MDBootstrapCDN CSS 链接和 JS 链接，因为一些 MDbootstrap CSS 函数依赖于 Javascript/Jquery 来工作。

[![](img/c86d8a974088be6032b302ef2c58c3ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V3W_TF3x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AUHolIv7Y8p29mzw_C3zARA.jpeg)

#### **第五步。**

在你的应用程序中添加一些 MDBootstrap 样式和类来测试它是否有效。

#### **第六步。**

你成功了！没什么可做的了。

现在，您应该已经成功地将 Angular 应用程序链接到了 MDBootstrap。

### 终注。

如果你正确遵循了上面的步骤，你的 angular app 应该可以和 MDBootstrap 完美配合；如果不起作用，请再次执行这些步骤，检查您是否遗漏了什么，并确保您将正确的 MDBootstrap CDN 链接复制并粘贴到您的应用程序 index.html 文件中。

如果您发现这很有帮助，请与您的朋友/小组分享。T3】

* * *