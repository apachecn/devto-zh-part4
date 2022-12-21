# Ubuntu 升级按钮不起作用

> 原文：<https://dev.to/get_njoshi/ubuntu-upgrade-button-does-nothing-54fg>

大约一个月以来，我一直被从 18.10 升级到 Ubuntu 19.04 的通知轰炸，所以我终于找到机会做了，却发现软件更新程序中的“升级”按钮什么也没做。

所以我开始谷歌需要做什么。一切都指向我用
更新已安装的软件包

```
>  sudo apt-get update && sudo apt-get upgrade 
```

这似乎返回“0 个新安装，0 个删除，1 个保留”。在这种情况下，libodbc1 被保留，必须使用下面的命令强制安装 libodbc1 包。

```
>  sudo apt-get install libodbc1 
```

只有在这之后，Ubuntu 中的升级按钮才开始工作。在这一点上，我还没有弄清楚为什么它没有以正常的方式升级，但如果有人遇到这样的困境，Ubuntu 升级按钮只是关闭窗口而不做任何事情，这往往与更新软件包有关。如果有任何软件包被保留或没有升级，它们将必须单独安装。