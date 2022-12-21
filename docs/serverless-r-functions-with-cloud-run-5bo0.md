# 云运行的无服务器 R 函数

> 原文：<https://dev.to/jinksi/serverless-r-functions-with-cloud-run-5bo0>

如果你是一名 JavaScript 开发人员，使用[无服务器函数](https://en.wikipedia.org/wiki/Serverless_computing)是创建自动伸缩、经济高效的 API 的好方法。[任何能够托管无服务器功能的](https://www.netlify.com/products/functions/) [云](https://cloud.google.com/functions/) [提供商](https://azure.microsoft.com/en-us/services/functions/)都将支持 JS，并且可能拥有可靠的工作流，允许您编写代码并简单地`git push`进行部署。

如果你是与 [R](https://en.wikipedia.org/wiki/R_(programming_language)) 一起工作的开发人员或数据科学家，你就没那么幸运了！任何主要的无服务器云提供商都不支持 r。令人欣慰的是，有一个解决方案开始受到云平台的青睐，这个解决方案叫做**无服务器容器**。我正在使用 [Google Cloud Run](https://cloud.run/) 来托管和管理无服务器容器。Cloud Run 提供了一个全面的“免费层”,因此您可以免费试用。它可以扩展到利用定制的机器类型，包括 GPU 支持，如果你正在运行机器学习任务，这可能是至关重要的。

无服务器容器使我们能够用任何语言和任何依赖项或库编写代码。我们将我们的功能捆绑到 Docker 容器中，部署它们，并且只在它们运行时支付费用。如果该功能遇到流量高峰，更多的容器将立即启动以处理工作负载，并在任务完成后再次释放。

> 跳到回购:[云运行与 R–Hello World(GitHub)](https://github.com/Jinksi/cloudrun-helloworld-r)

## R 在后端

在我的例子中，我使用 R 来生成地理空间地图数据，这些数据将使用前端 React web 应用程序来呈现。我使用 R 库 [`plumber`](https://www.rplumber.io) 创建了一个 REST API，公开了由`app.R`中定义的函数处理的 https 端点。

用`plumber`定义端点，通过用特殊注释修饰 R 函数来定义参数。

下面的函数将接受带有`lat`和`lon`数据的`POST`请求，返回 geojson 数据:

```
# app.R  library(geojsonio)  #' Generates geojson containing 70 points surrounding central coordinate  #' @param lat latitude of central coordinate  #' @param lon longitude of central coordinate  #' @post /geojson  function(lat,  lon){  lat  <-  as.numeric(lat)  lon  <-  as.numeric(lon)  maxRange  <-  0.1  n  <-  70  # Generate data  df  <-  data.frame(  lat  =  runif(n,  min  =  lat  -  maxRange,  max  =  lat  +  maxRange),  lon  =  runif(n,  min  =  lon  -  maxRange,  max  =  lon  +  maxRange),  magnitude  =  runif(n,  min  =  0,  max  =  1)  )  # create geojson string from dataframe  geojsonString  <-  geojson_json(df)  # data to return  geojsonString  }  print('app.R running') 
```

Enter fullscreen mode Exit fullscreen mode

`server.R`设置服务器监听特定端口。这也为探索 REST API 提供了一个大摇大摆的 T2 接口。

```
# server.R  library(plumber)  # 'app.R' is the location of the file containing your endpoint functions  r  <-  plumb("app.R")  # get port number from environment variable  port  <-  strtoi(Sys.getenv("PORT"))  r$run(port=port,  host='0.0.0.0',  swagger=TRUE) 
```

Enter fullscreen mode Exit fullscreen mode

## 定义一个 Docker 容器

一个`Dockerfile`用于定义 Docker 容器需求，安装 R 库并启动我们的服务器脚本。

```
# Use the official R image
# https://hub.docker.com/_/r-base
FROM r-base

# Create and change to the app directory.
WORKDIR /usr/src/app

# Copy local code to the container image.
COPY . .

# Install any R packages
RUN Rscript -e "install.packages('plumber')"

EXPOSE 8080

# Run the web service on container startup.
CMD ["Rscript", "server.R"] 
```

Enter fullscreen mode Exit fullscreen mode

如果您在本地安装了 Docker，您可以使用以下命令构建并运行这个容器:

*   构建:`docker build . -t 'cloudrun-r-helloworld'`
*   运行:`docker run -p 8080:8080 -e PORT=8080 cloudrun-r-helloworld`

## 部署到云运行

现在我们已经有了 Docker 容器，让我们将容器部署到云运行中。有几种方法可以做到这一点，[云运行文档](https://cloud.google.com/run/docs/quickstarts/build-and-deploy)解释了手动部署的方法。总而言之，我们告诉 Cloud Build 构建 docker 映像并将其推送到容器注册中心，然后使用这个映像来部署 Cloud Run 的新版本。

如果您想快速获得部署在云运行上的我的 hello world 示例的克隆，请单击此云运行按钮:

[![Run on Google Cloud](img/76ed020c91ec9f2379b28133e704fa4e.png)](https://console.cloud.google.com/cloudshell/editor?shellonly=true&cloudshell_image=gcr.io/cloudrun/button&cloudshell_git_repo=https://github.com/Jinksi/cloudrun-helloworld-r.git)

> 看看[云运行按钮回购](https://github.com/GoogleCloudPlatform/cloud-run-button)来创建自己的。

## 与`git push`连续部署

比起手动部署，我更喜欢将我的更改提交给 Git 存储库，让部署自动进行*。*

 *我们可以使用谷歌的[云构建](https://cloud.google.com/cloud-build/)在每次推送 git repo 时自动部署我们的容器。您需要将您的代码放在 Github 或另一个云 git 存储库中的`git repo`中。

> 如果是第一次运行，您可能需要启用 GCP 计费和 API。参见[云运行文档](https://cloud.google.com/run/docs/continuous-deployment)

要使用云构建，我们创建一个`cloudbuild.yaml`文件，并将其提交到存储库根文件夹。

```
# cloudbuild.yaml

# Replace $PROJECT_ID with your GCP project ID
# Replace [SERVICE-NAME] with the desired Cloud Run service name (e.g. hello-world)
# Replace [REGION] with the GCP region you are deploying to (e.g. asia-northeast1)

steps:
  # build the container image
  - name: 'gcr.io/cloud-builders/docker'
    args: ['build', '-t', 'gcr.io/$PROJECT_ID/[SERVICE-NAME]', '.']
    # push the container image to Container Registry
  - name: 'gcr.io/cloud-builders/docker'
    args: ['push', 'gcr.io/$PROJECT_ID/[SERVICE-NAME]']
    # Deploy container image to Cloud Run
  - name: 'gcr.io/cloud-builders/gcloud'
    args:
      [
        'beta',
        'run',
        'deploy',
        '[SERVICE-NAME]',
        '--image',
        'gcr.io/$PROJECT_ID/[SERVICE-NAME]',
        '--region',
        '[REGION]',
        '--platform',
        'managed',
        '--quiet',
      ]
images:
  - gcr.io/$PROJECT_ID/[SERVICE-NAME] 
```

Enter fullscreen mode Exit fullscreen mode

现在创建一个云构建触发器:

1.  转到[云构建触发器页面](https://console.cloud.google.com/cloud-build/triggers)
2.  点击**创建触发器**
3.  选择您的存储库
4.  在*构建配置*中选择`cloudbuild.yaml`
5.  点击**创建**
6.  将变更推送到您的存储库中
7.  在[云构建控制台](https://console.cloud.google.com/cloud-build/builds)中监控构建进度

一旦设置完成，任何时候您推送到这个存储库，Cloud Build 都会构建 Docker 容器并部署一个新的版本到 Cloud Run🎉

[阅读更多云运行文档](https://cloud.google.com/run/docs/continuous-deployment)。

## 你好云跑

既然我们已经将我们的容器部署到 Cloud Run，我们就可以高枕无忧，确信我们的 R 函数将*正常工作*,而不必管理服务器、负载平衡等。🏖

在[云运行控制台](https://console.cloud.google.com/run)中，我们可以将我们的服务映射到一个自定义域，增加服务内存分配，查看日志和使用统计等。Cloud Run 还提供每月慷慨的[免费等级](https://cloud.google.com/run/pricing)。

感谢阅读！我希望我已经向您展示了如何开始在 Cloud Run 中运行 serverless R。如果你觉得这篇文章有用或有任何问题，请告诉我，并随时分享！

## 更多信息:

*   [云随 R 跑——Hello World(GitHub)](https://github.com/Jinksi/cloudrun-helloworld-r)
*   [云运行文档](https://cloud.google.com/run/docs/)
*   [水管工](https://www.rplumber.io)

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [金克斯](https://github.com/Jinksi)/[cloud run-hello world-r](https://github.com/Jinksi/cloudrun-helloworld-r)

### 用 R，plumber 和 Docker 创建无服务器函数的例子

<article class="markdown-body entry-content container-lg" itemprop="text">

# 云运行与 R–Hello World

<g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png">⭐️</g-emoji> [阅读博文:用谷歌云运行无服务器 r 功能](https://ericjinks.com/blog/2019/serverless-R-cloud-run/) <g-emoji class="g-emoji" alias="star" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2b50.png">⭐️</g-emoji>

[![Run on Google Cloud](img/bcdf366e9a2810c6343d53aa8d9c70a8.png)](https://deploy.cloud.run)

利用`R`、`plumber`、`Docker`、[云运行](https://cloud.google.com/run/)创建无服务器功能的例子。

在[https://cloud run-hello-r-TJ F3 C3 jq 4 q-an . a . run . app/_ _ Swagger _ _/](https://cloudrun-hello-r-tjf3c3jq4q-an.a.run.app/__swagger__/)看一下 Swagger API UI

## 文件描述:

*   `app.R`描述服务器的端点和各自的请求处理器功能
*   `server.R`用水管工设置服务器环境
*   `Dockerfile`描述用于运行应用程序的 docker 容器的设置
*   `cloudbuild.yaml`将提交推送到主服务器时，设置持续部署到云运行

</article>

[View on GitHub](https://github.com/Jinksi/cloudrun-helloworld-r)*