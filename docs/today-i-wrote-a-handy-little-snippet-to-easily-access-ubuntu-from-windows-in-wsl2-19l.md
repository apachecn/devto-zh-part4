# 今天我写了一个方便的小片段来轻松地从 Windows 访问 Ubuntu(在 WSL2 中)

> 原文：<https://dev.to/codeluggage/today-i-wrote-a-handy-little-snippet-to-easily-access-ubuntu-from-windows-in-wsl2-19l>

如果你最近没有关注 Windows 新闻，你现在可以在 Windows 中运行 [Linux 了！](https://docs.microsoft.com/en-us/windows/wsl/wsl2-about)

对它的最新更新使它非常有用，因为 Docker 和其他工具现在都得到支持。

已经有[几个](https://github.com/shayne/go-wsl2-host)不同的[解决方案](https://github.com/microsoft/WSL/issues/4150)发布，用于自动访问一个暴露的程序——比如说，你的运行在 Ubuntu 上的节点服务器。这些对我都不起作用，所以我求助于一些传统的好方法。

我使用常规命令来获取 IP: `ip addr`，它会给你这样的结果:

```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: bond0: <BROADCAST,MULTICAST,MASTER> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether b6:5d:52:7b:32:42 brd ff:ff:ff:ff:ff:ff
3: sit0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/sit 0.0.0.0 brd 0.0.0.0
4: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:16:5a:42:cb:c2 brd ff:ff:ff:ff:ff:ff
    inet 172.17.177.74/16 brd 172.17.255.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::215:5dff:fe71:cac1/64 scope link
       valid_lft forever preferred_lft forever 
```

Enter fullscreen mode Exit fullscreen mode

然后我为我们抓住了最重要的部分；`eth0`同`grep eth0` :

```
4: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:16:5a:42:cb:c2 brd ff:ff:ff:ff:ff:ff
    inet 172.17.177.74/16 brd 172.17.255.255 scope global eth0 
```

Enter fullscreen mode Exit fullscreen mode

然后`inet`部分具体用`grep inet0` :

```
inet 172.17.177.74/16 brd 172.17.255.255 scope global eth0 
```

Enter fullscreen mode Exit fullscreen mode

从现在开始，我想只使用 IP 地址，而`awk`真的很擅长这种事情。我把带有`awk {'print $2'}`的那一行的第二个字删掉了:

```
172.17.177.74/16 
```

Enter fullscreen mode Exit fullscreen mode

我总是忘记如何用`awk`拆分一个字符串，但是经过一些搜索，而不是谷歌搜索(专业提示:使用 https://ecosia.org 的[作为你的搜索引擎！用你的搜索来种树，而不是喂谷歌；) )我发现用`awk -F`也可以。所以，为了拆分字符`/`，我们可以做`awk -F '/' '{print $1}'`来获取拆分字符串的第一部分:](https://ecosia.org) 

```
172.17.177.74 
```

Enter fullscreen mode Exit fullscreen mode

因为我倾向于使用 Node，所以默认情况下我总是希望使用端口 3000。经过一番摆弄，我用`awk '{printf`做了这个，用 IP 地址作为参数，像这样:`awk '{printf "%s:3000", $0}'` :

```
172.17.177.74:3000 
```

Enter fullscreen mode Exit fullscreen mode

最后，最重要的是:将结果发送到 Windows 剪贴板，这样我就可以将它粘贴到我的浏览器中(专业提示:支持网络多样性！我用火狐，很牛逼)。微软团队通过在 Ubuntu 中将“exe”文件公开为可管道化的工具，在这方面做得非常好。例如，您可以用`explorer.exe`打开 Windows 资源管理器，用`echo "hello from Ubuntu!" | clip.exe`通过管道连接到剪贴板。

所有这些放在一起，整个代码片段看起来像这样:

```
ip addr | grep eth0 | grep inet | awk '{print $2}' | awk -F '/' '{print $1}' | awk '{printf "%s:3000", $0}' | clip.exe 
```

Enter fullscreen mode Exit fullscreen mode

我把它放在一个名为`copy_ip.sh`的文件中，用`chmod +x copy_ip.sh`使它可执行，现在每当我想从 Windows 访问我的服务器时，我只需`./copy_ip.sh`并把它粘贴到我的浏览器中< 3