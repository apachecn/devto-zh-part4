# 关于 SSL 缓冲

> 原文：<https://dev.to/suntong/about-ssl-bumping-3acf>

### 前缀

之前的两篇帖子都谈到了 *SSL bumping* ，之前的一篇帖子谈到谷歌可能不是你的朋友，因为你发现的一些可能已经过时了。

会有什么问题呢？

这次让我们重点关注一下 *SSL 撞人*。

### SSL 碰撞

[SSL 碰撞](https://wiki.squid-cache.org/Features/SslPeekAndSplice)实际上已经发展并形成了三个不同的版本:

*   在我的谷歌搜索中，*的第一个*命中的[https://wiki.squid-cache.org/Features/SslBump](https://wiki.squid-cache.org/Features/SslBump)，实际上指向了“*中间鱿鱼 SSL 碰撞*方法，这是三个版本中最古老的*。它支持 SSL 流量的 ICAP 检查。*
**   下一个版本是使用 Bump-Server-First 方法的 [SslBump。它增强了对服务器证书细节的模仿。](https://wiki.squid-cache.org/Features/BumpSslServerFirst)*   最新版本是 [SslBump Peek 和 Splice](https://wiki.squid-cache.org/Features/SslPeekAndSplice) 。这是真实的*透明地拦截 TLS/SSL* 碰撞。*

 *当你访问三个不同版本的官方页面时，这一点会很明显，但我认为指出它们是很好的，因为仅仅看 URL 可能会给你另一种印象。

### 结论

这是我收集事实的博客 [squid 缓存服务器](http://www.squid-cache.org/)系列的一部分，重点是 *SSL 碰撞*部分。接下来，我们将详细了解更多的 SSL bumping 文章。*