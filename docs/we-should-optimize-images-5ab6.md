# 我们应该优化图像

> 原文：<https://dev.to/1000ch/we-should-optimize-images-5ab6>

史蒂夫·索德斯在高性能网站上说，

> 80-90%的最终用户响应时间花费在前端。从那里开始。

Web 性能主要取决于前端。意思是让网络更快；我们必须优化前端。例如，它包含以下因素:

*   最小化[往返时间](https://developers.google.com/speed/docs/insights/mobile)和[请求开销](https://developers.google.com/speed/docs/insights/EnableCompression)
*   最小化[有效载荷大小](https://developers.google.com/speed/docs/insights/PrioritizeVisibleContent)
*   [优化浏览器渲染](https://developers.google.com/speed/docs/insights/PrioritizeVisibleContent)
*   还有其他人…

最小化有效载荷大小是最容易应用的方法之一。为了减少数量和有效载荷大小，我们必须最小化 HTML、CSS 和图像文件。值得注意的是，在大多数情况下，图像的文件大小占用了大部分带宽。所以我给你介绍一些图片优化的工具。

## 用于图像优化的 GUI 工具

*   [ImageOptim](https://imageoptim.com/mac) —图像优化器，包含 PNGOUT、Zopfli、Pngcrush、AdvPNG、extended OptiPNG、JpegOptim、jpegrescan、jpegtran 和 Gifsicle。
*   [ImageAlpha](https://pngmini.com/) —通过将 24 位 png 文件转换为 8 位来减小文件大小。
*   [WebPonize](https://webponize.org) —一款将图片转换成 WebP 的 Mac OS 应用。
*   SVGO 失踪的 GUI。
*   [Squoosh](https://squoosh.app/) -在浏览器中用不同的编解码器压缩和比较图像。

## 图像对比

它们是 GUI 应用程序，因此您可以使用它们轻松地对图像进行压缩。以下是 PNG 图像压缩的示例结果:

### 未压缩的 PNG(71834 字节)

[![Uncompressed PNG (71,834bytes)](img/09bc5ec11ba3cf25bf79a4590ebe02b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m-Jg4FXM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v70syevstzvjx4spj9df.png)

这是未经压缩的原始 PNG 图像。这个图像是 24 位的，包含很多元信息，比如位置、日期和时间。

### 用 ImageAlpha & ImageOptim 压缩 PNG (28，369 字节)

[![Compressed PNG (28,369 bytes) with ImageAlpha & ImageOptim](img/6113376d3f38c32fa59c90a493ac2b17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VxlO55tO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gut0mn3w56fq49bict0x.png)

这是用 ImageAlpha 和 ImageOptim 压缩的 8 位转换的 PNG。看起来没有退化。

### 用 ImageOptim 优化 JPEG (213171 字节)。元已删除。但是没有应用有损压缩。

[![Optimized JPEG (213171 bytes) with ImageOptim. Meta is removed. But lossy-compression is not applied](img/998055f9863fca566e56878eed97786d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_Zib85E3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/27m3rb7refxeb2olg1fx.jpeg)

作为另一个例子，这是一个 JPEG。使用 ImageOptim 移除 Meta，并且不应用有损压缩。

### 用 JPEGmini & ImageOptim 压缩 JPEG (71874 字节)

[![Compressed JPEG (71874 bytes) with JPEGmini & ImageOptim](img/c09d66141c4f4229a06582d1323f1d7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1CgETeKs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/a6gvgpe6hp3t0oemmas4.jpeg)

这张是用 JPEGmini 压缩的。它看起来不会太差，文件大小大约是原来的 34%(减少了 66%!！！).包含许多颜色的照片会被降级，所以我们必须检查压缩后的图像。有点烦，但是值得。

## CUI 图像优化工具

我还想要 CLI 工具，所以我创建了这个工具作为 grunt task 和 gulp one。

*   [grunt-image](https://github.com/1000ch/grunt-image) —优化 PNG、JPEG、GIF 图像，无需图形用户界面。
*   [一饮而尽——图片](https://github.com/1000ch/grunt-image) —一饮而尽。

你可以使用它们优化 PNG，JPEG，GIF 图像。安装？如果你用过 Grunt 或 Gulp，这很简单。(我猜你已经用过 them☺了)

```
# download them using npm
$ npm install —-save-dev grunt-image
$ npm install —-save-dev gulp-image 
```

详细的设置在项目的 GitHub 库中。

## WebP 是 Google 推出的新图片格式

[WebP](https://developers.google.com/speed/webp/) 也是牛逼。这是谷歌的一种新的图像格式。上面写着，

> 与 PNGs 相比，WebP 无损图像的尺寸[比](https://developers.google.com/speed/webp/docs/webp_lossless_alpha_study#results)小 26%。与同等 SSIM 指数的 JPEG 图像相比，WebP 有损图像的尺寸[要小 25-34%](https://developers.google.com/speed/webp/docs/webp_study)。

Chrome 和其他包含 [Chrome 框架](https://dev.chromium.org/developers/how-tos/chrome-frame-getting-started)的浏览器都支持 WebP。Safari 目前不支持 WebP。不过有意思的是，iOS 上的 Chrome 是能够显示 WebP 图片的(好像有 Chrome 边框)！

WebP 从[这里](https://developers.google.com/speed/webp/)开始可用。我想给你看 WebP 图像，但遗憾的是它在媒体上不可用。但是，在我的测试中，转换成 WebP 之前的 PNG 文件大小是**28369 字节**。转换后 WebP one 的文件大小为**17382 字节**，看起来不降级耶！

* * *

如果你以前没有考虑过图像优化，你应该应用优化来获得更好的网页性能。最后，我推荐以下文章来了解更多关于图像的信息:

*   [给巴布亚新几内亚一个机会](http://www.phpied.com/give-png-a-chance/)—[斯托扬·斯特凡诺夫](http://www.phpied.com/bio/)
*   [移动 ISP 图像再压缩](https://calendar.perfplanet.com/2013/mobile-isp-image-recompression/)—[Kornel lesiński](https://kornel.ski/)
*   [png 8——明显的赢家](https://www.sitepoint.com/png8-the-clear-winner/)——作者[亚历克斯·沃克](https://www.sitepoint.com/author/alex-walker/)
*   [有效的巴布亚新几内亚](https://calendar.perfplanet.com/2010/png-that-works/)—[科尔内尔·莱西斯基](https://kornel.ski/)
*   [面向网络开发者的图像压缩](https://www.html5rocks.com/en/tutorials/speed/img-compression/)—[柯尔特·麦克安利斯](https://www.html5rocks.com/en/profiles/#coltmcanlis)
*   [WebP 介绍](https://medium.com/@1000ch/introduction-to-webp-b593dfe1d1d1) —由我

感谢您的阅读！