# 在 Docker Swarm 中使用反向代理作为 Nginx 时，获取容器中客户端的远程 Ip 地址

> 原文：<https://dev.to/sshivasurya/get-remote-ip-address-of-the-client-in-container-when-using-reverse-proxy-as-nginx-in-docker-swarm-43ie>

在普通服务器实例中运行时，获取远程 IP 地址并转发给内部服务并不是什么大不了的事情。但是，当你使用 Docker Swarm 进行设置并考虑扩展时，这非常令人沮丧，我花了一整天的时间来解决这个问题。

Docker 群组网络覆盖不提供对客户端 IP 地址的访问，客户端 IP 地址通常被视为 L4 层。GitHub([https://github.com/moby/moby/issues/25526](https://github.com/moby/moby/issues/25526))上正在进行一场巨大的讨论，评论爆炸。为什么？

1.  您不能监控用户访问和日志位置
2.  IP 级别检查的速率限制
3.  Prometheus 等监控工具需要精确的客户端 IP 来提供更好的分析和监控
4.  证明特定国家的信息，如定价，支付，时区，甚至更个性化的地理内容。

请解答，

1.  硬件级->添加 L7 负载平衡器作为物理机(服务器实例)并终止 SSL，将 IP 地址为 x-real-ip 或 x-forwarded-for 的流量转发到代理后面的内部服务或应用程序

2.  群集解决方案(解决方法完全违背了 Docker 群集和负载平衡路由的目的)

```
services:
  load_balancer:
    image: nginx:some_version
    deploy:
      mode: global
      constraint:
         - node.role == worker ( It's upto your wish )
    ports:
      - target: 80
        published: 80
        protocol: tcp
        **mode: host**
      - target: 443
        published: 443
        protocol: tcp
        **mode: host**
    networks:
      - your_internal_overlay_network
      - your_external_overlay_network

networks:
   your_internal_overlay_network:
   your_external_overlay_network:
     - external:
         name: your_external_overlay_network ( create with docker network command ) 
```

这将简单地允许 nginx 容器接收 80 & 443 中的流量，并转发给内部网服务。但问题是，您不能仅使用 docker scale 命令来扩展此容器，该命令绑定到 80 和 443 一次，并且当应用程序的流量增加时，不会在多个容器之间共享负载。没那么糟糕，但你可以平衡相当高的流量，如 3K 请求/分钟，这取决于机器。现在，您的 nginx 接收真正的客户端 Ip，您可以在内部转发它进行处理。

替代的高成本解决方案:

切换到负载平衡器，如 ELB ( Aws 负载平衡器)来管理您的证书和 SSL 卸载，并将您的群代理置于 ELB 之后。给了这个选项，你就给了 AWS 一把锁，当你从这个平台上迁移的时候，它会造成混乱。)

如果你有任何疑问，请告诉我。对接愉快！