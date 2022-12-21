# 构成容器

> 原文：<https://dev.to/davorg/composting-containers-26jd>

在[我的前一篇文章](https://dev.to/davorg/docker-and-dancer-take-2-30e)中，我创建了两个 Dockerfiles，它们创建了两个容器，共同创建了一个 web 应用程序。一个运行 Perl 应用程序本身，另一个包含数据库。我说他们“一起工作”，但实际上，这有点言过其实。它们只能一起工作，因为我已经写了几个粗略的现成的 shell 脚本来运行这两个容器并将它们连接在一起。

让容器协同工作的更好方法是使用一个叫做 [`docker-compose`](https://docs.docker.com/compose/) 的工具。这是我的下一步。事实证明这比预期的要容易得多，在这篇文章中，我将解释我是如何做到的。

`docker-compose`由名为`docker-compose.yml`的配置文件驱动。你可以在 Github 上看到[我的最终版本，但是让我们一行一行地过一遍。](https://github.com/davorg/succession/blob/master/docker-compose.yml)

```
version: '3' 
```

我们首先声明我们正在使用的合成文件语法的版本。当前版本是 3，我没有理由考虑使用其他版本。

```
services: 
```

文件的大部分用于定义系统中使用的服务。“服务”是`docker-compose`描述容器的方式。我的系统使用三个容器，所以我们定义了三个服务。

```
 database: 
```

我们将定义的第一个服务是构建数据库容器的服务。我决定称它为“数据库”

```
 build:
      context: .
      dockerfile: Dockerfile-db 
```

数据库服务定义中的第一部分告诉`docker-compose`如何构建容器。`context`和`dockerfile`参数与传递给`docker build`命令的参数相同。这里我们告诉命令在当前目录(这将是 Git checkout 的根目录)中查找名为`Dockerfile-db`的 Dockerfile。

```
 container_name: succession-db 
```

下一行为我们的容器命名。

```
 environment:
      - MARIADB_ROOT_PASSWORD=sekrit
      - MARIADB_DATABASE=$SUCC_DB_NAME
      - MARIADB_USER=$SUCC_DB_USER
      - MARIADB_PASSWORD=$SUCC_DB_PASS 
```

然后我们定义一些环境变量。这些和我们在我的[上一篇文章](https://dev.to/davorg/docker-and-dancer-take-2-30e)中被称为`docker run`时使用的论点是一样的。

```
 ports:
      - "13306:3306" 
```

最后，对于这个服务，我们定义了端口。同样，这与传递给`docker run`的参数(在本例中为`-p`参数)相同。我们告诉 Docker 将容器上的端口 3306(标准的 MariaDB 端口)公开为主机上的 13306。

这就是我们构建和运行数据库容器所需的全部内容。

`docker-compose.yml`文件的下一部分向系统添加了一些新的东西。为了加快应用程序的速度，我使用了一个`memcached`服务器。在我以前的文章中，我还没有创建缓存容器，但是没有理由再推迟了。

```
 cache:
    image: memcached:1.5 
```

没有比这更简单的了。我刚刚从码头中心拉了一个标准的预制 [`memcached`集装箱。](https://hub.docker.com/_/memcached)

最后，我们需要为实际的应用程序构建和运行容器。我们就叫它“app”。

```
 app:
    build: . 
```

`build`部分比数据库容器部分简单一点。这是因为我们使用 docker 文件的标准名称，所以我们只需要定义它的位置(在当前目录中)。

```
 container_name: succession 
```

我们给容器命名。

```
 links:
      - database
      - cache
    depends_on:
      - database
      - cache 
```

并从我们的系统中定义该容器需要与之通信的其他容器，以及它依赖于哪些容器。

```
 environment:
      - SUCC_CACHE_SERVER=cache
      - SUCC_DB_HOST=database
      - SUCC_DB_PORT
      - SUCC_DB_NAME
      - SUCC_DB_USER
      - SUCC_DB_PASS 
```

然后，我们定义应用程序需要的一些环境变量。与数据库容器一样，这些参数与我们之前手动传递给`docker run`命令的`-e`参数相同。还值得注意的是，我们的应用程序用来连接数据库和缓存服务器的“主机名”就是我们在这个文件的其他地方定义的服务名。

```
 ports:
      - "1701:1701" 
```

最后，我们定义暴露给主机系统的端口。在这里，我为我的服务选择了一个不起眼的端口号来运行，并在同一个端口号下公开它(您可能记得该服务是关于英国王位继承的历史——1701 年是“定居法案”通过的那一年)。

一旦我们在`docker-compose.yml`中获得了所有这些信息，我们就可以运行命令`docker-compose up`并观察我们的容器被构建和运行。完成后，我们可以在运行于主机系统上的浏览器中访问 [http://localhost:1701/](http://localhost:1701/) 来查看我们的应用程序。使用 Docker 的乐趣在于，任何人都可以克隆我们的 Git 库，他们将能够运行相同的命令并看到完全相同的行为。

写完上一篇文章后，我做的另一件事是在 Docker hub 上设置我的 Docker 图片。我还对它进行了配置，这样每当我提交对我的 [Github repo](https://github.com/davorg/succession) 的更改时，映像就会被重建。

因此，现在我有了一种容易重现的方法来构建和运行驱动我的应用程序所需的容器。我希望下一步是让它们在亚马逊的弹性容器服务中运行。所以我希望这就是我的下一篇文章的内容。