# 使用 docker 开发 Node.js & Express.js 应用程序

> 原文：<https://dev.to/azure/desenvolvendo-aplicacao-node-js-express-js-com-docker-2e7a>

[![docker.png](img/97c212d36a060bfdc98f55b9339e47bd.png)](https://postimg.cc/VJ2WzhgB)

*原由 Chris Noring - **[在此](https://dev.to/softchris/5-part-docker-series-beginner-to-master-3m1b)*** 撰写的文章

No **[【最后一篇】](https://dev.to/azure/introducao-a-docker-conceitos-basicos-4g19)** 我们看到并学习了码头、图像和集装箱的基本概念。现在是我们开发应用程序并学习如何在应用程序中使用坞站的时候了。

我们开始吧？！

## 让我们在群众中牵手吧！

现在我们已经了解坞站是什么以及为什么要使用它，现在正是我们需要在某些应用程序中练习使用坞站的时候！

好吧，我们需要一个在码头上很常用的文件:*docerfile*。因为我们将在“*”dock file“*”中详细说明操作系统、环境变量以及如何运行我们的应用程序所需的所有内容。

现在让我们深入了解一下这一部分。我们将开发一个应用程序&*对接*他。通过它，我们将在一个与外部世界隔离的容器中运行我们的应用程序，但它可用于其他端口。

我们将遵循以下步骤:

*   **在 Node.js** 上创建应用程序

我们将使用 Express 开发 Node.js 应用程序。它会像其他 API 一样工作。

*   **创建文件‘docketerfile’**

此文件将负责告知坞站如何构建我们的应用程序。

*   **创建映像**

为了使我们的应用程序正常运行，我们必须创建一个名为 Docker 的图像。

*   创建容器

最后，我们将看到我们的应用程序正在运行，并从 docker 图像创建一个容器。

我们现在要遵循这些步骤吗？！

## Criando a aplicao node . js&快递 com Docker

现在打开您的 Visual Studio 代码，创建一个名为:t0[示例-2]的文件夹，然后在该文件夹中键入以下命令:

```
> npm init -y 
```

Enter fullscreen mode Exit fullscreen mode

此命令为我们创建一个标准的文件“**”package . JSON“**”。

最后，文件 **package.json** 将如下所示:

```
{  "name":  "exemplo-2",  "version":  "1.0.0",  "description":  "Código do exemplo do artigo: Introdução a Docker - Parte I",  "main":  "app.js",  "scripts":  {  "dev":  "nodemon",  "lint":  "eslint --ext .js,.html -f ./node_modules/eslint-friendly-formatter . --fix",  "prepush":  "npm run lint",  "start":  "node app.js"  },  "keywords":  [  "nodejs",  "javascript",  "docker",  "azure",  "express"  ],  "author":  "Glaucia Lemos",  "license":  "MIT",  "bugs":  {  "url":  "https://github.com/glaucia86/docker-zero-to-hero-series/issues"  },  "homepage":  "https://github.com/glaucia86/docker-zero-to-hero-series/blob/master/README.md",  "devDependencies":  {  "eslint":  "^5.16.0",  "eslint-config-airbnb-base":  "^13.1.0",  "eslint-plugin-import":  "^2.17.3",  "husky":  "^2.3.0",  "nodemon":  "^1.19.1"  },  "dependencies":  {  "eslint-friendly-formatter":  "^4.0.1",  "eslint-plugin-html":  "^5.0.5",  "express":  "^4.17.1"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

如果你想开发源代码，我在 GitHub 上创建了一个存储库。在这种情况下，只需克隆 git 或下载项目

那么，通过继续操作，我们将在文件夹*【例-2】*中创建文件**【app . js】**，然后创建文件

 **最后，项目结构将如下所示:

[![Screen-Shot-07-05-19-at-03-40-PM.png](img/082d084869dca40ba2637bc1ee93c9c3.png)](https://postimg.cc/cgSz1ftD)

现在，在文件夹*示例-2* 中运行以下命令:

```
> npm install 
```

Enter fullscreen mode Exit fullscreen mode

此命令将安装项目所需的所有依赖项，并创建一个名为“*node _ modules*”的文件夹

太好了！现在让我们开始发展吧！

## 开发‘app . js’文件

创建项目结构并在我们的应用程序中安装必要的依赖关系后，打开文件“**app . js**”并添加以下代码:

```
/**
 * Arquivo: app.js
 * Descrição: arquivo principal e responsável pela execução da aplicação.
 * Data: 05/07/2019
 * Author: Glaucia Lemos
 */

const express = require('express');

const app = express();

const port = process.env.PORT || 3000;

app.get('/', (req, res) => {
  res.status(200).send({
    success: 'true',
    message: 'Seja Bem-Vindo(a) ao mundo Docker!',
    version: '1.0.0',
  });
});

app.listen(port);
console.log('Aplicação executando na porta ', port); 
```

Enter fullscreen mode Exit fullscreen mode

现在，转到命令提示符并运行命令: **nodemon** ，然后打开邮递员并输入 URL:*[【http://localhost:3000/](http://localhost:3000/)*，看看 Api 是否正常工作:

[![Screen-Shot-07-05-19-at-04-30-PM.png](img/9bc35a26f34c28123b89ff1783e81dc0.png)](https://postimg.cc/Z9c0YtwX)

如果出现上图所示的信息，则说明一切正常！让我们继续我们的教程！

## 开发‘dock erfile’文件

下一步现在是创建文件 *Dockerfile* 。如前所述，此文件就像清单，其中包含编译和运行我们的应用程序所需的所有说明。但是，通过*docerfile*运行应用程序需要什么？我们需要:

*   **通过 dock 容器复制**所有应用文件。

*   安装依赖项

*   **在将要打开的容器中打开**的门，也就是说，在外面。

*   **指示**容器如何启动我们的应用程序

在更复杂的应用程式中，我们需要输入更多资讯，例如:设定资料库的环境变数或设定证明资料，或者执行特定资料库等等。但是，由于这是一个简单的应用程序，所以让我们用一种非常简单的方式来创建‘t0’docerfile:T2]

```
//==> Dockerfile

FROM node:latest

WORKDIR /app

COPY . .

RUN npm install

EXPOSE 3000

ENTRYPOINT ["node", "app.js"] 
```

Enter fullscreen mode Exit fullscreen mode

我们走吧:

*   **从**这里我们正在选择坞站枢纽操作系统的映像。docker hub 是一个全局存储库，包含可以在本地提取的图像。在我们的案例中，我们选择了一个基于 Ubuntu 的映像，该映像安装了 Node.js，称为 Node。我们还具体规定，我们需要 Node.js 的更高版本，使用以下标记:`latest`

*   **WORKDIR** 这里只是指我们定义了一个工作目录。这是一种配置以后在下面的命令中会发生什么的方法。

*   **COPY** 在此，我们正在从我们所在的目录中复制文件，通过此命令指定一个目录。

*   **运行**在这里，我们将在结尾执行命令。就我们而言，我们将安装在 node . js with express 上构建应用程序所需的所有库。

*   **暴露**在这里意味着我们将打开一定的门。通过这个门我们可以和集装箱沟通。

*   **【entry point】**这是我们应该申报如何启动申请的地方。必须将命令指定为数组，例如:["node "，" app.js"]。就像在终端上运行命令:“node app.js”

## 创建图像

要创建映像，我们需要执行以下两个步骤:

*   **创建映像:**借助‘docketerfile’文件和`docker build`命令，我们将创建映像

*   **启动容器:**现在我们有图像了，我们需要创建一个容器。

让我们现在就执行这些步骤！打开项目文件夹`exemplo-2`并运行以下命令:(**别忘了运行桌面坞站！！！** )

```
> docker build -t exemplo-2-node-docker/node:latest . 
```

Enter fullscreen mode Exit fullscreen mode

请注意，它的末尾有一个句号`(.)`。他必须被包括在内才能创造我们的形象！因为它会指示 docker，并告诉 dockerfile 的位置，在我们的例子中，就是你们所在的目录。如果您没有操作系统映像，我们将订购“`FROM`”命令并引导您通过坞站中心下载，然后在坞站配置文件中创建指定的映像。您的命令提示符应如下所示(取决于 internet 连接，此命令可能需要几分钟才能完成):

[![gif-3d3a08bc2e12687ca.gif](img/8dadd81cd56793646c8fec0b9f82a393.png)](https://gifyu.com/image/EJJm)

我们在上面的 gif 中看到的是操作系统图片`node:latest`是如何通过坞站集线器传输和下载的。然后，我们的每个命令都以`WORKDIR`、`RUN`等形式执行。我们在这里看到的是 docker，它在每个命令后非常智能地运行和缓存所有不同的文件层，以便更快地运行。最后，我们看到消息:`successfully built`表示我们的形象已经成功构建！让我们来看看我们的形象是如何形成的。打开提示并键入以下命令:

```
> docker images 
```

Enter fullscreen mode Exit fullscreen mode

[![Screen-Shot-07-05-19-at-08-08-PM.png](img/fd740a52079def8ea4606b44955a3603.png)](https://postimg.cc/dLgwpzkR)

如果显示如上图所示，则说明已成功创建映像！

## 创建容器

下一步是把我们创造的图像做成一个容器。容器是在其中运行我们应用程序的独立部件。我们通过运行以下命令创建了一个容器:`docker run`。重新打开提示并运行以下命令:

```
> docker run -p 8000:3000 exemplo-2-node-docker/node 
```

Enter fullscreen mode Exit fullscreen mode

还记得在文件`Dockerfile`中我们定义了一个端口吗？我们在上一篇文章中已经解释过，我们需要一个内部端口映射到主机上的外部端口。请记住，这是我们要在浏览器中运行的应用程序。因此，我们应按以下方式进行测绘:

```
-p [porta externa]:[porta interna] 
```

Enter fullscreen mode Exit fullscreen mode

gif 中的观察程序已运行

[![gif-4.gif](img/f544c97f3e70293cd1d50fd387b1429d.png)](https://gifyu.com/image/EltG)

请注意，在浏览器中，我们必须放置外门，在本例中是门:`8000`。

但是，要停止运行此容器，我们需要执行一些命令:

1.  我们必须先列出他们正在运行的容器

```
> docker container ls 
```

Enter fullscreen mode Exit fullscreen mode

1.  然后从容器中复制 **Id** ，并运行以下命令:

```
> docker kill <Id do Container> 
```

Enter fullscreen mode Exit fullscreen mode

请看下面的 gif:

[![gif-5.gif](img/e0e80767e93eb3df82bd9820de62199e.png)](https://gifyu.com/image/El5b)

## 用环境变量改进设置

球秀！我们已经学习了如何使用 Node.js & Express 创建第一个 dock 映像，学习了如何运行容器，最后使用文件“`DockerFile`”运行应用程序。但是，我们可以在文件`DockerFile`中改进变量 **PORT** 。因为你现在的样子很容易出错。

要解决此问题，我们可以包括一个环境变量。因此，我们将做两个更改:

*   新增环境变数至 dock file
*   从文件 **app.js** 中的环境变量读取

### 添加了环境变量

为此，我们需要使用命令 **ENV** 。打开文件`DockerFile`并修改如下:

```
FROM node:latest

WORKDIR /app

COPY . .

ENV PORT=3000

RUN npm install EXPOSE 3000

ENTRYPOINT ["node", "app.js"] 
```

Enter fullscreen mode Exit fullscreen mode

我们再做一次改变吧！我们现在更新变量暴露，那么我们将摆脱静态值，依靠变量，如下所示:

```
FROM node:latest

WORKDIR /app

COPY . .

ENV PORT=3000

RUN npm install EXPOSE $PORT

ENTRYPOINT ["node", "app.js"] 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们已将命令**改为 **$PORT** 。这里要说的一个重要点:我们使用的任何变量都必须以字符“`$`t5”作为前缀**

```
> EXPOSE $PORT 
```

Enter fullscreen mode Exit fullscreen mode

## 读取 App.js 文件中环境变量的值

我们可以按如下方式读取 Node.js 中的环境变量值:

```
> process.env.PORT 
```

Enter fullscreen mode Exit fullscreen mode

因此，我们将更新文件`app.js`，如下所示:

```
/**
 * Arquivo: app.js
 * Descrição: arquivo principal e responsável pela execução da aplicação.
 * Data: 05/07/2019
 * Author: Glaucia Lemos
 */

const express = require('express');

const app = express();

const port = process.env.PORT;

app.get('/', (req, res) => {
  res.status(200).send({
    success: 'true',
    message: 'Seja Bem-Vindo(a) ao mundo Docker!',
    version: '1.0.0',
  });
});

app.listen(port);
console.log(`Aplicação executando na porta..: ${port}`); 
```

Enter fullscreen mode Exit fullscreen mode

请注意，当我们更改文件`app.js`或文件`Dockerfile`时，我们需要重建我们的映像。这意味着我们需要再次运行坞站编译命令，在此之前，我们需要使用命令:
来拆除容器

```
> docker stop 
```

Enter fullscreen mode Exit fullscreen mode

```
> docker rm 
```

Enter fullscreen mode Exit fullscreen mode

我们将在接下来的几节中进一步了解这两个命令！😉

## 管理容器

太好了！您刚刚启动了容器，并注意到它没有在终端上完成/关闭运行。那才是恐慌的根源！😳😳

在这种情况下，您可以执行以下操作，打开另一个终端窗口，然后执行以下命令:

```
> docker ps 
```

Enter fullscreen mode Exit fullscreen mode

此命令将列出计算机上运行的所有容器。您可以看到容器名称及其 Id，如下图所示:

[![Screen-Shot-08-03-19-at-04-41-PM.png](img/96ab3330bde83f85a022953f83f78669.png)](https://postimg.cc/Y4n59X0G)

如上所述，我们有**栏【container _ id】**和**栏**名称。这两个值都可以用来打断我们的容器，因为这是我们需要做的，所以:

```
> docker stop 3da 
```

Enter fullscreen mode Exit fullscreen mode

我们选择使用**【container _ id】**栏，只需放置前 3 位数字，无需包含全部 **ID** 。执行此命令后，请注意键入“`docker ps`”后，将不再显示任何容器，并停止执行我们的映像！

## Modo 守护进程

我们可以像上面那样做，开一个新的分开的终端。但是，在守护进程模式下运行，这是最好的选择。这意味着我们将在后台运行容器，并且它的所有输出都不可见。为此，只需添加-d 标志即可。我们要试试吗？！

```
> docker run -d -p 8000:3000 exemplo-2-node-docker/node 
```

Enter fullscreen mode Exit fullscreen mode

[![Screen-Shot-08-03-19-at-04-56-PM.png](img/229527aee5bc653709b554a7c0567dcb.png)](https://postimg.cc/bDKkzhj6)

请注意，我们现在有容器 Id。现在更容易停止运行！只需键入上述 ID 的前 3 位数。例:

```
> docker stop 014 
```

Enter fullscreen mode Exit fullscreen mode

## 码头工人杀 vs 码头工人停

到目前为止，我们一直以`docker stop`命令作为停止容器的方法。但是，还有另一种方法可以停止容器，即执行命令`docker kill`。但有什么区别呢？

*   **停靠站**:该命令在一段时间的停靠后发送信号**【sigterm】**，然后**【sigkill】**。简而言之，这是一种更优雅地缩小容器的方法，这意味着它释放资源并保存状态。

*   **码头杀手**:此命令立即发送 **SIGKILL** 。这意味着释放资源或维护状态可能无法按计划运行。正在开发中，无论使用哪一个命令，但是在生产场景中依靠命令`docker stop`更为谨慎

## 清洗容器

在某些应用程序开发过程中，您最终会创建许多容器。因此，为确保移除/清洁这些容器，只需键入以下命令:

```
> docker rm <id-do-contêiner> 
```

Enter fullscreen mode Exit fullscreen mode

例:

```
> docker rm -f ef8 
```

Enter fullscreen mode Exit fullscreen mode

```
> docker ps 
```

Enter fullscreen mode Exit fullscreen mode

## 结语

现在是了-我...。在本文中，我们学习了如何使用 docker 实用的分步方式创建 Node.js & Express.js 应用程序。我们已经学习了一些重要的码头指令，这不会停留在这里。因为我们还有很多重要的事情要学习，例如如何使用数据库、卷、链接容器以及管理多个容器(也称为编排)的原因和方法。

但这是一系列文章！我们得停在某个地方否则这篇文章会很长。保持联系，注意接下来的部分，我们将讨论卷和**数据库**。

## 结语

今天，我们实际上了解了如何使用 docker 来轻松创建 Node.js & Express.js 应用程序。

如果您想学习和进一步学习坞站，我推荐以下坞站课程:

**[免费课程-使用坞站](https://docs.microsoft.com/learn/modules/intro-to-containers/?WT.mc_id=devto-blog-gllemos)** 创建集装箱式 Web 应用程序

**[váRios Cursos grátis de Docker](https://docs.microsoft.com/learn/browse/?term=docker&WT.mc_id=devto-blog-gllemos)T3】**

而且为了跟上最新的更新，不要忘了在 Twitter 上跟踪我！

[![Twitter](img/9a13c0534f6448ca23e17b3afbfb6a4c.png)](https://twitter.com/glaucia_lemos86)

现在，我再次感谢克里斯·诺林提供了这一系列关于坞站的优秀文章。跟着 Chris Noring 在 twitter 上说:

[![Screen-Shot-08-03-19-at-05-14-PM.png](img/990febbcaf4ca253457df0dfa0487574.png)](https://postimg.cc/4HkX5JtR)

下次见！**