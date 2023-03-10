# 带 SSL 缓冲的 Squid 代理

> 原文：<https://dev.to/suntong/squid-proxy-with-ssl-bump-2khe>

*转自[带有 SSL Bump 的 Squid 代理](https://smoothnet.org/squid-proxy-with-ssl-bump/)(2014 年 1 月 26 日)，略有编辑*。

squid 代理是一个非常强大的 web 代理，可以用于从任何东西到强制门户、重定向、用户认证、日志等等；但是 Squid 在 SSL 方面总是有局限性。在 3.2 版本之前，Squid 处理 SSL 的方法是简单地通过 SSL 加密的流量，因为它无法在不使 SSL 信任链无效的情况下对其做任何事情，并为每个 SSL 连接向用户发出警报。

然而，3.2 以上版本允许更多的控制，允许创建本地证书颁发机构和动态生成链式证书，但请注意，这种方法仍然会使 SSL 信任链无效。

这个方法是关于让 squid 运行 SSL Bump 的。我们需要做的第一件事是确保您使用的 squid 版本高于 3.3。虽然 3.2 版支持这种方法，但从 3.3 版开始，SSL Bump 指令发生了变化。

我们需要做的下一件事是生成一个本地 ssl 证书。

```
# Generate Private Key
openssl genrsa -out example.com.private 2048  

# Create Certificate Signing Request
openssl req -new -key example.com.private -out example.com.csr 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要签署我们的证书签名请求。

```
# Sign Certificate
openssl x509 -req -days 3652 -in example.com.csr -signkey example.com.private -out example.com.cert 
```

Enter fullscreen mode Exit fullscreen mode

一旦完成，

*   将私钥和证书复制到 squid 可以访问的某个位置，
*   确保把你的私人钥匙放在安全的地方。
*   证书需要能够被 squid 代理用户访问，并作为受信任的根证书颁发机构安装。

当 squid 生成证书时，它将每个证书的副本存储在一个缓存目录中，这样它只需偶尔做一次。所以我们需要设置它的证书缓存。

```
# Generate certificate cache
/usr/lib64/squid/ssl_crtd -c -s /var/lib/ssl_db
# Change ownership of the certificate cache
chown squid: /var/lib/ssl_db 
```

Enter fullscreen mode Exit fullscreen mode

这应该解决了大部分外部 squid 问题，让我们进入 squid 配置...

*配置的细节和他们的解释在帖子里省略了。如果你愿意的话，可以检查一下原件。*