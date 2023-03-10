# 启用 TLS 变得更加容易了

> 原文：<https://dev.to/ssimontis/enabling-tls-just-got-a-little-bit-easier-1igk>

我们都知道启用 HTTPS 是 web 开发的十诫之一，但这总是一件痛苦的事，而且总是有一个绝妙的理由将它推迟到以后。嗯，不再是了。Mozilla 发布了一个[生成器，它会自动编写你的安全配置](https://ssl-config.mozilla.org/)。

什么？是真的！它支持许多 web 服务器，包括 nginx 和 Apache，数据库连接(PostgreSQL 和 MySQL)，甚至一些云资源(AWS)。还提供了几个网络服务器，其中一些我现在不想搜索。

选择要配置的系统后，您可以从三个安全级别中选择一个。 **Modern** 支持 TLS1.3，如果你不需要任何形式的向后兼容性，它会很棒。不幸的是，这不是我们工作的现实，所以有一个**中级**通用选项可用。有一个**老**选项被列为最后手段，应该避免，除非你已经测试和中间不工作。

您甚至可以控制 OpenSSL 的版本和您正在使用的服务器。我找不到任何服务器版本和 OpenSSL 版本的组合会对我感兴趣的配置产生重大影响，但是您可以查看一下。

我需要查找的唯一选项是 **OCSP 装订**。我仍然不知道我已经掌握了它，但这里有一个机会用我自己的话来解释它:它是对*证书撤销列表*的替代，您的浏览器使用它来确定 SSL 证书是否仍然值得信任，直到根证书的整个证书链。 *OCSP Stapling* 允许您向 OCSP 服务器发出请求，并为用户进行验证。

这听起来像是可疑网站可以利用的东西，对吗？其实这样对你的隐私更好。由于您的浏览器不进行撤销列表检查，证书颁发机构没有来自您的浏览器的记录请求。证书状态请求在 TLS 握手过程中发送到您的浏览器，因此您的浏览器可以验证响应是否合法。它还加快了握手过程，因为你不需要连接到 CA！

如果最后一段听起来像胡言乱语，请告诉我，我会试着写一篇关于安全证书如何工作的文章。这可能会成为一个系列，因为它包含了大量的信息！

这是我在 PostgreSQL 11.3 中得到的关于**中级**的设置，因为 PostgreSQL 目前显然不支持 TLS 1.3。我没有那样做，我也不知道我需要这个配置片段。

```
# 2019-08-27, https://ssl-config.mozilla.org/#server=postgresql&server-version=11.3&config=intermediate
ssl = on

ssl_cert_file = '/path/to/signed_cert_plus_intermediates'
ssl_key_file = '/path/to/private_key'

# curl https://ssl-config.mozilla.org/ffdhe2048.txt > /path/to/dhparam.pem
ssl_dh_params_file = '/path/to/dhparam.pem'

ssl_ciphers = 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384' 
```

Enter fullscreen mode Exit fullscreen mode

如果你错过了，[这里是链接](https://ssl-config.mozilla.org)。拿起你的 LetsEncrypt 证书，开始锁东西吧！如果你不知道如何从 LetsEncrypt 获得免费证书，请在评论中告诉我，我会确保写一篇文章来帮助你。

大家配置愉快，恭喜又一个周一活下来了！