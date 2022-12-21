# Docker 容器中的调试烧瓶应用程序

> 原文：<https://dev.to/anquangman/debug-flask-application-in-a-docker-container-3l28>

"我如何调试一个运行在 Docker 容器中的 Flask 应用程序？"如果你在问同样的问题，那么这篇文章可能会帮助你回答这个问题。

注意:这是我第一次向 Dev.to 发帖，请原谅我的英语。

## 调试器:

我们花了你大部分的时间去追踪和杀死 bug，调试器除了让你做得更快之外什么也帮不了你。真正的调试器的主要优势是能够设置断点，并开始逐行或逐函数地单步执行代码，并查看变量如何变化，直到您发现哪些部分没有按预期运行。您可以调试 python web 应用程序，如 Flask、Django，或者在 VSCode 中调试一个 python 脚本。要启动 debug you app，您需要安装 Python 扩展并编写一个描述来告诉 VSCode 启动项目。
安装 [Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)并开始调试。

## 码头工人

Docker 已经成为 Devops 中最流行的技术之一。它提供了许多好处，例如

1.  一致的环境:容器使开发人员能够创建与他人隔离的一致环境。您可以制作几个符合目的的容器，您可以有三个容器用于开发，一个用于测试，一个用于生产
2.  版本控制:容器还可以包含应用程序所需的软件依赖，例如特定版本的编程语言和其他软件库。
3.  在任何地方运行:车辆无关紧要，它在任何地方都能完美运行。
4.  隔离:容器中的依赖项或设置不会影响您的计算机或任何其他可能正在运行的容器上的任何安装或配置。

## Docker 和调试器

对一个应用程序进行 Dockerize 很有趣，但是调试起来会很麻烦。好消息是，你仍然可以使用 VSCode 调试容器中的应用程序，但是这需要更多的工作。您将需要一个名为“ptvsd”的 python 库来完成这项工作。

你可以在 Github
中查看[我的示例代码，我使用的是 *docker 版本 19.03.1* 和 *docker-compose 版本 1.24.1*](https://github.com/anquangman/debugging-dockerized-flask-app)

我将假设您的主应用程序文件首先被称为`app.py`
**，将这些行添加到`app.py` :
的顶部**

```
import ptvsd
try:
    ptvsd.enable_attach(address=('0.0.0.0', 4000))
    ptvsd.wait_for_attach()
except Exception as ex:
    raise e 
```

这将允许您运行 attach debug 到端口 4000，您可以随意更改端口，但一定要将它暴露给本地。

**第二个**，开始你的服务

**第三个**，将此配置添加到`launch.json`T3】

```
{
    "name":"Attach Debug",
    "type":"python",
    "request":"attach",
    "port":4000,
    "host":"0.0.0.0",
    "pathMappings":[{
        "localRoot":"path to where the app.py lives on your local",
        "remoteRoot":"path to where the app.py lives in app container"
    }]
} 
```

*port* 值必须与容器中公开的端口相匹配。

显示时间
[![Alt Text](img/d53b74b8054ec59b4d6ce4bfb8a47388.png)](https://i.giphy.com/media/RJ19FBuZmJ15YGa6mY/giphy.gif)

如果你需要帮助，请随时发表评论。**