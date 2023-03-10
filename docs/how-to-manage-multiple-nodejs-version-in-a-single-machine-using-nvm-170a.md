# 如何使用 nvm 在一台机器上管理多个 nodejs 版本

> 原文：<https://dev.to/rubiin/how-to-manage-multiple-nodejs-version-in-a-single-machine-using-nvm-170a>

您是否曾经遇到过这样的情况:对于不同的项目，您必须使用不同的 nodejs 版本。或者您希望同时拥有稳定版本和最新版本。在需要的时候安装特定的版本似乎是一个可行的解决方案。但是如果经常需要多个节点版本呢？上述过程似乎有点不合逻辑。所以 NVM 又名节点版本管理器来拯救你的机器，让你有一个以上的节点版本，所以你可以切换版本只需一个命令。对于 Mac 和 Linux，链接是[http://github.com/creationix/nvm](http://github.com/creationix/nvm)。对于 windows【http://github.com/coreybutler/nvm-windows T2】的。你所要做的就是根据你机器的操作系统去链接并下载最新的版本。
入门
在你的机器上安装了 nvm 之后，打开终端或者 cmd，输入

```
nvm list 
```

Enter fullscreen mode Exit fullscreen mode

列出计算机上安装的所有节点版本。要安装特定的节点版本，请使用

```
nvm install <node version number> 
```

Enter fullscreen mode Exit fullscreen mode

例如，

```
nvm install 10 
```

Enter fullscreen mode Exit fullscreen mode

要使用机器上安装的特定节点版本，请执行

```
nvm use <node version> 
```

Enter fullscreen mode Exit fullscreen mode

要卸载一个节点版本，请执行

```
nvm uninstall <node version> 
```

Enter fullscreen mode Exit fullscreen mode

您还可以传入可选参数，该参数是您希望使用 install and uninstall 命令安装或卸载的 32 位或 64 位版本。
输入
获得帮助

```
nvm -help 
```

Enter fullscreen mode Exit fullscreen mode

快乐的节点