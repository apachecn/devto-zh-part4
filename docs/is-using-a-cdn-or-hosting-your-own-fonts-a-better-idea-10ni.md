# 使用 CDN 或托管自己的字体是更好的主意吗？

> 原文：<https://dev.to/prahladyeri/is-using-a-cdn-or-hosting-your-own-fonts-a-better-idea-10ni>

最近，我看到许多文章建议你托管自己的静态资产(尤其是字体和 css)，而不是使用像 [CDNJS](http://cdnjs.com) 这样的全球 CDN。他们引用的论点是，你自己的网站将比 CDN 快得多，因此资产将加载得更快。

但我的观点是，如果我使用 jquery 或 bootstrap 这样的流行库，它们很可能已经通过 CDN 下载到用户的本地机器上了(因为用户可能访问过的许多其他网站都使用 CDN)。所以，网络成本为零！但是，如果我在我的服务器上专门托管一个 jquery.min.js 或 bootstrap.min.css，这将是对网络资源的额外消耗，不是吗？

所以，我认为托管自己的资产是一种反模式，你认为呢？