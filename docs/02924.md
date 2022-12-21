# 使用 docker 和 NodeJS 进行本地开发

> 原文：<https://dev.to/binarysort/using-docker-with-nodejs-for-local-development-43k5>

在不同的机器上设置项目可能是一项单调乏味的任务，因为这需要花费大量的时间，有时还会出现版本问题。新成员在他/她能够开始做出贡献之前，必须浪费大量时间来调整环境。如果您和您的团队成员可以在同一个代码基础上工作，而不用担心每个人的系统配置，这不是很酷吗？？

这就是 Docker 可以帮助我们的地方。Docker 允许您在所有机器上本地运行相同的环境，稍加调整，它也可以用于生产。在这篇博客中，我们将使用 Docker 用 PostgreSQL 创建一个 NodeJS web 应用程序。

## Docker 是什么？

Docker 是一个使用容器创建、部署和运行应用程序的工具。容器允许我们为我们的应用程序创建一个包含所有必需的库和环境依赖的包。作为容器提供的应用程序可以在任何其他 Linux 机器上运行，不管定制设置是否与用于编写和测试代码的机器不同。

因此，你可以与任何人共享你的应用程序，他们将能够毫不费力地运行和使用你的应用程序。

## DockerFile，Docker Image &容器

Docker 文件、Docker 图像和 Docker 容器是使用 Docker 时需要理解的三个重要术语。

**DockerFile:** DockerFile 是一个包含一组用于创建图像的命令的文件。您可以在命令行上运行这些相同的命令来创建映像。

**Docker Image:**Docker Image 可以看作是用来创建容器的模板。Docker 图片可以通过 docker 注册表共享。Docker Hub 是一个公共注册中心，允许多个用户使用这些图像并进行协作。

**Docker 容器:**简单来说，Docker 容器就是一个图像的运行实例。

## 复合坞站

Docker Compose 是一个让我们轻松定义和运行多个容器的工具。您编写一个 YAML 文件，称为 compose 文件(`docker-compose.yml`)，其中包含有关服务、网络和卷的详细信息，以设置 docker 应用程序。你可以创建单独的容器，托管它们并让它们互相通信。

让我们快速了解一下服务、网络和卷:

**服务** : Docker 服务将是某个大型应用程序环境中的微服务的映像。当您创建服务时，您可以指定要使用的容器映像以及要在运行的容器中执行的命令。服务的例子可能包括 HTTP 服务器、数据库或您希望在分布式环境中运行的任何其他类型的可执行程序。

**网络**:Docker 网络允许服务相互通信或者与其他非 Docker 工作负载通信。我们使用网络连接服务器容器和数据库容器。

**卷**:卷是 docker 文件系统之外的目录(或文件)，作为普通目录(或文件)存在于主机文件系统(您的机器)上。这允许我们将工作目录与服务器容器绑定在一起。

## 让我们使用 docker 创建一个 NodeJS 应用程序

我们将以通常的方式创建我们的节点应用程序，使用 npm:

`npm init`

这将提示您输入几个信息，比如应用程序的名称和版本。除了入口点之外，大多数都可以接受默认值。
输入`app.js`作为入口点。

`entry point: (index.js) app.js`

这将创建一个类似于
的`package.json`文件

```
{  "name":  "app-name",  "version":  "1.0.0",  "description":  "",  "main":  "app.js",  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1"  },  "author":  "",  "license":  "ISC"  } 
```

Enter fullscreen mode Exit fullscreen mode

现在安装 express:

`npm install express --save`

将以下代码添加到`app.js`文件:

```
const express = require('express');

const app = express();

app.get('/', (req, res) => {
  res.send(
    `<h1>Docker is fun!</h1>`
  )
})

app.use((err, req, res, next) => {
  if (err) {
    console.error(err);
    res
      .status(err.statusCode || err.status || 500)
      .send(err || {});
  } else {
    next();
  }
});

const server = app.listen(3000, () => {
  console.log('App listening on port', server.address().port);
}); 
```

Enter fullscreen mode Exit fullscreen mode

在`package.json` :
中添加启动脚本

```
...  "scripts":  {  "test":  "echo \"Error: no test specified\" && exit 1",  "start":  "node app.js"  },  ... 
```

Enter fullscreen mode Exit fullscreen mode

要在您的本地机器上运行该程序，请运行以下命令:

`npm start`

应用程序将在`http://localhost:3000`开始。

既然我们的节点应用程序已经启动并运行，我们就可以使用 docker 了。我们将为节点和数据库创建服务。

创建`Dockerfile`并将下面的代码放入其中:

```
# Official docker image for Node.js
FROM node:10

# Create app directory
WORKDIR /app

# Install app dependencies
# A wildcard is used to ensure both
# package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./

RUN npm install
RUN npm install -g nodemon

# Bundle app source
COPY . .

EXPOSE 3000
CMD [ "nodemon" ] 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将创建一个包含服务细节的组合文件，在我们的例子中是 web 和数据库。

```
version: '3'

services:
  web:
    build:
      context: .
      dockerfile: ./Dockerfile
    ports:
      - 3000:3000
    volumes:
      - .:/app
    networks:
      - app-network
    depends_on:
      - db

  db:
    image: library/postgres:11.3-alpine
    restart: unless-stopped
    ports:
      - 10000:5432
    networks:
      - app-network
    environment:
      - POSTGRES_USER=db_user
      - POSTGRES_PASSWORD=db_password

networks:
  app-network:
    driver: bridge 
```

Enter fullscreen mode Exit fullscreen mode

让我们了解一下这个文件中发生了什么:

*   我们定义了两个服务`web`和`db`。web 服务使用 Dockerfile 文件来构建映像，而 db 服务直接从提供的配置中构建映像。
*   这些容器使用名为`app-network`的网络相互通信。您可以向 web 应用程序提供数据库连接信息。
*   因为我们将使用这些容器进行本地开发，所以本地目录中的任何更新都应该反映到容器中。为此，我们将装入一个卷。该卷将本地目录映射到容器中的工作目录。

现在我们有了一个合成文件，运行这个命令来启动我们的应用程序:

`docker-compose -f local.yml up`

该命令将构建映像，然后运行它们。

就是这样！现在你可以和任何人分享你的项目，他们所要做的就是运行上面的命令并开始编码。