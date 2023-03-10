# 用 Next.js、Prisma 2 和 Docker 创建一个 Fullstack 博客应用程序——第一部分设置 Repo 并配置后端

> 原文：<https://dev.to/codemochi/create-a-fullstack-blog-app-with-next-js-prisma-2-and-docker-part-i-setup-the-repo-and-configure-the-backend-39eb>

这篇文章将介绍如何使用 Docker 创建一个完整的 javascript 应用程序，其中 Next.js 用于服务器端渲染和 Prisma 2 支持的 GraphQL 服务器，所有这些都由 Docker-Compose 编排。

**为什么是 Docker？**

Docker 通过将技术堆栈的不同部分分离到不同的 Docker 容器中，革新了 web 开发。通过在 Docker 中开发代码，您可以确保它在您的开发环境中与在生产环境中完全一样。

[![Architectural drawing with Docker, Prisma 2, and Next.js.](img/c2c5784376f16b70eaf020e803b9ba1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--14JrVuBi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/830e0429aee7223aae900ed1157ddd57/65f42/architecture.png)

它是如何组织的？

我们在前端容器中运行 Next.js，在后端容器中运行连接到 Prisma 的 GraphQL Yoga，在第三个容器中运行 Prisma Studio UI。我们从第四个容器中的 MySQL 数据库中读取数据，这个容器可供 Prisma UI 和后端服务器使用。

[![Our finished blog application](img/aa7bc18aeeec8935c6681d8fa9720415.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F7YpLSFz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/38f05743b628162730a007394db815e8/9424f/finished-app.png)

我们在建造什么？

我们正在基于 Prisma 2 CLI 附带的示例博客项目构建一个博客 web 应用程序。以下是我们可以从后端执行的操作:

*查询:*

*   阅读所有发布的博客帖子
*   阅读所有博客文章草稿
*   读取所有用户

*突变:*

*   创建用户
*   创建博客草稿
*   发布博客
*   删除博客

显然，在真实的应用程序中，您永远不会允许任何人看到所有用户或未发布的博客帖子——但在这里这样做是为了让我们可以看到所有帖子和用户，因为它们是从我们的 Next.js 网站上创建和修改的。

代码在哪里？

我们在 Github 上发布了最终代码。

# 目录:

**第一部分-设置回购和配置后端(本文)**

1.  创建基础回购
2.  创建 Next.js 前端
3.  创建样板 Prisma 2 后端
4.  将我们的网络应用归档

a.创建一个 docker 合成文件

b.为每个容器添加 docker 文件

1.  配置后端

a.将数据库从 SQLite 切换到 MySQL

b.移除未使用的后端样板代码

c.更新 Prisma Nexus 中的后端查询

1.  验证我们的 Docker-Compose 集群工作正常

**第二部分-配置前端**

1.  添加 GraphQL 片段、查询和解析器
2.  添加 GraphQL-Codegen 以生成 React 组件
3.  添加 Apollo 并为 Next.js 创建 HOC
4.  将 React-Apollo 添加到项目根
5.  安装 Antd 为一个美丽的网站布局

**第三部分-构建用户界面**

1.  创建主布局
2.  创建用户布局组件
3.  创建注册用户组件
4.  创建提要布局组件
5.  创建新的拔模组件
6.  创建发布草稿按钮组件
7.  创建删除帖子按钮组件

下面我们将浏览这个大纲的第一部分——一定要跳到结尾，观看整个过程的视频。

# 第一部分-设置回购和配置后端

**1。创建基础回购**

首先，让我们创建一个项目，并使用 nvm 设置节点的正确版本。如果您还没有安装 nvm，它是一个允许您在不同版本的 node 和 npm 之间切换的工具。你可以在这里查看。

```
mkdir blog-prisma2
cd blog-prisma2/
nvm use 10.15.3 
```

现在我们可以用`npm`和`git`初始化项目。正如你可能从[以前的博客文章](https://www.codemochi.com/blog/git-branches-for-organized-code/)中知道的，我是一个非常坚持良好分支实践的人，所以现在让我们创建一个阶段和特征分支。

```
npm init -y
git init
git checkout -b staging
git checkout -b base 
```

现在让我们在项目的根目录下创建一个`.gitignore`,并添加我们不想在回购中提交的所有文件名和文件夹。我们在运行任何`npm install`命令之前这样做很重要，因为`node_modules`中的文件数量非常大，我们不想提交这些文件。

```
logs
*.log
npm-debug.log*

pids
*.pid
*.seed

build/Release
**/node_modules

.DS_Store
.next/ 
```

现在运行这些命令进行第一次提交。随意使用 git 北海巨妖或者你最喜欢的 Git 工具:

```
git add .gitignore
git commit -am 'added gitignore' 
```

**2。创建 Next.js 前端**

现在，让我们创建一个文件夹，并在该文件夹中创建一个新的 npm 项目。然后我们可以添加 react、next.js、next.js 的 css 加载器以及所有的 typescript 依赖项。

```
mkdir frontend
cd frontend
npm init -y
npm install --save next react react-dom @zeit/next-css
npm install --save-dev @types/node @types/react @types/react-dom typescript 
```

现在我们可以使用一个`tsconfig.json`文件告诉 typescript 实际运行。制造`frontend/tsconfig.json` :

```
{  "compilerOptions":  {  "allowJs":  true,  "alwaysStrict":  true,  "esModuleInterop":  true,  "forceConsistentCasingInFileNames":  true,  "isolatedModules":  true,  "jsx":  "preserve",  "lib":  ["dom",  "es2017"],  "module":  "esnext",  "moduleResolution":  "node",  "noEmit":  true,  "noFallthroughCasesInSwitch":  true,  "noUnusedLocals":  true,  "noUnusedParameters":  true,  "resolveJsonModule":  true,  "skipLibCheck":  true,  "strict":  true,  "target":  "esnext"  },  "exclude":  ["node_modules"],  "include":  ["**/*.ts",  "**/*.tsx"]  } 
```

在本教程的最后，我们将使用`antd`进行所有的样式设计，所以让我们继续添加 css 支持，这样我们就可以使用样式表了。创建下一个配置文件，并将 css 插件添加到其中:

制作`frontend/next.config.js` :

```
const withCSS = require('@zeit/next-css')
module.exports = withCSS({}) 
```

现在我们可以创建一个`frontend/next-env.d.ts`文件，它包含我们项目中使用的 next.js 类型的引用:

```
/// <reference types="next" />
/// <reference types="next/types/global" /> 
```

现在我们有了这些基础文件，我们可以开始创建我们的 react 组件了。首先要有条理，制作`frontend/components`和`frontend/pages`文件夹。然后创建`frontend/pages/index.tsx` :

```
import * as React from 'react'
import { NextPage } from 'next'

const IndexPage: NextPage = () => {
  return <h1>Index Page</h1>
}

export default IndexPage 
```

Next.js 使用的约定是，页面目录中的 React 组件是网站的路由。索引页面表示`/`路线，在上面的 React 组件中，我们只是创建了一个简单显示 h1 标题的页面。

现在我们需要将 next.js 脚本添加到`frontend/package.json`文件中，这样我们就可以轻松地启动和停止我们的前端服务器:

```
 "scripts":  {  "dev":  "next",  "build":  "next build",  "start":  "next start",  "type-check":  "tsc"  }, 
```

Next.js 8.1.1 本身支持我们的项目需要的 typescript。快速检查一下你的`frontend/package.json`文件中的版本是否是最新的。截至 2019 年 7 月初，执行 Next.js 的`npm install`将产生 8.1.0，因此我需要手动修改`package.json`中的版本为:

```
"next":  "^8.1.1-canary.61", 
```

如果您需要更新它，请确保在编辑完`package.json`文件后运行一个`npm install`，以便获取最新版本。

接下来，通过运行`npm run dev`启动服务器。转到`http://localhost:3000`并确认索引页面出现:

[![Base Next.js website running on our computer.](img/2ef06e09c7ccd978edab8a4db963d525.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--INQ5_a20--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/98df867411fedcd6234f2b88ebc69bd6/feedf/starter-frontend.png)

一旦你对它的运行感到满意，按 ctrl+c 停止前端服务器。

**3。创建样板 Prisma 2 后端**

现在我们必须做我们的后端。导航回`blog-prisma2`目录并初始化 Prisma 2。

```
npm install -g prisma2
prisma2 init backend
  // Select SQLite
  // Select Photon and Lift
  // Select Typescript
  // Select GraphQL Boilerplate 
```

这个过程将创建一个名为 backend 的文件夹，它将使用 Prisma 2 克隆一个博客后端。我们可以启动它，然后从那里开始，但我们会立即开始将它集成到 docker 中，这样我们就不必与 SQLite 打交道，而是从一开始就将我们的后端连接到 MySQL 数据库。

**4。将我们的网络应用**归档

a .创建一个 docker-compose 文件。

现在我们想对我们的应用程序进行归档。在项目的根目录下创建`docker-compose.yml`。

```
version: '3.7'
services:
  mysql:
    container_name: mysql
    ports:
      - '3306:3306'
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: prisma
      MYSQL_ROOT_PASSWORD: prisma
    volumes:
      - mysql:/var/lib/mysql
  prisma:
    links:
      - mysql
    depends_on:
      - mysql
    container_name: prisma
    ports:
      - '5555:5555'
    build:
      context: backend/prisma
      dockerfile: Dockerfile
    volumes:
      - /app/prisma
  backend:
    links:
      - mysql
    depends_on:
      - mysql
    container_name: backend
    ports:
      - '4000:4000'
    build:
      context: backend
      dockerfile: Dockerfile
    volumes:
      - ./backend:/app
      - /app/node_modules
      - /app/prisma
  frontend:
    container_name: frontend
    ports:
      - '3000:3000'
    build:
      context: frontend
      dockerfile: Dockerfile
    volumes:
      - ./frontend:/app
      - /app/node_modules
      - /app/.next

volumes: #define our mysql volume used above
  mysql: 
```

让我们看一下这份文件。它分为 4 个服务:mysql、prisma、后端和前端。我们已经创建了前端服务器，并在 docker 容器外进行了测试，现在我们将把它移入。prisma 容器是用于 Prisma Studio UI 的，后端是我们的后端服务器，mysql 是我们的数据库。以下是每项服务中的关键字段及其作用:

*   这就是我们将称之为容器的东西——为了简单起见，让它与服务名相匹配。

*   如果我们从 docker hub 下载图像，我们会把它放在这里。

*   `build`如果我们*没有*从 docker hub 下载，我们将构建我们的映像，这个块给出了关于哪个文件夹是构建的工作目录以及我们的`Dockerfile`的名称是什么(我们将在下面创建)的说明。

*   任何环境变量都在这里。

*   确保我们在容器死亡时重启它。

*   `links`和`depends_on`在两个容器之间建立一个连接，并指定一个特定的容器在启动之前应该等待另一个容器。

*   `volumes`指定容器应该创建哪种类型的卷。其中有一个`:`的表示我们正在我们的计算机上的一个文件夹和我们的容器中的一个路径之间创建一个链接。没有冒号的那种只是意味着它将在构建步骤中保存该文件夹，这样我们就可以在容器运行时使用它。这对于`node_modules`来说很重要，例如，因为我们希望确保 docker 容器在构建阶段的`npm install`步骤中保留该文件夹，因为运行我们的应用程序需要该文件夹。

现在让我们在*基础*(非前端或后端)`package.json`文件:
中添加一些脚本，让我们的生活更轻松

```
 "start":  "docker-compose up",  "build":  "docker-compose build",  "stop":  "docker-compose down",  "clean":  "docker system prune -af",  "clean:volumes":  "docker volume prune -f",  "seed":  "docker exec -it prisma npm run seed", 
```

为每个集装箱添加 Dockerfiles。

现在我们需要为前端、后端和 prisma 容器创建`Dockerfile`。这些文件包含安装服务器所需的步骤。添加以下三个 docker 文件:

`frontend/Dockerfile` :

```
FROM node:10.16.0

RUN mkdir /app
WORKDIR /app

COPY package*.json ./
RUN npm install

CMD [ "npm", "run", "dev" ] 
```

`backend/Dockerfile` :

```
FROM node:10.16.0
RUN npm install -g --unsafe-perm prisma2

RUN mkdir /app
WORKDIR /app

COPY package*.json ./
COPY prisma ./prisma/

RUN npm install
RUN prisma2 generate

CMD [ "npm", "start" ] 
```

`backend/prisma/Dockerfile` :

```
FROM node:10.16.0
RUN npm install -g --unsafe-perm prisma2

RUN mkdir /app
WORKDIR /app

COPY ./ ./prisma/

CMD [ "prisma2", "dev"] 
```

所有这些都以一个`FROM`块开始，这是我们正在提取的图像。在我们的例子中，我们使用的是 Node.js 的官方版本。然后，我们创建一个`app`文件夹，将`package.json`和`package-lock.json`复制到其中，这样我们就可以运行一个`npm install`来获取我们所有的包。

我们将`prisma`文件夹复制到我们的后端服务器，这样我们就可以生成一个从`prisma.project`文件构建的 prisma 依赖项。这里很酷的一点是，当我们修改我们的模式时，生成的依赖关系将随之改变。prisma 容器需要 prisma 文件夹，以便针对我们的数据库执行迁移，从而创建匹配我们的模式所需的所有不同的表。

我们的前端`Dockerfile`更简单，因为它只需要安装所有的包依赖项，不需要知道`prisma`文件夹。

**配置后端**

*a .将数据库从 SQLite 切换到 MySQL*

我们有 docker-compose 文件，但是有一点你会注意到，我们在这个文件中使用了 MySQL，而我们在 Prisma 设置步骤中指定了 SQLite。让我们通过更新`backend/prisma/project.prisma`文件来解决这个问题。用以下代码更新数据源数据库块:

```
datasource db {
  provider = "mysql"
  url      = "mysql://root:prisma@mysql:3306/prisma"
} 
```

注意，我们给它一个连接字符串，它带有我们在`docker-compose.yml`文件中指定的密码和数据库名称。

*b .删除未使用的后端样板代码*

现在我们可以删除下面的文件，我们不会在我们的项目中使用。

```
backend/src/permissions/*
backend/src/resolvers/*
backend/src/utils.ts
backend/README.md` 
```

*c .更新 Prisma Nexus 中的后端查询*

在 backend/src/index.ts 文件中，添加一个用户查询:

```
t.list.field('users', {
  type: 'User',
  resolve: (parent, args, ctx) => {
    return ctx.photon.users.findMany({})
  },
}) 
```

在同一个文件中，向名为`published`的`feed`查询添加一个布尔输入，在这里我们可以指定我们想要发布还是不发布的帖子。确保将`booleanArg`添加到文件顶部`@prisma/nexus`的导入:

```
import {
  idArg,
  makeSchema,
  objectType,
  stringArg,
  booleanArg,
} from '@prisma/nexus'
// Lines of code omitted for brevity...
//
//
t.list.field('feed', {
  type: 'Post',
  args: {
    published: booleanArg(),
  },
  resolve: (parent, { published }, ctx) => {
    return ctx.photon.posts.findMany({
      where: { published },
    })
  },
}) 
```

**6。验证我们的 Docker-Compose 集群工作情况**

我们使用`npm run build`来构建我们的图像，然后使用`npm start`来启动我们的项目。我们可以使用`npm run stop`停止我们的项目，并使用 clean 命令清理我们的卷、映像和容器。

今后，如果我们使用 npm 安装新的包依赖项，我们需要停止我们的项目并重新构建它，以确保我们的 docker 映像是最新的。当有疑问时，`npm stop`后面跟着`npm run clean`应该可以解决问题。

现在我们需要建立我们的图像，以确保它们的工作。从项目类型的根:

```
npm run build 
```

现在我们已经构建了映像，让我们开始吧`npm start`。从前往`http://localhost:3000`开始。我们应该看到我们的 Next.js 应用程序:

[![Base Next.js website running in docker.](img/2ef06e09c7ccd978edab8a4db963d525.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--INQ5_a20--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/98df867411fedcd6234f2b88ebc69bd6/feedf/starter-frontend.png)

现在让我们转到`http://localhost:4000`，我们应该看到我们的后端 GraphQL 操场:

[![GraphQL Playground website running in docker.](img/1617a53af932792dbce460331e148e8b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Sq8jiCOd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/dd1bd839fd2dfb5ff37a76141fe378d9/feedf/starter-backend.png)

最后，让我们转到`http://localhost:5555`，我们应该会看到 Prisma Studio 应用程序:

[![Prisma Studio website running in docker.](img/79c59092577480faa0ff19b6b03b67a2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gx0n0CuV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codemochi.com/static/3c1505c84ddff2ed9a995e40d2ef8c81/feedf/starter-prisma.png)

厉害！我们所做的是创建一个 starter docker-compose 环境，其中我们运行 3 个 web 服务器和一个数据库服务器，我们可以用一个命令启动它。现在让我们将工作保存在 git 中，然后对其进行简化，这样我们就有了一个很好的构建起点。

```
git add .
git commit -am 'added base projects' 
```

现在，我们已经完成了后端所需的所有更改。在这篇文章的第二部分，我们将移动到前端。

**第一部分视频系列:**

*建筑概述*
[https://www.youtube.com/embed/DmcgOzWy6xI](https://www.youtube.com/embed/DmcgOzWy6xI)

*我们正在建设什么*
[https://www.youtube.com/embed/uMSi5eRE8tM](https://www.youtube.com/embed/uMSi5eRE8tM)

*用 Git 和 NPM*
[https://www.youtube.com/embed/XWAZeyKVQLk](https://www.youtube.com/embed/XWAZeyKVQLk) 建立我们的项目

*创建 Next.js 前端*
[https://www.youtube.com/embed/MBn6NWiRwy8](https://www.youtube.com/embed/MBn6NWiRwy8)

*创建 Prisma 2 后端*
[https://www.youtube.com/embed/bLQOftTh9Zg](https://www.youtube.com/embed/bLQOftTh9Zg)

*创建我们的码头工人作曲*
[https://www.youtube.com/embed/za25acTaGPQ](https://www.youtube.com/embed/za25acTaGPQ)

*创建我们的 Dockerfiles*
[https://www.youtube.com/embed/-IOjuo0VpdY](https://www.youtube.com/embed/-IOjuo0VpdY)

*将 Prisma 2 连接到 MySQL*
[https://www.youtube.com/embed/VcrsVmTxHp0](https://www.youtube.com/embed/VcrsVmTxHp0)

*添加新的查询到我们的后台*
[https://www.youtube.com/embed/s-GTfAF3XxY](https://www.youtube.com/embed/s-GTfAF3XxY)

*启动我们的 Docker 环境*
[https://www.youtube.com/embed/tSXzPDwmpbM](https://www.youtube.com/embed/tSXzPDwmpbM)

那里还有更多！点击这里给我们你的电子邮件，我们会在发布新内容时通知你。我们尊重您的电子邮件隐私，我们绝不会向您发送垃圾邮件，您可以随时退订。

最初发布于 [Code Mochi](https://www.codemochi.com/blog/2019-07-10-prisma-2-nextjs-docker/) 。