# Docker 容器中的节点版本管理

> 原文：<https://dev.to/kabisasoftware/node-version-management-in-docker-containers-42c7>

最近，我遇到了一个 Docker 特有问题的解决方案，我不知道这个问题，而且我认为这个解决方案并不常用。主要是因为你不应该在不知道含义的情况下使用它。尽管如此，知道这一点还是很有用的。

我正在为一个客户构建容器，由于历史原因，这个客户的代码库使用了几个不同的节点版本。作为一名后端/ devops 开发人员，我以前并没有真正使用过`nvm`。

通常我会选择使用 Docker 镜像`FROM node:<version_here>`,但是正在使用的版本(可能有很好的理由)不在 Docker hub 上。如果 Dockerfile 使用`.nvmrc`文件来确定使用哪个节点版本，可能实际上更容易维护。

构建这个容器似乎比我想象的要困难。这是因为 nvm 使用在配置文件中声明的环境变量。这是非常合乎逻辑的，因为它是一个开发工具，您不希望切换环境对整个系统产生影响。

## 当前解决方案

我写这篇文章的原因是因为大部分的答案给人的感觉是错误的。像这样:

```
# replace shell with bash so we can source files
RUN rm /bin/sh && ln -s /bin/bash /bin/sh 
```

或者在 Dockerfile:
中硬编码节点版本

```
ENV NODE_VERSION 4.4.7 
```

如果您使用 nvm 而不是 Docker hub 映像，最好使用文件夹中的`.nvmrc`文件作为记录所用节点版本的单点。

## 更好的解决方案？

只是在构建了一个`rvm`容器之后，我才碰到了这个[栈溢出答案](https://stackoverflow.com/a/51803492)

> 不要用`RUN bash -l -c rvm install 2.3.3.`它很脏。您可以通过 shell [ "/bin/bash "、"-l "、"-c" ]设置 SHELL 命令，并简单地调用`RUN rvm install 2.3.3.`

在这之后，你的 docker 文件变得干净多了。但是请注意，你正在做一些不寻常的事情。用`-l`你是在要求一个“登录外壳”。这在 Docker 容器中是不常见的，它可能会产生您不想要的副作用。但这主要取决于你在做什么。在大多数情况下，你是安全的。就我而言，我真的不介意，因为我在 docker 中使用了多阶段构建，所以最终结果不会受此影响。如果你在开发容器中使用它，我想你也没问题。

这是我想出来的文档。注意，我没有清理我的 apt 文件，因为这是一个中间的构建步骤

```
FROM debian

SHELL [ "/bin/bash", "-l", "-c" ]

RUN apt-get update && apt-get install -y curl
RUN curl --silent -o- https://raw.githubusercontent.com/creationix/nvm/master/install.sh | bash
# this now works
RUN nvm install && nvm use 
```

请注意，这不能直接在 Ubuntu 容器中工作，因为 Ubuntu 中的`/root/.bashrc`文件包含这个:

```
# If not running interactively, don't do anything
[ -z "$PS1" ] && return 
```

然后它将跳过设置 nvm 所需的环境变量，因为在非登录 shell 中没有设置`$PS1`。

## 总结

我确实认为这个解决方案在多个方面都更清洁。但是我当然愿意接受建议；我希望这能节省其他人的时间，并帮助清理那些臃肿的文件。