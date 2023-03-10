# 从终端启动 HTTP Web 服务器的 3 种不同方式

> 原文：<https://dev.to/kardelio/3-different-ways-to-start-a-http-web-server-from-the-terminal-4am3>

在这个相对快速的视频中，我想展示 3 种非常快速且不同的方法，您可以从本地机器上的终端在指定的文件夹中启动 HTTP Web 服务器，这样您就可以为网络上的其他机器或您自己提供内容。这对 web 开发人员尤其有用，这样可以快速设置开发服务器进行开发和测试...

3 种不同的方法使用:

*   服务器端编程语言（Professional Hypertext Preprocessor 的缩写）
*   计算机编程语言
*   NodeJs

**带信息的完整视频(或向下滚动阅读...)**
[https://www.youtube.com/embed/tdC5jo7GaRw](https://www.youtube.com/embed/tdC5jo7GaRw)

对于那些喜欢阅读的人，让我简单地展示一下命令...

PHP 有一个内置的网络服务器，你可以这样触发它:

```
php -S 0.0.0.0:8080 
```

(更多信息请访问此链接:[https://www . PHP . net/manual/en/features . command line . web server . PHP](https://www.php.net/manual/en/features.commandline.webserver.php))

**Python** *(版本 3)*
Python 也有一个内置的 web 服务器，你可以这样触发:

```
python -m http.server 8080 
```

(更多信息请访问此链接:[https://docs.python.org/3/library/http.server.html](https://docs.python.org/3/library/http.server.html))

**NodeJS**
Node 有一个名为“http-server”的包，需要安装后才能使用这个命令，可以用这个命令来完成:

```
npm install http-server -g 
```

这会在您的机器(-g)上全局安装这个包，这样您就可以在整个系统中的任何地方使用它，然后实际启动服务器的命令是:

```
http-server -p 8080 
```

(更多信息请访问此链接:[https://www.npmjs.com/package/http-server](https://www.npmjs.com/package/http-server))

**Outro**
当然，使用所有这些命令，您可以将端口号(每个示例中的端口号都是 8080)更改为您选择的端口号，然后一旦您运行了这些命令，您就可以进入您的浏览器并转到地址:
**localhost:8080** 或 **127.0.0.1:8080**
与您的 web 服务器和位于您触发命令的文件夹中的内容进行通信， 它也可以被网络上任何想要访问您的 web 服务器的设备访问，所以请记住这一点！
...

我也在努力拓展我的新 YouTube 频道...

咳嗽
链接:[https://www.youtube.com/channel/UCMz9lmndR0BEqi70kM_ioyA](https://www.youtube.com/channel/UCMz9lmndR0BEqi70kM_ioyA)T3】咳嗽

所以，请给我尽可能多的反馈和批评，以便我可以改进，如果你喜欢它或我做的任何其他视频，请分享它，这真的会有帮助！

对于你在这里或我的任何其他视频中看到的任何东西有任何问题，或者即使你对未来的视频有想法，请在 twitter 上联系 https://twitter.com/ben_kadel

谢谢！