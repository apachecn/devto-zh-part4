# 使用 Next.js Docker 开发环境分 4 步进行热模块重装

> 原文：<https://dev.to/codemochi/hot-module-reloading-with-next-js-docker-development-environment-in-4-steps-32i8>

这篇文章将介绍如何在运行 docker 环境的 Next.js 中添加热模块重载。这对于 Next.js 应用程序的开发至关重要，因为当您更改应用程序文件夹中的文件时，您希望应用程序重新加载您的网页。当我们完成时，您对 React 代码库所做的任何更改都将自动重新加载，并且您应该会在更改后的 1 秒钟内看到更改显示在您的开发环境中。

**我们正在做的事情的视频:**

[https://www.youtube.com/embed/4w0tM-dXXEQ](https://www.youtube.com/embed/4w0tM-dXXEQ)

步骤 1:为你的 Next.js 应用程序创建一个 docker 文件。

在[之前的博客文章](https://www.codemochi.com/blog/2019-07-08-prisma-2-nextjs-docker/)中，我们已经为我们的 Next.js 应用程序构建了一个`Dockerfile`，但是如果你第一次尝试对你的应用程序进行 dockerize，你需要创建一个`Dockerfile`。

```
FROM node:10.16.0

RUN mkdir /app
WORKDIR /app

COPY package*.json ./
RUN npm install

CMD [ "npm", "run", "dev" ] 
```

这里的想法是从一个基本的 ubuntu 映像开始，将`package.json`和`package-lock.json`文件复制到目录中，并运行一个`npm install`命令。

**步骤 2:在您的主机和 docker 环境之间添加卷挂载。**

我们使用 docker-compose 在本地操作系统(`/frontend`)上的`/app`目录和实际工作目录之间创建一个卷挂载。这个应用程序的服务块看起来像这样——其中 frontend 是我们的 Next.js 应用程序所在的目录。

```
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
```

**步骤 3:为 node_modules 和添加 docker 卷。下一文件夹。**

正如我们在步骤 2 中提到的，我们还需要为`node_modules`和`.next`文件夹添加 docker 卷，以便 docker 将使用 docker 文件夹而不是主机操作系统文件夹。

步骤 4:为我们的 Next.js 应用程序修改我们的 webpack 中间件。

在`frontend/next.config.js`文件中，我们需要给 webpack 中间件关于何时重新加载自身的指令。如果您的应用程序没有 next.config.js 文件，您可以简单地添加以下代码:

```
module.exports = {
  webpackDevMiddleware: config => {
    config.watchOptions = {
      poll: 1000,
      aggregateTimeout: 300,
    }
    return config
  },
} 
```

对于我们之前的项目，我们不需要创建一个全新的对象来导出，我们只需要将`webpackDevMiddleware`块添加到我们已经存在的配置对象:

```
const withCSS = require('@zeit/next-css')

module.exports = withCSS({
  target: 'serverless',
  env: {
    BACKEND_URL: process.env.BACKEND_URL,
  },
  webpackDevMiddleware: config => {
    config.watchOptions = {
      poll: 1000,
      aggregateTimeout: 300,
    }
    return config
  },
}) 
```

应该就是这样了——现在您应该看到，当您从 Next.js 中更新 react 组件时，它们会立即在您的 web 浏览器中显示为已更改，而无需运行`docker-compose down`和`docker-compose up`。

从那里来的还有更多！
[点击这里给我们您的电子邮件，我们会在发布新内容时通知您。](https://gmail.us20.list-manage.com/subscribe?u=37f38485b2c7cff2f3d9935b5&id=e3bc056dde)我们尊重您的电子邮件隐私，我们绝不会向您发送垃圾邮件，您可以随时退订。

最初发布于 [Code Mochi](https://www.codemochi.com/blog/2019-08-27-nextjs-hmr/) 。