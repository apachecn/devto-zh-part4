# 如何更新 Python 如何在 Ubuntu 18.04/18.10 上升级到 Python 3.7

> 原文：<https://dev.to/serhatteker/how-to-upgrade-to-python-3-7-on-ubuntu-18-04-18-10-5hab>

本文的 Dev.to 版本可能不是最新的。您可以访问最初发布的资源:[tech.serhatteker.com](https://tech.serhatteker.com/post/2019-09/upgrade-python37-on-ubuntu18/)查看最新版本。

* * *

## 免责声明

> **警告**
> 编辑时间:UTC 2020-12-28 16:53:45
> 
> 请考虑在您的系统上添加一个新的/多 python 版本，而不是使用下面的方法。根据你想要的 python 版本，看看下面的一篇文章:
> 
> *   [如何在 Ubuntu 上安装 Python 3.8](https://tech.serhatteker.com/post/2019-12/how-to-install-python38-on-ubuntu)
> *   [如何在 Ubuntu 上安装 Python 3.9](https://tech.serhatteker.com/post/2020-09/how-to-install-python39-on-ubuntu)
> 
> **由于以下方法可能导致系统错误**:可能是[断线](https://unix.stackexchange.com/a/410580)或[系统配置错误](https://askubuntu.com/questions/1074763/error-when-trying-sudo-apt-get-update-python-related)。

## [T1】简介](#intro)

在本文中，我们从`python 3.6`升级到`python 3.7`，并将其配置为 python 的默认版本。

我只是想升级我的 python，我发现这有点难。`Python 3.6`是 Ubuntu 18.04/18.10 的默认版本，但最新版本是 Python 3.8。所以最好升级一个主要版本。

让我们开始吧:

## 第 0 步:检查当前 python 版本

运行以下命令来测试当前安装的 python 版本。

```
$ python3 --version 
```

Enter fullscreen mode Exit fullscreen mode

输出如下:

```
python 3.6.8 
```

Enter fullscreen mode Exit fullscreen mode

## 第一步:安装 python3.7

通过键入:
安装 python

```
$ sudo apt update -y
$ sudo apt install python3.7 
```

Enter fullscreen mode Exit fullscreen mode

## 第二步:添加 python 3.6 & python 3.7 更新-替代方案

```
$ sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 1
$ sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.7 2 
```

Enter fullscreen mode Exit fullscreen mode

## 第三步:更新 python 3 指向 python 3.7

默认情况下，`Python 3.6`指向`Python 3`。这意味着当我们运行`python3`时，它将作为`python3.6`二进制执行，但我们希望作为`python3.7`执行。

键入以下命令来配置`python3:`

```
$ sudo update-alternatives --config python3 
```

Enter fullscreen mode Exit fullscreen mode

```
user@ubuntu1804:~$ sudo update-alternatives --config python3
There are 2 choices for the alternative python3 (providing /usr/bin/python3).

  Selection    Path                Priority   Status
------------------------------------------------------------
* 0            /usr/bin/python3.6   2         auto mode
  1            /usr/bin/python3.6   1         manual mode
  2            /usr/bin/python3.7   2         manual mode

Press <enter> to keep the current choice[*], or type selection number: 
```

Enter fullscreen mode Exit fullscreen mode

您应该得到上面的输出。现在键入 2 并按回车键输入`Python 3.7`。记住选择号可能不同，所以选择`Python 3.7`的选择号。

### 替代更新 python 3 指向 python3.7

`/usr/bin/python3`只是一个`symlink`。删除它，做一个新的`symlink`到
T3:

```
$ sudo rm /usr/bin/python3
$ sudo ln -s python3.7 /usr/bin/python3 
```

Enter fullscreen mode Exit fullscreen mode

## 第四步:测试新版 python3

```
$ python3 -V 
```

Enter fullscreen mode Exit fullscreen mode

全部完成！

* * *

**变更日志**

*   2020-03-10:添加声明和警告
*   2020-12-28:添加更新的替代 python 版本