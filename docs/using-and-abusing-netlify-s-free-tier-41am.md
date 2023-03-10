# 使用和滥用 Netlify 的免费层

> 原文：<https://dev.to/mcapodici/using-and-abusing-netlify-s-free-tier-41am>

如果你在托管一个网站，你绝对要关心它的速度。它为你的页面提供服务有多快？从用户按下回车键到看到完整的页面需要多长时间？

创建一个快速的网站有很多方面，因为有很多接触点会很慢。第一点是你使用的托管公司、计划和方法。

许多网站是使用 PHP 或 Ruby on Rails 等技术动态提供的。需要这样做的网站通常比使用静态 html 文件的网站要慢，因为服务器有 CPU 工作要做。它需要计算网页的内容，而不仅仅是从硬盘上提供。

如果你能做到这一点，移动静态文件托管将大大提高速度。(或者，使用缓存也会有很大的不同)。

静态文件托管给你另一个你可能没有想到的速度提升。如果你的网站只是一堆愚蠢的文件，这些文件可以从世界各地的许多地方提供。你可以在纽约、巴西和香港安装服务器。不同城市的服务器，由离浏览器最近的服务器提供内容。

这将帮助你克服一个不可避免的限制:光速。如果你的内容来自世界的另一端，而不是“直线前进”,而是通过其他国家的多次跳跃——这将会减慢速度。

如果你的服务器靠近你的观众，速度会更快。这听起来很难做到，这就是为什么你使用 CDN(内容交付网络)来做这件事。Netlify 是一家在 CDN 上提供静态文件托管的托管公司。把你的网站放在那里，无论你的受众在哪里，它都会变得很快。

这对你的访问者都很重要(所以你不会因为太慢而失去他们！)和 SEO——因为 Google 关心你的站点有多快。

Netlify 有一个慷慨的免费层，在网站[https://dealscombined.com.au](https://dealscombined.com.au)上，我们使用它(并滥用它——见下文)来托管来自澳大利亚各地的数千笔交易。请看看，希望你同意这是一个快速加载网站。

我说我们在“滥用”Netlify，因为我们使用它的方式与大多数用户截然不同，也更具侵略性。我们有 8000 个交易页面，我们从服务器推送，而不是让 Netlify 从 Github 获取。这样做的原因是页面每天都会发生变化，因此将源数据保存在 git repo 中是没有好处的，因为它会变得太大而不切实际。