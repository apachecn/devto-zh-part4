# 使用 Gatsby + Now + GitHub 进行自动部署

> 原文：<https://dev.to/spukas/auto-deployments-with-gatsby-now-github-2jhf>

我的博客站点是在 Gatsby 框架上运行的，每一篇新的文章，我都在 Zeit Now 平台上部署最新的版本。做同样的过程令人厌倦:更改、构建、部署，将我的域别名化为新的部署地址。自动化流程的时间到了。最终目标是发送一个新帖子。md '文件到 GitHub，拿瓶啤酒，让机器做剩下的事。

顺便说一句，如果你不熟悉 Zeit，他们现在的部署平台是令人难以置信的，我非常喜欢它。开始使用它很容易，文档写得很好，并且它有高达 100GB 的免费带宽。此外，它们支持域别名；如果您拥有一个域，请将 Zeit Now DNS 地址添加到您的域设置中，并将您的部署地址与您的域挂钩。我说过这是免费的吗？

我假设现在您已经有了一个 Zeit 帐户，并且在您的终端上安装了 CLI。

### 立即用 GitHub 连接

要立即连接 GitHub，请进入 Zeit 帐户设置，然后将 Now 应用程序安装到 GitHub 中。

[![Install Now app into GitHub](img/c140ec27eaddfa2bc7c2752ecbca37fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dH2HMN0G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0a4nkj2vqnsiu61i17ro.png)

### 定义构建脚本

对于现在的部署，必须在`package.json` :
中定义一个脚本

```
{  "scripts":  {  "build":  "gatsby build",  "now-build":  "npm run build"  }  } 
```

并且为了构建和部署项目，它必须包含`now.json`设置文件，其中包含关于如何以及在哪里构建它的指令:

```
{  "builds":  [{  "src":  "package.json",  "use":  "@now/static-build",  "config":  {  "distDir":  "public"  }  }]  } 
```

### 添加自动域名别名(可选)

Zeit Now 的一个很酷的特性是自定义域名别名。您可以通过几种不同的方式进行设置:

*   在`now.json`中添加以下内容:

```
{  "alias":  "spukas.lt"  } 
```

构建完成后，现在会将您的自定义域分配给该构建

*   在 Zeit 仪表板中，选择项目并选择 Domain 选项卡(注意，仪表板中必须至少有一个项目的构建)。在域控制面板中，您将看到添加自定义域的选项:

[![Zeit Now Domain dashboard](img/c140ec27eaddfa2bc7c2752ecbca37fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dH2HMN0G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0a4nkj2vqnsiu61i17ro.png)

### 享受您的体形

就是这样。现在在你的项目中做出改变，提交并推动它们。GitHub 将构建一个项目，现在将它部署到他们的平台上，并为您的自定义域设置一个别名。生活又变得美好了。