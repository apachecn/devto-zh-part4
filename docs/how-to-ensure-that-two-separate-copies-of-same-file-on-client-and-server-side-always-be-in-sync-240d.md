# 如何确保同一文件在客户端和服务器端的两个独立副本始终保持同步？

> 原文：<https://dev.to/aviral14/how-to-ensure-that-two-separate-copies-of-same-file-on-client-and-server-side-always-be-in-sync-240d>

实际上，我已经用 dnslib 用 python 制作了一个 dns 服务器。dns 服务器首先在其自己的字典中检查域，如果找到域，它返回 ip 地址，如果没有找到，它从外部 dns 获取 IP 地址。

我还建立了一个门户网站，人们可以使用它在 dns 服务器上注册自己的域名。为此，域和 ip 地址被输入到 dns 服务器的字典副本中。

现在使用一个 TCP 连接，我正在考虑将在门户字典中所做的更改发送到 dns 服务器字典中。

但是我担心，如果有一段时间客户机字典的数据不能到达主机字典，那么这两个字典就会保持异步。

解决这个问题最有效的方法是什么？