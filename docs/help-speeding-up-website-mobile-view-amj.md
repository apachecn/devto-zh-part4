# 帮助加速网站/移动视图

> 原文：<https://dev.to/johanneslichtenberger/help-speeding-up-website-mobile-view-amj>

大家好，

在我即将发布 Siri xdb 1 . 0 . 0 版本之前，有人能帮我加速我的网站[1]吗！？太好了。

到目前为止，我所做的是缩放图像(较低比例)，使用 Webp 而不是 JPEG 文件，将 CSS 文件的加载放在 Javascript 之前，并将 GZipping 添加到 Jekyll 配置中。异步或延迟加载 JQuery 内容...

然而，由于我不是前端工程师，我有一个相当困难的时间，弄清楚所有的东西:-)

我想@导入 URL(" font-awesome . min . CSS ")；在主 CSS 文件中也花费了一些时间。

遗憾的是，移动视图似乎不是最佳的:

[https://developers.google.com/speed/pagespeed/insights/?URL = https % 3A % 2F % 2f sirix . io % 2F&tab = mobile](https://developers.google.com/speed/pagespeed/insights/?url=https%3A%2F%2Fsirix.io%2F&tab=mobile)

我使用了一个 Jekyll 主题，为此我主要修改了 CSS 内容:-)

子目〔t0〕约翰尼斯

[1] [https://sirix.io](https://sirix.io)