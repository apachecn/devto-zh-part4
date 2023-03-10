# API 网关

> 原文：<https://dev.to/vikashagrawal/api-gateway-1hjn>

# 代理

代理是运行在同一台计算机或不同计算机上的专用计算机或软件系统，充当网站请求者和实际 web 服务器之间的中介。
代理类型:
·转发代理:帮助用户连接到网站。
反向代理:使用:

```
o To allow load balancing between the server.
o To stream internal content to its user. (SSO page)
o To block some particular website.
Nginx 
```

Enter fullscreen mode Exit fullscreen mode

# 负载平衡器:

随着许多服务器被配置为上游，您可以为这个上游定义 proxy_pass。
负载平衡器有多种类型:

```
o Round Robin: this is by default.
o least_conn: the one with the least client connection will be picked up.
o ip_hash: each of the client has some unique key, the first few characters of this key works as server chooser. It helps in speeding up the process. 
```

Enter fullscreen mode Exit fullscreen mode

为了提供高可用性负载平衡器，您还可以插入外部负载平衡器。
缓存

```
o Various cache related information like path, key, validity can be configured to improve the performance.
o User data is suggested not to be cached to avoid sharing one user data with other user. 
```

Enter fullscreen mode Exit fullscreen mode

# API 网关

它用于管理 API，也称为翻译器，包括
将多个 API 组合成 1 个
将多个 API 的响应组合成 1 个
在任何 API 调用出错时提供默认数据/消息
安全管理