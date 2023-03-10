# 如何使用多阶段 Docker 构件

> 原文：<https://dev.to/tomtucka/how-to-use-multi-stage-docker-builds-1fde>

这篇文章于 2019 年 7 月 26 日首次发表在司法部数字与技术部[技术博客](https://medium.com/just-tech)上。

Docker 彻底改变了软件工程师构建应用程序的方式，这些应用程序是可维护的小软件，可以轻松扩展以满足需求。但是有了新技术，现在有更多的事情需要考虑。Docker 出现的一个问题是图像大小。传统上，为了获得高效的 docker 映像，您会在 docker 文件中使用一些复杂的 shell 命令，以使层尽可能小。

在`Docker 17.05`中，引入了多阶段构建，这意味着我们现在可以让 docker 图像变得更小，下面我将通过一个例子来说明如何使用 Symfony 应用程序和 composer 来实现这一点。

对于多阶段构建，您可以在单个`Dockerfile`中使用多个`FROM`命令。每个`FROM`命令可以使用不同的基础，并象征着一个新的建设阶段。最有用的是，您可以有选择地将工件从一个阶段复制到另一个阶段，留下所有您不需要的东西。

所以让我们创建一个新的`Dockerfile`并添加我们的第一个`FROM`命令。这将使用 composer 作为基础映像，因此我们可以获得所有 composer 依赖项。我们还可以通过在`FROM`命令中添加`AS <NAME>`来命名构建中的不同阶段。

```
FROM composer AS composer 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们抄一下我们的`composer.json`、`composer.lock`。我们还将添加一个`RUN`命令来运行 composer 安装。

```
FROM composer AS composer

COPY composer.json /app
COPY composer.lock /app

RUN composer install 
```

Enter fullscreen mode Exit fullscreen mode

我们的多阶段构建的第一部分已经完成，这将允许我们将 composer 依赖项安装到第一个映像中，然后将它们复制到最终映像中。

接下来，我们需要建立我们的最后阶段:

```
FROM php:7-fpm-alpine

WORKDIR /var/www

COPY ./ /app
COPY --from=composer /app/vendor /app/vendor 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`FROM php:7-fpm-alpine`，我们使用`php:7-fpm-alpine`作为基础映像开始一个新的构建，然后我们使用`COPY --from=composer`将`vendor`文件夹复制到我们的新映像中。我们把所有的作曲家 gubbins 都留下了，它没有保存到我们的最终图像中。

满`Dockerfile` :

```
FROM composer AS composer

COPY composer.json /app
COPY composer.lock /app

RUN composer install

FROM php:7-fpm-alpine

WORKDIR /var/www

COPY ./ /app
COPY --from=composer /app/vendor /app/vendor 
```

Enter fullscreen mode Exit fullscreen mode

您只需要一个 Dockerfile 文件。只需运行`docker build`和 docker，构建过程就会开始。

使用多阶段构建的最终结果是一个没有任何复杂性的精简的生产就绪映像。我希望你能看到使用多阶段构建的好处，我会鼓励你去尝试。