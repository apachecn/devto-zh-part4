# 在 macOS 终端中切换代理

> 原文：<https://dev.to/therealdarkmage/toggle-proxies-in-macos-terminal-1f8j>

macOS 用户界面中最大的烦恼之一是在普通互联网和代理连接之间切换的相对不舒服。你必须:

```
1\. Go to "System Preferences"
2\. Go to "Networking"
3\. Click your connection (Probably Ethernet or Wi-Fi)
4\. Click "Advanced"
5\. Click "Proxies"
6\. Click "Web Proxy" and enter your info if its the first time setting up and check the box
7\. Click "Secure Web Proxy" and enter your info if its the first time setting up and check the box
8\. Click "OK"
9\. Click "Apply" 
```

Enter fullscreen mode Exit fullscreen mode

九个该死的步骤就为了切换你的代理？！？！我不会接受这成为我工作流程的一部分，你也不应该。

谢天谢地，一个叫 sryze 的家伙写了一个快速 bash 脚本来为我们处理切换:

[https://gist . github . com/sryze/1 f77d 803023 b 9586 E6 c 565 b 634 e 10787](https://gist.github.com/sryze/1f77d803023b9586e6c565b634e10787)

我已经分出了他们的要点，并将其更新为更加一般化:

[https://gist . github . com/mikdesu/6b 0040 BD 419 c900 d11c 07 F11 EDB 2 ce 1 e](https://gist.github.com/mikedesu/6b0040bd419c900d11c07f11edb2ce1e)

对我来说，打开或关闭现在是一行命令:

```
$ ./proxy.sh Wi-Fi 8080 on
$ ./proxy.sh Wi-Fi 8080 off 
```

Enter fullscreen mode Exit fullscreen mode

通常，我只需要在终端中按“up up enter ”,这是我的右手可以自己完成的事情！:D

感觉很好，采取这一大堆烦人的过程，并减少到几个按钮按下。

现在我可以轻松地回去找虫子了！