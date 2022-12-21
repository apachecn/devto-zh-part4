# 用 Docker 一次性运行 React 和 Node.js！

> 原文：<https://dev.to/numtostr/running-react-and-node-js-in-one-shot-with-docker-3o09>

这是我上一篇文章的第二部分。如果你没有看过我的第一篇文章，请看看它，因为它是这一部分的基础。

*   [连接同时运行的&](https://dev.to/numtostr/running-react-and-node-js-in-one-shot-with-concurrently-2oac)(第一岗位)
*   使用 Docker(本帖)

> ## TL； If you don't know docker, you can go back. However, if you want to read on.

在这篇文章中，我们将看看 docker 运行 React 和 Node.js 的方式。这是一种高级开发设置，我希望你已经[安装了](https://docs.docker.com/install/)，并了解了 [docker](https://docs.docker.com/engine/docker-overview/) 和 [docker-compose](https://docs.docker.com/compose/) 的基础知识。如果你想了解更多关于 docker 的信息，请前往 docker.com

## #初始设置

```
$ mkdir awesome_project 
```

在这种方法中，我们不会污染根文件夹。客户端和服务器将保留在它们专用的文件夹中。这样，如果有必要，我们可以随时分离客户机和服务器，而不会破坏任何东西。为了让它正常工作，他们每个人都应该有一个 [Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) ，并且都将与 docker-compose 连接。

## #客户端设置(React)

#### ~创建 react app

```
$ cd awesome_project && npx create-react-app client 
```

这将创建一个名为 *client* 的文件夹来保存我们的 react 应用程序。

#### ~ Dockerfile for React

在 ***客户端*** 文件夹中创建一个文件名`Dockerfile`，并粘贴以下代码。

```
FROM node:lts-slim

RUN mkdir -p /usr/src/app

WORKDIR /usr/src/app

EXPOSE 3000

CMD [ "npm", "start" ] 
```

这将是我们 react 应用程序的 docker 图像说明，react 应用程序将在这里编译和运行。

## #服务器设置(Node.js)

我们的服务器代码将保存在根文件夹中名为`server`的文件夹中。在这里，您可以使用 express 或您选择的任何其他框架来组成服务器。或者你可以使用[这个例子](https://gist.github.com/vkasraj/d0de199f77834f86c5971c81716ac70b)来快速设置一个服务器。

#### ~节点服务器的 Dockerfile

在 ***服务器*** 文件夹中创建一个`Dockerfile`。确保你有一个`dev`剧本在你的`package.json`里。如果你有不同的脚本来运行你的服务器，你可以在下面的`Dockerfile`中修改`CMD`指令。

```
FROM node:lts-slim

RUN mkdir -p /usr/src/app

WORKDIR /usr/src/app

EXPOSE 5000

# You can change this
CMD [ "npm", "run", "dev" ] 
```

## #用 docker-compose 运行

Docker-compose 帮助我们将多个 Docker 文件合并并运行到一个网络容器中。在项目的 ***根*** 中做一个名为`docker-compose.yml`的文件，复制下面的代码。

```
version: "3"
services:
    frontend:
        container_name: awesome_web
        build:
            context: ./client
            dockerfile: Dockerfile
        image: vikasraj/awesome_web
        ports:
            - "3000:3000"
        volumes:
            - ./client:/usr/src/app
    backend:
        container_name: awesome_server
        build:
            context: ./server
            dockerfile: Dockerfile
        image: vikasraj/awesome_server
        ports:
            - "5000:5000"
        volumes:
            - ./server:/usr/src/app 
```

最后，我们将有一个类似这样的文件夹结构。

```
> awesome_project
    > client # This is our react front-end
        > node_modules
        > src
        - Dockerfile
        - package.json
    > server # This is our Node.js server
        > node_modules
        - index.js
        - Dockerfile
        - package.json
    - docker-compose.yml 
```

最后，你需要像
一样修改`client/package.json`中的`proxy`字段

```
{  "proxy"  :  "http://backend:5000"  } 
```

> `backend`是合成文件中后端服务的名称

现在，我们可以通过运行以下命令来运行我们的项目。这将创建 docker 映像和[卷](https://docs.docker.com/storage/volumes/)，它们将在容器中运行。

```
$ docker-compose up 
```

如果您想在启动容器之前构建映像。

```
$ docker-compose up --build 
```

这可能是一种乏味的工作方法，因为您必须具备 docker 和 docker-compose 的知识。但是它有一些优点:

*   所有开发工作流程的一个设置。
*   Docker 可以用于任何编程语言。
*   如果您在 DevOps 中使用 docker，生产延期将变得轻而易举。
*   不需要 npm 包(尽管被 docker 取代)。