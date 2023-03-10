# 我如何用 Docker & Docker Compose 替换我所有的开发二进制文件

> 原文：<https://dev.to/aminnairi/how-i-replaced-all-my-development-binaries-with-docker-docker-compose-4m25>

有趣的是，就在不久前，我开始在我的电脑上使用 VSCode。从那时起，我真的很喜欢微软用这个软件做的事情，这也是我开始使用 TypeScript 的时候。他们真的在朝着一个好的方向前进，我不认为微软是唯一一家只想赚钱的大公司，而是一家试图帮助开源社区繁荣的公司，当然同时保持他们的主要目标。但这不是这篇文章的主题。

当我开始在我的 Archlinux 发行版上使用 VSCode 时，我已经在我的操作系统中安装了 Node.js 二进制文件。但由于某种原因，开源版本的 VSCode 使用了一个特殊版本的 Node.js，名为 Node.js Carbon(我真的不知道这和原来的二进制有什么区别，也没有花时间去检查)。所以很明显在安装过程中出现了冲突，说我需要卸载我的`nodejs`并确认安装`nodejs-lts-carbon`版本。此时，我并不太担心，只是点击`ENTER`，直到我安装了我的 VSCode。

但是当我开始更新一些正在进行的项目时，我注意到了一些我以前没有发现的错误。经过一些调查，我终于明白这是我的 Node.js 版本，不符合项目的要求。起初，我很难过，因为这意味着我必须卸载 VSCode，或者使用预编译版本(我不想使用的版本)。所以我继续使用 VIM，并安装了最新版本的 Node.js。

就在这个时候，有人和我谈起了 Docker。这也是我开始更加享受我的发展的时候。

Docker 允许你创建一个容器化的空间，所有安装的二进制文件都与外界隔离，也就是你自己的操作系统。这意味着你可以同时拥有一个版本的`nodejs`、`nodejs-lts-carbon`和任何你想要的版本，而不需要改变你的操作系统，只需要安装 Docker。当然，从那以后，我重新安装了 VSCode，并在我所有的项目中使用 Node.js 和 Docker。我以为这是我所能做的一切，但我错了。

随着时间的推移，我开始获得一些关于 Docker 和 dicovered Docker Compose 的经验，这就像一个 Docker 的脚本化版本，它允许您自动执行您可以使用 Docker 完成的大多数任务，并将它们编写在一个简单的 YAML 文件中。这真的很棒，因为不必编写一些 Bash 脚本来完成这种任务。它非常清晰和标准化，因此您可以将该文件嵌入到您的项目中，并与您的团队共享。您必须安装另一个二进制文件，即 Docker Compose 二进制文件。但是最后，如果你足够适应，你可以去掉所有的开发二进制文件，只需要使用 Docker 和 Docker Compose 就可以做任何事情。

这正是我所做的。我没有更多的 web 服务器，(Apache 或 NGINX)，也没有像 PHP 或 Node.js 等 web 开发二进制文件。每当我想开始一个项目时，我简单地用我所有的需求写一个`docker-compose.yaml`文件，并通过运行`docker-compose up --detach`启动我所有的容器来开始我的项目。

这有一些好处。假设你想测试一个运行 PHP 5 的网站。如果您想测试在将远程 web 服务器升级到 PHP 7 之前是否真的需要对源代码进行一些修改，您可以将您的网站安装在 PHP 7 的 Dockerized 版本中，并在运行中测试它。如果你不得不这样做，你会想在你的 OS X、Windows 或 Linux 操作系统上安装 PHP 7，并可能破坏你的 Apache web 服务器的一些配置，并开始调查这个问题，这最终会导致你使用一些黑暗的工具，使你的操作系统更加混乱。

```
version: "3"

services:
  php:
    image: php7:apache
    volumes:
      - ./www:/var/www/html
    ports:
      - 1234:80 
```

```
$  docker-compose up --detach
$  curl http://localhost:1234
Hello from my website in PHP5! 
```

就是这样！我真的用了你在现实生活中会用到的所有东西来测试这个案例。即使你不知道 Docker 是如何工作的，你也必须承认这比用 MAMP、WAMP 或`[INSERT A WEB STACK HERE]`建立一个完整的环境要容易得多。

在我看来，Docker 与 Docker Compose 的结合是为了让您的项目安心、易于开发并具有更好的可移植性，因为它适用于所有主要的操作系统，无论是 OS X、Windows 还是 GNU/Linux。

如果你想试试 Docker，可以查看官网[这里](https://www.docker.com/)。如果你还有其他问题，请告诉我。谢谢你阅读我的文章，下次再见！