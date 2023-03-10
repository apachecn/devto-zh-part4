# 角度+ Docker...实际上

> 原文：<https://dev.to/gelopfalcon/angular-docker-really-56in>

> 这是 front end developers dock 的基础教程，类似于此处介绍的概念，可用作对接使用其他语言或框架创建的任何其他应用程序的基础。

当微服务大爆炸发生时，据说一个在当时具有创新性和巨大价值的概念从深处浮现出来，众神称之为“虚拟机(VM)”。虚拟机安装了一个完整的操作系统，并安装了自己的内存管理以及相关的虚拟设备驱动程序开销。在虚拟机中，模拟宝贵的来宾操作系统和虚拟机管理程序资源，使一个或多个操作系统的多个实例能够在单台计算机(或主机)上并行运行。每个来宾操作系统作为主机系统中的单个实体运行。

一切都很顺利但一如既往没有什么是完美的。硬件维护成本，金钱，这些围绕着我们发展的恶魔，开始走出黑暗，压倒虚拟机的天堂。虚拟机是依赖硬件的，这使得虚拟机的使用和部署成本比预期的要高$$，此外，由于使用了来宾操作系统，它们消耗的内存也比预期的要多。当每个人都开始绝望时，从同一个海洋里产生了希望，一只小鲸鱼带来了答案:集装箱。

[![Alt Text](img/09a8ceacb032f839ee159af9294664e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iisGCkMU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i0.wp.com/www.vinsguru.com/wp-content/uploads/2018/04/docker-volume-mapping-header.gif)

###### 托马多·德·https://www.testautomationguru.com

## 码头工人。

容器比虚拟机小，由于主机内核共享的缘故，可提供更快的启动速度、更好的性能、更少的隔离和更好的兼容性。运行微服务应用架构的最佳选择是应用程序容器。简而言之，docker 是一个软件平台，它简化了应用程序的创建、运行、管理和分发过程。这是通过虚拟化安装和运行它的计算机的操作系统来实现的。

### 基本概念

*   **docerfile**:docerfile 是一个文本文档，其中包含用户可以在命令行中调用的所有命令来组合图像。

*   **dock image**:是包含运行应用程序所需的所有库和配置的可执行文件。它有多个堆叠在彼此之上的图层，并表示为单一物件。使用 dockerfile 建立影像。

*   **坞站容器**:是坞站图像的运行实例。可能会有许多容器基于同一图像运行。

### “遏制”一个角度应用程序

出于此发布的原因，我假定您已经安装 NPM 和 Angular CLI，因此，我们将创建 Angular app。

`ng new angular-docker`

好的，我们将创建一个坞站，它必须保存在我们的文件夹*角坞站*中。此文件没有扩展名，名为 dockerfile。然后将以下代码添加到 dockerfile:

```
FROM node:latest as node

ARG ENV=prod
ARG APP=angular-docker

ENV ENV ${ENV}
ENV APP ${APP}

WORKDIR /app
COPY ./ /app/

# Instala y construye el Angular App
RUN npm ci
RUN npm run build --prod
RUN mv /app/dist/${APP}/* /app/dist/

# Angular app construida, la vamos a hostear un server production, este es Nginx

FROM nginx:1.13.8-alpine

COPY --from=node /app/dist/ /usr/share/nginx/html
COPY ./nginx.conf /etc/nginx/conf.d/default.conf 
```

好吧，到目前为止非常漂亮和所有的东西，但这到底是什么意思在码头？

`FROM node:latest as node`:角化应用程序需要`node`和`npm`来安装其依赖项，同样可以使用`yarm`，但这将是另一回事。所以，这里我们所做的是使用最新版本的 node，我们将它保存在一个叫做 node 的‘T4’中(您可以选择任何名称)。

`WORKDIR /app`:这为我们在图中的代码设置了工作目录，被以后的所有命令使用，如`COPY`、`RUN`和`CMD`。

`/app`:此命令会将项目中的所有现有文件复制到我们的坞站映像的“`/app`”文件夹中。

`RUN npm ci`:命令`npm ci`具有与`npm install`相同的安装依赖项的功能，但不同之处在于`npm ci`首先删除文件夹`node_modules`，然后使用`packaje.json.lock`安装项目依赖项的准确版本。使用 CI/CD 工具(如 Travis、Jenkins)时，建议使用此命令。

`RUN npm run build --prod`:命令`build`创建一个名为`dist`的新文件夹进行分发。这些是我们可以存放在服务器上的文件，我们的角应用程序将会加载这些文件。`--prod`标志创建更优化、更小版本的我们的应用程序。

`RUN mv /app/dist/${APP}/* /app/dist/`:我们移动了上述命令在`app/dist`文件夹中创建的所有内容。

到目前为止，我们的图像已经包含了项目的最优化版本，但这还不够，请记住，Angular 仅是 HTML、CSS 和 JS，需要服务器/代理才能托管和供用户访问。我最喜欢的 web 应用服务器是 nginx。

`1.13.8-alpine`:在此命令中，我们将创建第二层，其中包含版本`nginx`的`1.13.8-alpine`。

`COPY --from=node /app/dist/ /usr/share/nginx/html`:在这个命令中发生了很多魔法，就像我们哥斯达黎加所说的那样，`pele el ojo`。我们将从名为“`node`”的第一层生成的文件夹“`app/dist/`”的内容移动到我们的 nginx 服务器上的文件夹“`/usr/share/nginx/html`”。这里我们看到的是坞站的‘t5’的魔力。

[![Alt Text](img/ef2a522b9a6fae45753d6b84be08a00b.png)](https://i.giphy.com/media/Fkmgse8OMKn9C/giphy.gif)

`COPY ./nginx.conf /etc/nginx/conf.d/default.conf`:最后，我们只复制容器文件夹中的 nginx 配置文件。我将设置 https://github . com/gel head alcon/kubrines-fundamentals/blob/master/contact-list-master/engine x . conf 的链接留给您

### 建立影像

`docker build -t my-angular-docker .`

上述命令允许我们在本地环境中创建映像。我们可以使用“`docker images`”确保它存在，它部署我们创建或从任何注册表下载的映像。

### 构建容器

`docker container run -p 8080:80 --name myapp my-angular-docker`。

我们正在根据图片`my-angular-docker`创建一个名为`myapp`的`container`。

`-p 8080:80`:我们的`container`需要在坞站外无障碍。然后，我们主机服务器端口 8080 发出的请求将通过我们容器端口 80 进行监听。请记住，我们的容器`myapp`有一个带有托管角度应用程序的 nginx 服务器，可通过 80 访问。

确保容器工作正常，`docker ps`将显示正在运行的容器及其状态。嗯，如果没问题的话，打开你最喜欢的浏览器，进入你的集装箱`http://localhost:8080`。

如果你想停止容器，你只需要【`docker stop myapp`】对于我们来说，一旦停止容器，你就应该无法从浏览器存取你的应用程式，直到你重新启动它。为此，只需`docker start myapp`即可。

[![Alt Text](img/f46ea7ae46f1b02584589e92968b7d53.png)](https://i.giphy.com/media/9Jcw5pUQlgQLe5NonJ/giphy.gif)

感谢您的关注，我们正在作出巨大努力，使讲西班牙语的社区也能与内容合作。我邀请你们在 twitter 上以及在像[【gdg pure life】](https://www.meetup.com/gdg-costarica)这样的社区里跟随我，在那里经常为社区举办免费活动。