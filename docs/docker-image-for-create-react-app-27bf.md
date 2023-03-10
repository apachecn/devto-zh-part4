# 创建-反应-应用程序的 Docker 图像

> 原文：<https://dev.to/edgargonzalez/docker-image-for-create-react-app-27bf>

[![Alt Text](img/3d48d5dca31f56fcc1be3f13b86ab036.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--noWyiiA0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2gc3jaeykm3tq27tff3b.png)

因为我非常喜欢将我的所有开发环境归档，所以我不想在我的机器上安装`node`、`npm`(或`yarn`)和[、`create-react-app`、](https://github.com/facebook/create-react-app)、
，甚至不想生成`create-react-app`脚手架。

为此，我创建了一个简单的[docker 映像](https://hub.docker.com/r/edgargonzalez/create-react-app)，它只做一件事，安装`create-react-app`并生成一个`create-react-app`脚手架。

## 用法

```
$ docker run --rm -v $PWD:/app edgargonzalez/create-react-app my-app

Creating a new React app in /app/my-app.

Installing packages. This might take a couple of minutes.
Installing react, react-dom, and react-scripts...
...
Happy hacking! 
```

Enter fullscreen mode Exit fullscreen mode

## Github 回购

Docker 映像的代码位于以下存储库中:

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)T1】埃德加/[docker-create-react-app](https://github.com/edgar/docker-create-react-app)

### 创建-反应-应用程序的 Docker 图像

<article class="markdown-body entry-content container-lg" itemprop="text">

# 创建-反应-应用程序的 Docker 图像

避免在开发机中安装`node`、`npm` ( `yarn`)、 [`create-react-app`](https://github.com/facebook/create-react-app) 的 docker 镜像，生成`create-react-app`脚手架。

## 使用

```
$ docker run --rm -v $PWD:/app edgargonzalez/create-react-app my-app

Creating a new React app in /app/my-app.

Installing packages. This might take a couple of minutes.
Installing react, react-dom, and react-scripts...
...
Happy hacking! 
```

</article>

[View on GitHub](https://github.com/edgar/docker-create-react-app)