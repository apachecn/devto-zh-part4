# 远程服务器上的 Visual Studio 代码

> 原文：<https://dev.to/johnhenry/visual-studio-code-on-a-remote-server-2dkh>

如今，很容易建立一个 web 服务器，您可以使用几乎任何客户端设备远程编程。

单独使用安全 shell 并不难做到这一点，但是这里有一些方法可以使用 [Visual Studio 代码](https://code.visualstudio.com)编辑器来增强这种体验。

## 定义

*   CL =命令行
*   FS =文件系统

## 先决条件

本教程假设您已经熟悉使用[安全外壳(SSH)](https://en.wikipedia.org/wiki/Secure_Shell) 。

你*应该*至少熟悉使用[密钥认证](https://www.ssh.com/ssh/key/)登录远程服务器。

## 方法一:代码-服务器

[Code-server](https://github.com/cdr/code-server) 创建一个 web 服务器，其中包含一个 web 版本的 Visual Studio 代码实例，您可以通过浏览器访问它。

1.  在您的服务器上安装 code-server。
2.  通过 SSH 访问服务器的 CL。
3.  在服务器上，运行`code-server <path to workspace or directory>`。
4.  您可以通过密码保护的 web 界面访问服务器的 FS 和 CL，网址为:8443

## 方法二:sshcode

Sshcode 的工作方式类似于 code-server(它实际上是建立在 code-server 之上的),但是它使用一个客户端程序来建立 web 服务器。此外，如果你使用 chrome，它会打开一个带有 Visual Studio 代码键绑定的定制窗口。

1.  在客户端安装 chrome 和 sshcode
2.  在客户端上，运行`sshcode <user>@<serveraddress>`
3.  通过 chrome 窗口访问服务器的 FS 和 CL。

## 方法三:Visual Studio 代码

1.  在客户端安装 [Visual Studio 代码](https://code.visualstudio.com/)
2.  安装[远程 SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) 扩展
3.  从扩展菜单，连接@
4.  通过 Visual Studio 代码访问服务器的 FS 和 CL
5.  从扩展菜单，隧道端口从服务器到客户端。

## 下一步

为了补充上述方法，您可能需要了解:

*   使用 [sshfs](https://github.com/libfuse/sshfs) 在本地挂载文件远程文件夹并使用任何你想要的编辑器
*   通过[隧道](https://www.ssh.com/ssh/tunneling/example)访问远程机器上的其他资源。
*   使用 [mosh](https://mosh.org/) 缓解不稳定的远程[移动]连接。
*   以上程序的定制和缺陷。