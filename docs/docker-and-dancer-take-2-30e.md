# 码头工人和舞蹈演员(第二场)

> 原文：<https://dev.to/davorg/docker-and-dancer-take-2-30e>

几个月前，我发表了我第一次为 Dancer 应用程序构建 Docker 容器的尝试，这个应用程序为我的一个网站提供了动力。很明显，我边走边学，而且我使用的 docker 文件已经过时了。我从各种各样的人那里得到了大量的好建议，这些人在这方面比我懂得多。

看起来好像我已经放弃了这个项目，但事实并非如此。最近一些其他的事情花了我很多时间，但是现在我又回来看这个了。

下面是我的 docker 文件中的两个:

```
FROM perl:5.30.0
LABEL maintainer="dave@perlhacks.org"

EXPOSE 1701
CMD carton exec starman --port 1701 Succession/bin/app.psgi

RUN cpanm Carton Starman

COPY . /succession
RUN cd /succession && carton install --deployment
WORKDIR /succession 
```

你会(希望)注意到的第一件事是，它比之前的版本更简单。我已经删除了一些指令——有些东西已经安装在我的基本映像上了(`cpanm`和我正在使用的一些 C 库)。

哦，我已经把 docker 文件移到了主代码 repo 中，而不是有一个单独的 docker 文件，并且必须将真正的 repo 克隆到其中。我真的不明白为什么我认为那是个好主意！

我不打算像上次那样一行一行地解释——那样会有点重复。

我使用与之前相同的`docker`命令(`docker build -t succession .`)从这个文件构建一个映像，然后使用这个简单的 shell 脚本从这个映像运行容器:

```
docker run --name succession \
  --network="host"
  -e SUCC_DB_HOST \
  -e SUCC_DB_NAME \
  -e SUCC_DB_USER \
  -e SUCC_DB_PASS \
  -p 1701:1701 succession 
```

脚本中使用的各种`SUCC_DB_*`环境变量包含应用程序使用的数据库的连接细节。如果我有一个正在运行的数据库，并且这些变量设置正确，那么我可以让应用程序在浏览器中查看`localhost`上的端口 1701。

当然，我需要的下一件事是让数据库在另一个容器中运行。现在我想我有解决办法了。我创建了另一个名为`Dockerfile-db`的 docker 文件，它包含以下内容:

```
FROM mariadb/server:10.3
LABEL maintainer="dave@perlhacks.org"

COPY data/succession.dump /docker-entrypoint-initdb.d/succession.dump.sql 
```

正如您将看到的，这非常简单。我基于一个官方的 MariaDB 映像，我添加的唯一内容是将一个文件从 Git checkout 复制到一个名为`/docker-entrypoint-initdb.d`的目录中。如果 MariaDB 映像在运行时在该目录中看到一个扩展名为`.sql`的文件，那么该数据将被加载到数据库中。碰巧的是，我的数据是相当静态的，并且我已经在 Git repo 中有了它的转储。

我用命令`docker build -t succession-db -f Dockerfile-db .`构建我的映像，然后用这个脚本运行它:

```
docker run --name succession-db \
  -e MARIADB_ROOT_PASSWORD=sekrit \
  -e MARIADB_DATABASE=$SUCC_DB_NAME \
  -e MARIADB_USER=$SUCC_DB_USER \
  -e MARIADB_PASSWORD=$SUCC_DB_PASS \
  -p 13306:3306 -d succession-db 
```

图像的 [Docker Hub 页面上记录了所有的`MARIADB_*`环境变量。我已经将端口重新映射到一个更大的数字，这样它就不会与主机上可能已经运行的任何数据库服务器发生冲突。](https://hub.docker.com/r/mariadb/server)

运行之后，我可以通过运行以下命令连接到服务器:

```
mysql -h127.0.0.1 -u$SUCC_DB_USER -P 13306 -p$SUCC_DB_PASS -D$SUCC_DB_NAME 
```

我可以在数据库中看到我所有的数据。

所以我有一个容器运行我的应用程序，另一个运行我的数据库。下一步是让他们一起开始并互相交流。这听起来像是`docker-compose`的工作。也是本系列下一篇文章的好主题。