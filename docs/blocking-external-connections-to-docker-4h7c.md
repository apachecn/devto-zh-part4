# 阻止与 Docker 的外部连接

> 原文：<https://dev.to/renanpessoa/blocking-external-connections-to-docker-4h7c>

在 Linux 上，Docker 操纵 iptables 规则来提供网络隔离，默认情况下，**所有外部源 IP 都被允许连接到 Docker 守护进程** :/

要仅允许特定的 IP 或网络访问容器，请在 iptables 文件 **`/etc/sysconfig/iptables`** 中插入以下规则

在这种情况下，我们将阻止端口 80 上的所有连接，只允许办公室 IP。

```
-N DOCKER-USER
-I DOCKER-USER -p tcp --dport 80  -j DROP
-I DOCKER-USER -p tcp --dport 80 -s 185.2.46.131 -m comment --comment "My Office" -j ACCEPT 
```

添加规则后，重启 iptables 和 Docker，检查访问权限。

### 我们在这里做什么

`-N DOCKER-USER` —第一条规则创建 Docker 使用的链
`-I DOCKER-USER -p tcp --dport 80 -j DROP` —阻塞端口 80 的所有连接
`-I DOCKER-USER -p tcp --dport 80 -s 185.2.46.131 -m comment --comment "My Office" -j ACCEPT` —允许办公室 IP

使用`iptables -nL DOCKER-USER`检查规则:

```
[root@server ~]# iptables -nL DOCKER-USER
Chain DOCKER-USER (1 references)
target     prot opt source destination        
ACCEPT     tcp  --  185.2.46.131    0.0.0.0/0            tcp dpt:80 /* My Office */
DROP       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:80
RETURN     all  --  0.0.0.0/0            0.0.0.0/0          
[root@server ~]# 
```

现在端口 80 上的所有外部连接都被阻塞
[![Ok](img/323eedfcc2b8bbc08dcef9485e50632e.png)](https://i.giphy.com/media/D6WuLOKOpR2fK/giphy.gif)