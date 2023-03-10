# 让你的生活变得更轻松的 Postgres 和 Docker 技巧

> 原文：<https://dev.to/martinheinz/tricks-for-postgres-and-docker-that-will-make-your-life-easier-3e35>

*注:这最初发布在[martinheinz . dev](https://martinheinz.dev/blog/3)T3】*

如今，每个人都试图在容器中运行一切，我不怪他们，我也在做同样的事情，因为在 Docker 容器中运行应用程序、
数据库或其他工具非常好，我们都知道为什么(隔离、容易设置、安全...).然而，有时调试、访问或与容器的交互会很烦人。这包括访问、修改或查询数据库。因此，由于我广泛使用 PostgreSQL，并且已经在容器中运行了一段时间，随着时间的推移，我列出了一些命令，这些命令在对数据库服务器进行简单和不太简单的操作时会有很大的帮助。

### 登录 PSQL

如果你想与你的数据库服务器交互，你需要做的最基本的事情就是连接到数据库本身(使用 *PSQL* ):

```
docker exec -it <container_name> psql -U<user_name> -a <db_name> 
```

因此，对于名为`db`的 Docker 容器、默认用户`postgres`和数据库名称`blog`，它将是

```
docker exec -it db psql -Upostgres -a blog 
```

### 针对数据库运行命令

您可以登录然后执行您需要的任何命令，这很好，但是一次完成通常更方便，尤其是如果您只想运行单个命令或查询:

```
docker exec -it <container_name> psql -U<user_name> -a <db_name> -c '<command/query>' 
```

因此，如果我们想使用与上例相同的参数列出数据库中的所有表:

```
docker exec -it db psql -Upostgres -a blog -c '\l' 
```

这里，`\l`列出了当前数据库中的所有表格，如果你不熟悉 *psql* “反斜杠”命令，那么我强烈推荐这张[备忘单](https://gist.github.com/Kartones/dd3ff5ec5ea238d4c546)。

除了`psql`命令之外，您可以运行任何 SQL 查询，比如:

```
docker exec -it db psql -Upostgres -a blog -c 'SELECT * FROM posts;' 
```

### 备份您的数据

有时我需要备份数据或数据库的整个模式，有时只是作为一个*“保险单”，有时我可以不顾一切地进行更改，然后恢复一切，下面是如何做:* 

```
docker exec -it <container_name> pg_dump -U<user_name> --column-inserts --data-only <db_name> > backup.sql 
```

在这个例子中，我们使用了`pg_dump`实用程序，它允许我们提取 PostgreSQL 数据库。我使用`--column-inserts`和`--data-only`标志只获取表行，但通常只需要模式，为此你可以使用`-s`标志。

### 执行整个 SQL 文件

有时，您需要用足够的数据填充现有数据库进行测试(请不要对生产数据库这样做)，或者使用文件中的数据，然后将它们复制并粘贴到上面的命令中会更容易。

```
docker cp ./data.sql <container_name>:/data.sql
docker exec -it <container_name> psql -U<user_name> -a <db_name> -f /data.sql 
```

这里我们首先需要将文件本身复制到运行容器中，然后使用`-f`标志执行它。

### 开始时预填充数据库

如果您需要不时地执行它，前面的例子已经足够好了，但是如果您每次启动数据库时都必须这样做，那就变得很烦人了。因此，如果您决定最好从一开始就填充数据库，那么这里有一个解决方案。它只需要多做一点工作:

我们需要以下文件:

*   `Dockerfile` - *Dockerfile* 为你的 Postgres 镜像
*   `create_db.sh` -创建数据库、模式并填充它的脚本。
*   `schema.sql` -包含数据库模式的 SQL 文件
*   `data.sql` -包含用于填充数据库的数据的 SQL 文件
*   `.env` -环境变量文件，让你的生活更轻松

第一，`Dockerfile` :

```
FROM postgres:11

# Custom initialization scripts
COPY ./create_db.sh /docker-entrypoint-initdb.d/20-create_db.sh
COPY schema.sql /schema.sql
COPY data.sql /data.sql

RUN chmod +x /docker-entrypoint-initdb.d/20-create_db.sh 
```

这非常简单 *Dockerfile* ，我们在这里需要做的就是将我们的脚本和模式/数据复制到映像中，这样它们就可以在运行启动时使用。你可能会问，*没有`ENTRYPOINT`或`COMMAND`，我们如何在启动时运行它？* -答案是，基本的`postgres`映像在`docker-entrypoint-initdb.d`目录中的任何脚本上运行，所以我们需要做的就是将我们的脚本复制到这个目录，PostgreSQL 会处理它。

但是脚本(`create_db.sh`)里有什么呢？

```
#!/bin/bash
set -e

POSTGRES="psql --username ${POSTGRES_USER}"

echo "Creating database: ${DB_NAME}"

$POSTGRES <<EOSQL CREATE DATABASE ${DB_NAME} OWNER ${POSTGRES_USER}; EOSQL echo "Creating schema..."
psql -d ${DB_NAME} -a -U${POSTGRES_USER} -f /schema.sql

echo "Populating database..."
psql -d ${DB_NAME} -a  -U${POSTGRES_USER} -f /data.sql 
```

启动脚本首先使用指定的用户名(`POSTGRES_USER`)登录`psql`，然后创建您的数据库(`DB_NAME`)。接下来，它使用我们复制到映像中的文件创建数据库模式，最后用数据填充数据库。这里的所有变量都来自前面提到的`.env`文件，这使得随时更改数据库名称或用户名变得非常容易，而无需修改脚本本身。

更多完整的例子请看我的知识库[这里](https://github.com/MartinHeinz/blog-backend/tree/master/postgres)

### 那`docker-compose`呢？

根据我的经验，大部分时间我都是与使用数据库的应用程序一起运行数据库，最简单的方法是 *docker-compose* 。通常我更喜欢用服务名来指代 *docker-compose* 服务，而不是容器名，容器名可能是也可能不是同一个东西。如果
不一样，你只需执行以下命令:

```
docker exec -it $(docker-compose ps -q <db_service_name>) psql -U<user_name> -a <db_name> 
```

这里与前面的例子唯一不同的是`docker-compose`部分，它查找指定服务的信息。`-q`标志使得只显示容器 id，这正是我们所需要的。

### 结论

我希望这些小技巧中至少有一些能让您在处理 Docker 和 PostgreSQL 时更轻松，或者如果您只是因为在处理数据库时 Docker 可能有点烦人而避免使用它，那么我希望您在阅读本文后会尝试一下。🙂*