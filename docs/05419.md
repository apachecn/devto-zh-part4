# 终结了。我如何解决清漆缓存问题！

> 原文：<https://dev.to/tammalee/busted-how-i-solved-a-varnish-cache-issue-238k>

我不是世界上最伟大的侦探。

老实说，我甚至不是第四好的(T1)，但我没有让这阻止我解决被破坏的清漆缓存(T3)的(T2)之谜！

我们正在做自己的事情，做性能报告，检查数据等，这时我们注意到有几个网站的主页似乎没有被缓存。

我们是怎么知道的？糟糕的加载时间！10+秒？！什么？！

我们需要更多的数据。

# 背景故事

我们主持一个 400+子网站 [WordPress 多站点](https://wordpress.org/support/article/create-a-network/)网络与[先贤祠](https://pantheon.io/)托管。

经过多年断断续续的维护，我们继承了它。

要重构的东西太多了，以至于一个代码清理者估计我们要花 34 年才能全部清理干净。哈哈哈哈*但是真的，我在这里抽泣。*

因此，长话短说，我们在很大程度上依赖缓存来让访问者体验更少的垃圾，同时让我们的代码井然有序。

这是你有时不得不做出的妥协。

# 故障排除

首先，我们通过 Chrome 访问了该网站。很好，但是很慢。然而，主 URL 立即执行了重定向，并将`/en`附加到 URL 上。

啊哈，这是一个使用 [Polylang Pro 插件](https://polylang.pro/downloads/polylang-pro/)在法语和英语之间切换的双语 WordPress 网站。(*你好！我是加拿大人，是吗？我还可以告诉你，我去图书馆的时候带了一个法国葡萄柚和其他一年级的短语。*)为了进行切换，主页重定向到 URL 中带有`/en`或`/fr`的页面。

考虑到这一点，我们从命令行使用`curl`来查看头文件等发生了什么。

```
curl -I https://www.domainname.ca 
```

嗯。我们得到的信息不出所料:

```
cache-control: public, max-age=86400
via: 1.1 varnish
age: 53
x-served-by: cache-mdw17340-MDW, cache-sea1027-SEA
x-cache-hits: 0, 1
x-cache: Miss from cloudfront 
```

缓存控制设置为 24 小时，这很酷。( [Pantheon 有一个插件](https://wordpress.org/plugins/pantheon-advanced-page-cache/)，如果你更新它，它将刷新单个页面的缓存。如果你能使用，长缓存 TTL 是你的朋友。)

我们有一个年龄，这意味着我们有一个工作的缓存。

哼。因此...清漆被击中，但云锋没有？快速检查表明，我们的 Cloudfront 没有配置缓存，这就解释了为什么。

可是...如果我们访问 Varnish 缓存，为什么会得到这么慢的加载时间？

让我们试试重定向的 URL:

```
curl -I https://www.domainname.ca/en 
```

哦哦！这给了我们一些意想不到的信息:

```
set-cookie: pll_language=en; expires=Fri, 31-Jul-2020 19:37:04 GMT; Max-Age=31536000; path=/; domain=.xxxxxxxxxxxxxxx.io
cache-control: public, max-age=86400
via: 1.1 varnish
age: 0
x-served-by: cache-mdw17359-MDW, cache-sea1030-SEA
x-cache: MISS, MISS
x-cache-hits: 0, 0
x-cache: Miss from cloudfront 
```

哼。那个 URL 肯定没有缓存任何东西。( *Age 为零，x-cache 为双未命中，x-cache-hits 为零。*)

但是等等！那个`set-cookie`头球在那里搞什么鬼？这不在主页上...

# 解

Pantheon [有一个关于调试 cookies 和它们的全局 CDN](https://pantheon.io/docs/global-cdn-caching/#debugging-cookies-and-the-global-cdn) 的页面，解释了正在发生的事情。

> 如果在多次请求后，您的输出如下所示(即年龄为 0)，请检查是否正在设置 cookie(使用 Set-Cookie 头)。设置 cookies 将阻止 CDN 缓存该页面

兵兵兵！看起来我们的 Polylang Pro 插件正在使用`set-cookie`头为语言偏好设置 cookie！

如果我们没有用这个插件运行 100 多个网站来处理翻译，我们可能会考虑用一个不同的插件来替换它。

谢天谢地，原来 [GDPR](https://eugdpr.org/) 意味着 Polylang Pro 已经[开发出一种方法来阻止 *pll_language* cookie 设置](https://polylang.pro/doc/is-polylang-compatible-with-the-eu-cookie-law/)。

我们的`wp-config.php`文件被更新以包含这一变化，现在我们的多语言主页正在缓存。

当然，代价是，如果你返回一个网站，它不会记得你的语言偏好，但这似乎是一个小小的牺牲，让网站加载速度加快 8 秒。

`¯\_(ツ)_/¯`

乔安娜·科辛斯卡在 [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的基础照片