# 动物的占位符图像。一个简单的 PHP 服务。

> 原文：<https://dev.to/codestuff2/placeholder-images-of-animals-a-simple-php-service-181m>

我创建了一个有趣的占位符图片服务，名为 Wildcard.fun。

[https://wildcard.fun](https://wildcard.fun)

我们的想法是创建一个服务，它可以生成你想要的任何尺寸的图像作为占位符，并根据这些尺寸调整图像的大小/裁剪图像。

对于应用程序的基本结构，我使用了 vanilla PHP 和一个名为“PHRoute-Fast request router for PHP”的快速路由器。

[https://github.com/mrjgreen/phroute](https://github.com/mrjgreen/phroute)

为了根据请求调整大小、裁剪和压缩图像，我使用了 ImageMagick。

这些图片也来自 Unsplash.com。

[![image of animal](img/2e2ca1d5d20648ab65156becb011ff9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CcUitz-u--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://wildcard.fun/r/400/400)

试试看！