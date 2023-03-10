# 将 Docker 容器从 Bitbucket 发布到 Google Cloud Container Registry

> 原文：<https://dev.to/juristr/publish-a-docker-container-from-bitbucket-to-google-cloud-container-registry-5dif>

*本帖最初发布于[https://justir . com/blog/2019/08/docker-deploy-bit bucket-ci-to-GCP](https://juristr.com/blog/2019/08/docker-deploy-bitbucket-ci-to-gcp)。前往[juristr.com/blog](https://juristr.com/blog)了解更多内容*

* * *

自动化是关键，这一点我再怎么重复也不为过。在本文中，我们将探讨如何设置和配置您的 Bitbucket 管道，以自动将您的 Docker 容器推送到 Google Cloud Container Registry。

让机器来做繁重的工作，对吗？如果你还记得我的文章“像专业人士一样释放你的 libs！”(回到 2015 年)，谈到自动化，我是 100%的投入。我只想摆脱繁琐的事情，专注于重要的事情。当我们谈到自动化时，自动化构建管道就派上了用场。我已经使用詹金斯多年，自我托管与我们当地的 GitLab 回购。最近我转向了云管道，比如 Travis、CircleCi 和 Bitbucket 管道。

因此，我们来看看如何快速配置一个 Bitbucket 管道，将我们的 Docker 映像部署到一个私有的 repo，托管在 Google Cloud Container Registry 上。

我们的总体想法如下:

*   每次提交到`master`，都会自动触发我们的管道，该管道编译并推送带有`latest`标签的 Docker 映像
*   标签的每次提交都会自动触发流水线，并通过为图像分配相应的标签来推送图像，即 g it 标签`v1.2.0`会导致图像像`hello-world:1.2.0`一样被推送。

好吧，让我们把它分成几个部分，以便更好地理解发生了什么。如果你很快想看完整版，可以直接跳到文章末尾。

## 构建 docker 图像

构建 Docker 映像非常简单。下面是流水线步骤:

```
definitions:
  steps:
    - step: &build-image
        name: Build Docker image
        image: openjdk:8-jdk-alpine
        script:
          - docker build -t helloworld -f docker/hello-world/Dockerfile . 
```

Enter fullscreen mode Exit fullscreen mode

注意，我创建了一个“定义”部分。这将允许我们稍后通过使用类似`*build-image`的“指针”来引用该步骤。

`image`属性设置应该用于该步骤的 Docker 图像。因为这是一个基于 Spring 框架的项目，所以我使用了`openjdk:8-jdk-alpine`图像。

## 向 GCP 推进

首先，您需要在 Bitbucket 管道配置中设置两个环境变量:

*   \$GCLOUD_API_KEYFILE
*   \$GCLOUD_PROJECT

你可以从你的谷歌云平台账户获得这些。

同样，我们为部署创建一个定义部分:

```
definitions:
  steps:
    - step: &build-image
        ...
    - step: &push-gcp
        name: Push to GCP registry
        image: google/cloud-sdk:alpine
        script:
          ... 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们使用`google/cloud-sdk:alpine`图像，它已经包含了我们需要推送到 GCP 注册中心的所有必要内容。

### 向 GCP 求证

作为下一步，我们需要验证我们自己，并选择项目推进到:

```
definitions:
  steps:
    - step: &build-image
        ...
    - step: &push-gcp
        name: Push to GCP registry
        image: google/cloud-sdk:alpine
        script:
          - echo $GCLOUD_API_KEYFILE | base64 -d > ./gcloud-api-key.json
          - gcloud auth activate-service-account --key-file gcloud-api-key.json
          - gcloud config set project $GCLOUD_PROJECT 
```

Enter fullscreen mode Exit fullscreen mode

### 标记我们的 Docker 图像

接下来，我们需要标记我们的图像。我们先来看看步骤再来解释:

```
definitions:
  steps:
    - step: &build-image
        ...
    - step: &push-gcp
        name: Push to GCP registry
        image: google/cloud-sdk:alpine
        script:
          ...
          - export TAG=$(echo "$BITBUCKET_TAG" | sed -e "s/v//g")
          - export SUFFIX=$([[ ! -z "$TAG" ]] && echo ":$TAG" || echo "")
          - export IMAGE_NAME=gcr.io/my-project/hello-world${SUFFIX}
          - docker tag helloworld ${IMAGE_NAME} 
```

Enter fullscreen mode Exit fullscreen mode

因此,`export TAG=$(echo "$BITBUCKET_TAG" | sed -e "s/v//g")`表达式采用了`$BITBUCKET_TAG`,这是一个环境变量，每当 Bitbucket 通过 git 标签被触发时，它就会注入到我们的管道中。这里需要注意的是，我们将 git 标签中的`v`(看起来像`v1.2.0`)替换为空，从而得到`1.2.0`。这只是我在这里做的一些特别的事情。你也可以轻松地选择`v1.2.0`。

接下来在`export SUFFIX=$([[ ! -z "$TAG" ]] && echo ":$TAG" || echo "")`指令中，我基本上计算了我想要添加的后缀来标记我的 docker 图像。我这样做的原因是，我想在`master`触发的构建过程中以及 git 标签触发它时重用这个步骤。区别？嗯，`master`触发建造不会有`$BITBUCKET_TAG`设定。所以我想要的是得到以下内容:

*   `master`触发器构建= > `docker tag helloworld gcr.io/my-project/hello-world`。这将自动标记为`latest`
*   git 标签触发 build => `docker tag helloworld gcr.io/my-project/hello-world:1.2.0`。

回来，每当一个`$TAG`被设置，`export SUFFIX=$([[ ! -z "$TAG" ]] && echo ":$TAG" || echo "")`将用标签填充`$SUFFIX`，否则我们将得到一个空字符串。

### 登录并推送

最后，我们使用之前创建的身份验证令牌登录，并推送标记的图像。

```
definitions:
  steps:
    - step: &build-image
        name: Build Docker image
        image: openjdk:8-jdk-alpine
        script:
          - docker build -t helloworld -f docker/hello-world/Dockerfile .
          - docker save --output tmp-image.docker helloworld
        artifacts:
          - tmp-image.docker
    - step: &push-gcp
        name: Push to GCP registry
        image: google/cloud-sdk:alpine
        script:
          ...
          - cat ./gcloud-api-key.json | docker login -u _json_key --password-stdin https://gcr.io
          - docker push ${IMAGE_NAME} 
```

Enter fullscreen mode Exit fullscreen mode

## 在流水线步骤之间传递编译好的图像

太好了，现在我们知道如何建立和推广我们的形象了。缺少的是如何将步骤`build-image`中编译的图像传递到`push-gcp`步骤。请注意，我们不能只使用一个步骤，因为两者都需要不同的 Docker 映像，一个用于构建，另一个用于将所有工具推送到 GCP。

为了在步骤之间传递图像，我们可以用`docker save`保存它，然后将保存的文件声明为工件。

```
definitions:
  steps:
    - step: &build-image
        name: Build Docker image
        image: openjdk:8-jdk-alpine
        script:
          ...
          - docker save --output tmp-image.docker helloworld
        artifacts:
          - tmp-image.docker
    - step: &push-gcp
        name: Push to GCP registry
        image: google/cloud-sdk:alpine
        script:
          - docker load --input ./tmp-image.docker 
```

Enter fullscreen mode Exit fullscreen mode

## 结论&完整剧本

因此，使用这种设置，每当您对 master 和 commit 进行更改时，都会推送一个新的 Docker 映像，并标记为`latest`。类似地，如果我们决定发布一个新版本，我们设置一个标签并推送它。由于标签应该设置在`master`上，所以带有“最新”标签的新图像和带有我们指定的标签的图像都将在我们的 repo 上完成。

这是完整的剧本😃

```
options:
  docker: true

definitions:
  steps:
    - step: &build-image
        name: Build Docker image
        image: openjdk:8-jdk-alpine
        script:
          - docker build -t helloworld -f docker/hello-world/Dockerfile .
          - docker save --output tmp-image.docker helloworld
        artifacts:
          - tmp-image.docker
    - step: &push-gcp
        name: Push to GCP registry
        image: google/cloud-sdk:alpine
        script:
          - docker load --input ./tmp-image.docker
          # Authenticating with the service account key file
          - echo $GCLOUD_API_KEYFILE | base64 -d > ./gcloud-api-key.json
          - gcloud auth activate-service-account --key-file gcloud-api-key.json
          - gcloud config set project $GCLOUD_PROJECT
          # Tag container & push
          - export TAG=$(echo "$BITBUCKET_TAG" | sed -e "s/v//g")
          - export SUFFIX=$([[ ! -z "$TAG" ]] && echo ":$TAG" || echo "")
          - export IMAGE_NAME=gcr.io/my-project/hello-world${SUFFIX}
          - docker tag helloworld ${IMAGE_NAME}
          # Login to google docker hub
          - cat ./gcloud-api-key.json | docker login -u _json_key --password-stdin https://gcr.io
          - docker push ${IMAGE_NAME}

pipelines:
  tags:
    v*:
      - step: *build-image
      - step: *push-gcp

  branches:
    master:
      - step: *build-image
      - step: *push-gcp 
```

Enter fullscreen mode Exit fullscreen mode

### 可能的改进

将各个步骤封装在一个 shell 脚本中，这个脚本包含在 git repo 中，您只需从 Bitbucket 管道中启动它。这样，如果需要的话，您也可以在本地非常容易地构建和推送新版本。此外，管道保持清洁，更易于维护。