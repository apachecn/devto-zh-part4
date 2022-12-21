# 解决方法:杀死 Kibana 导致 Elasticsearch Down / ELK 7.2

> 原文：<https://dev.to/peepeepopapapeepeepo/workaround-kill-kibana-cause-elasticsearch-down-elk-7-2-4kd9>

事情是这样的..(笑声)..(笑声)..昨天，当我坐在 ELK(K)堆栈 7 上时。2 新鲜的！新的！穿上！吉儿！我就遵循了我做过的标准作业阶段，即 install elasticsearch，性能测试，可执行安全性测试，然后再进行一次性能测试。

一切都快好了，直到意外发生

[![img](img/7387a2240b49546d9e02555590fc2fac.png)](https://i.giphy.com/media/2vqB3SPyzhSGO6ORQR/giphy.gif)

太好了！我的天啊！我的天啊！弹性研究进展到哪去了？死了！我的天啊！我的天啊！为什么死了！error 也没有

经过一段时间的搜寻，我发现..

> **真理只有一个**真理

[![](img/13c5553c80381320defb80f0ae67bd15.png)](https://i.giphy.com/media/nkbHJf196TV7y/giphy.gif)

嘭嘭嘭嘭！我的天啊！我的天啊！那是柯南的酒吧

* * *

[![](img/d24b1e20c0c01de6286a4e66155efe5f.png)](https://i.giphy.com/media/3o6UBp9jPYK7VI83II/giphy.gif)

嘭嘭也！还没戒！我的天啊！我的天啊！我的天啊！那是柯南奥布莱恩

* * *

好吧！好吧！好吧！音乐来了...

[![](img/bf4483f30e4e25000336d0079d3f2f08.png)](https://i.giphy.com/media/lITcaDWgInpra/giphy.gif)

那个封闭的房间里的凶手就是基班那的剧本！我的天啊！我的天啊！但原因是什么？

由于 kibana 没有使用 root 执行，因此无法创建足够的/var/run 文件 pid。我们命令`systemctl stop kibana``systemctl restart kibana`或`systemctl restart kibana`。它就去了 kill pre。

如果 stop 直接通过 init 脚本，将不会产生任何影响。

case，我想应该只是 kibana 7 .只有 2 因为我的另一个集群是基班纳 7。1 找不到这个问题

让我们来看看我遇到的环境。
t0㎡。

```
OS: RHEL7.6
Kibana version: 7.2.0
Elasticsearch version: 7.2.0 
```

弹出的解决方案

1.  `systemctl stop kibana`
2.  `systemctl stop elasticsearch`
3.  向/etc/kibana/kibana/kibana 添加`pid.file: /var/run/kibana/kibana.pid`。yml
4.  编辑/etc/initd/kibana，由`pidfile="/var/run/$name.pid"`改为`pidfile="/var/run/$name/$name.pid"`。
5.  `systemctl edit kibana`，然后把这个文本放进去。

```
 [Service]
   RuntimeDirectory=kibana 
```

1.  `systemctl daemon-reload`
2.  `systemctl start elasticsearch`
3.  `systemctl start kibana`

这样就可以了(～◌◍)～

[![](img/d565b8a59c2a7e8cbd0b2dc5cda5b3a1.png)](https://i.giphy.com/media/ReImZejkBnqYU/giphy.gif)

* * *