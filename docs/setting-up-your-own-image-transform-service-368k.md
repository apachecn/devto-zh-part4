# 设置您自己的图像转换服务

> 原文：<https://dev.to/gaijinity/setting-up-your-own-image-transform-service-368k>

# 设置您自己的图像转换服务

### 通过利用 AWS Lamb da 的强大功能创建您自己的无服务器图像处理程序，为 pen nies 创建优化/转换的图像

安德鲁·韦尔奇

[![Image transforms serverless image handler](img/8b2029fbb85ae1e6bcf08cf383c062a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LNuwV-1---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/image-transforms-serverless-image-handler.jpg)

图像转换是现代 web 开发的一个必要部分，因为我们希望将最佳大小的图像交付给客户端浏览器。

无论你使用的是一个服务器网站还是一个 [JAM stack](https://jamstack.org/) 设置，图像操作都是这个过程的一部分。

即使你在使用 JS 的 [Gats 之类的东西，转换图像的工作仍然需要完成，它只是移动到构建时间。我们只是在改变完成的地方。](https://www.gatsbyjs.org/)

<aside>Unfor­tu­nate­ly, image trans­forms are also very proces­sor intensive.</aside>

许多 CMS 和其他服务器端的分布式技术都内置了映像转换。然而，这在本质上意味着您在为您的网站提供服务的同一台服务器上自托管处理高强度的图像传输。

如果您还没有遇到自托管图像转换的问题，请放心，您最终会遇到的。

[![Image transforming optimization problems](img/05018405b63f0fd9cc0472d13e2f570b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QPbvHdB3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/image-transforming-optimization-problems.jpg)

这不是一个成功的秘诀，就像为 mant 人服务一样。我见过一些 web 服务器在图像转换缓存被破坏时崩溃，因为重新创建转换后的图像需要 CPU 处理能力。

此外，大多数内置图像转换系统不包括图像优化阶段。

有关图像优化的方法和原因的更多信息，请查看我在 Craft CMS 文章中的[创建优化图像，或 Addy Osmani 的 composite](https://dev.to/gaijinity/creating-optimized-images-in-craft-cms-2hc5-temp-slug-2354251)[基本图像优化](https://images.guide/)文章。

<aside>“We should all be automat­ing our image com­pres­sion.” — Addy Osmani</aside>

因为图像转换是如此的进程密集型，像 [Imgix](https://www.imgix.com/) 和 [Cloud i nary](https://cloudinary.com/) 这样的服务允许你把工作卸载到他们的可扩展服务器上。这两种服务都很好，但它们也要花钱，而且往往做的比我们实际需要的多。

## 进入 AWS Server less 图像处理器

Ama zon AWS 已经提供了一个[无服务器的图像处理程序](https://aws.amazon.com/solutions/serverless-image-handler/)，它允许你建立一个 AWS Lamb 函数来创建你自己的私有的小图像传输服务，它是便宜的，快速的，并且是由[云前端](https://aws.amazon.com/cloudfront/)内容交付网络(CDN)提供的。

AWS Lamb da 是新一波“无服务器”功能的一部分，它允许你在云中运行代码，而无需维护服务器。它很便宜，而且不含 sle，这在我的书里是一个很好的例子。

下面是 AWS Lamb da pric ing 的样子:

[![Aws lambda pricing](img/d536da870a63652db7cc429ba89cae41.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MBthpzhz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x399_crop_center-center_100_line/aws-lambda-pricing.png)

对于许多小型网站，你可能永远不会离开免费层。

在引擎盖下，[AWS Server less Image](https://aws.amazon.com/solutions/serverless-image-handler/)Han dler 使用的是 [Sharp JS](http://sharp.pixelplumbing.com/en/stable/) ，这是一个为 mant[lib VIPs](https://github.com/libvips/libvips)Image processing lib 构建的 [Node JS](https://nodejs.org/en/) pack age。以下是它的大致外观:

[![Aws lambda overview chart](img/70fb1b2ca3650e2a3ce1c45e4b02d21a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9FDj9sBl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x600_crop_center-center_100_line/aws-lambda-overview-chart.png)

如果你愿意，你可以查看所有的[代码，但是好的一面是你不需要这么做。](https://github.com/awslabs/serverless-image-handler)

## 设置无 AWS 服务器的图像处理程序

所有这些最有价值的部分是，您可以设置自己的 AWS Lamb 函数，而不必深入了解它是如何工作的。

你将需要一些 AWS 的小经验，也许当你吃力地通过 AWS 用户界面/UX 时需要一些舒缓的音乐，但仅此而已！

<aside>This is a point & click no-code solution 🎉</aside>

这是可行的，因为你可以通过 [AWS Cloud For ma tion](https://aws.amazon.com/cloudformation/) 旋转你的服务，它使用预制模板来创建一个包含你的设置的堆栈。这意味着在服务器端点击设置所有东西是非常重要的:

*   你需要有一个 Ama zon AWS 账户；如果你没有，就去买一个
*   转到[无服务器图像处理程序](https://aws.amazon.com/solutions/serverless-image-handler/)页面，点击**在 AWS 控制台中启动解决方案** **，然后完成模板设置:

[![Launch in management console](img/c07df32eab782aee32ddabbbf531f1c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wgPRz5yr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_768x685_crop_center-center_100_line/launch-in-management-console.png)

**1。规格财政模板**

你不需要改变这里的任何东西，除非你想使用你自己定制的模板。点击**下一步**继续。

[![Step 1 specify a template](img/dac4497b2ec2a73d5cec96cf13f50452.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KOs3GedZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x777_crop_center-center_100_line/step-1-specify-a-template.png)

**2。规格信息堆栈详细信息**

这里可能有一些您可以选择更改的内容(不要担心，您稍后也可以返回并更改这些内容):

*   **CORS 选项**——如果您想启用 [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) ，您可以在这里进行配置
*   **图像来源** —您需要列出无服务器图像处理程序可以访问的 [AWS S3 Buck ets](https://aws.amazon.com/s3/)
*   演示用户界面(Demo UI)—这将导致它设置一个演示用户界面，您可以使用它来测试您的 API。这是第一个选择，但是我认为当你开始的时候会很有用。您可以稍后删除演示资源

点击**下一步**继续。

[![Step 2 specify stack details](img/4a55ed384306b0e20c2d51b29c7f7aa0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dBR3MIRD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x972_crop_center-center_100_line/step-2-specify-stack-details.png)

**3。图标 URE 堆栈细节**

你不需要在这里做任何改变，除非你有一个更复杂的 AWS 设置在你的权限和里坡方面。点击**下一步**继续。

[![Step 3 configure stack options](img/3d235a69088022bd5ed8c318eb0ec0dd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SwsVxdtV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x1024_crop_center-center_100_line/step-3-configure-stack-options.png)

**4。回顾**

这里没有什么需要改变的，你可以看到你将要创建的内容的概述。在勾选了**我确认 AWS 云管理可能使用客户名称**复选框后，单击**下一步**继续。

[![Step 4 review](img/8effafe143b02922ee0aa79e148d2b57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vMxZHeVC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x332_crop_center-center_100_line/step-4-review.png)

如果您遇到问题，请使用[无服务器映像处理程序部署指南](https://docs.aws.amazon.com/solutions/latest/serverless-image-handler/welcome.html)作为佳能校准源。

## 使用 AWS 无服务器图像处理器

现在您已经启动了您的服务，假设您也设置了演示用户界面，您将设置以下云前端诊断:

[![Cloudfront distributions](img/575b40fce8ddd443f989fc18843995d7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U8zT6Hzj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x252_crop_center-center_100_line/cloudfront-distributions.png)

*   **图像处理程序分配** —这是您的无服务器图像处理程序分配，您将从您的前端代码访问它，以获得您的优化、转换图像
*   **网站讨论解决方案** —这是由演示用户界面创建的讨论，您可以使用它

虽然如果你创建了演示用户界面，这将为你创建一个 S3 工具箱，但是你实际的网站/ webapp 所需的任何 S3 工具箱都由你自己来创建。

请确保您随后将它们添加到您的[云管理堆栈](https://console.aws.amazon.com/cloudformation)。典型地，你的 S3 桶应该是完全私有的，并且你设置了云锋分布作为通向 S3 桶的大门。

**专家提示:**你不会真的想提供像 S3 钱包里的图片这样的固定资产，它是用来存储的，而不是用来传播的。在 S3 上使用 CDN 分布层，例如 CloudFront。

**专业提示:**如果您在 AWS 中没有看到您知道已经升级的特定类似服务，请确保您处于正确的区域。

如果您将 URL 复制到演示 UI **网站解决方案讨论**，您将获得一个可以玩耍的场地:

[![Serverless image handler demo](img/e4ed3c671be03a39c9dd4a729ec0d15e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--o7syOKZl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x865_crop_center-center_100_line/serverless-image-handler-demo.png)

所以关于 API 有趣的事情是你需要做的就是创建一个 JSON blob 选项:

```
 {
  "bucket": "images.nystudio107",
  "key": "Octagon-Titlarks-House-02.jpg",
  "edits": {
    "resize": {
      "width": 500,
      "height": 300,
      "fit": "cover"
    },
    "grayscale": true,
    "flip": true
  }
} 
```

以下是 JSON 中重要的顶级键:

*   这是源图像所在的 S3 巴克 et 的 id
*   `key` —这是 S3 存储桶中源图像的路径
*   `edits`—[Sharp JS API](http://sharp.pixelplumbing.com/en/stable/api-resize/)的 JSON 认证版本，包含您想要应用到图像的更改

然后在你使用的任何程序明兰语言中，你只要把 JSON 转换成一个字符串，然后 base64 编码成一个 URL:

```
 https://d3dvp96ru4zk60.cloudfront.net/eyJidWNrZXQiOiJpbWFnZXMubnlzdHVkaW8xMDciLCJrZXkiOiJPY3RhZ29uLVRpdGxhcmtzLUhvdXNlLTAyLmpwZyIsImVkaXRzIjp7InJlc2l6ZSI6eyJ3aWR0aCI6NTAwLCJoZWlnaHQiOjMwMCwiZml0IjoiY292ZXIifSwiZ3JheXNjYWxlIjp0cnVlLCJmbGlwIjp0cnVlfX0= 
```

在你的演示用户界面 S3·巴克等人创建的`scripts.js`中有一个如何通过 JavaScript 实现这一点的例子。用其他语言做这件事都很简单。

所有在 [Sharp JS](http://sharp.pixelplumbing.com/en/stable/api-resize/) API 中实现的选项对你都是可用的！在 Sharp JS 中通常不会被链接的所有转换选项都在 JSON for mat 中的`edits`键中。

您可以调整大小、裁剪、旋转、着色、锐化以及一系列其他图像操作，包括在`jpeg`、`webp`、`png`和`tiff`等地垫之间进行转换。

如果您需要，您可以完全控制确切的编码方法，默认情况下，所有图像都针对大小进行了优化。

## Craft CMS 服务器少图像处理程序

这里描述的解决方案适用于您可能使用的任何前端/后端设置。

然而，如果你使用的是[Craft CMS](https://craftcms.com/),[Ima geo mize](https://nystudio107.com/plugins/imageoptimize)plug in 允许你将站点范围内使用的转换方法切换到尤尔想要的，从内置的 Craft 图像转换到 Imgix 图像转换。

版本 1.6.0 增加了一种快速变换方法，可用于上述设置:

[![Image optimize plugin sharp demo](img/29082879f9b1d7612e22a550933e68d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cwc0pA1J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x953_crop_center-center_100_line/image-optimize-plugin-sharp-demo.png)

它的好处是你可以转换到使用差分转换方法，而不需要改变模板。

## 包 ping 起来！

这就是创建你自己的 AWS 无服务器映像包所需要的一切！在个人/演示网站上尝试一下，获得如何设置的信心，然后就可以走了。

与服务相比，它确实非常便宜:

[![Aws lambda bill](img/505de123b2d3f73d4183fd2c9cdb6cda.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RPPKP-8C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x968_crop_center-center_100_line/aws-lambda-bill.png)

是的，你将不得不费力地通过一点不好玩的 AWS UX/UI，并学习一点关于 AWS 的错误…但我认为这绝对值得努力。

无论您使用的是服务器解决方案、JAM stack 解决方案，还是 Gats by 之类的解决方案，您都会适应。无论在哪里进行图像转换工作，一个无服务器的图像处理器都将加速这一过程。

[![Aws lambda setup worth it](img/cd054a4ac8bcfbf39910e29fddb71ecf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fQluytqP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nystudio107-ems2qegf7x6qiqq.netdna-ssl.com/img/blog/_1200x675_crop_center-center_82_line/aws-lambda-setup-worth-it.jpg)

您不仅能够将 CPU 密集型映像优化/转换卸载到您的 AWS Lamb 函数中，而且很可能还可以缩减您的服务器(假设您有一个服务器)。

此外，在这个过程中，你会学到一些 AWS，这对我们的业务来说是一件好事。

祝你转型成功！

## 进一步阅读

如果你想获得新文章的通知，请在 Twitter 上关注[纽约时报 107](https://twitter.com/nystudio107) 。

版权所有 2020 nystudio107。由 nystudio107 设计