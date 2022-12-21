# 为设置 CI/CD 管道。带圈的网芯

> 原文：<https://dev.to/herocod3r/setup-a-ci-cd-pipeline-for-net-core-with-circleci-292d>

CircleCI 在最近几年显示了他们的承诺，确保他们使持续集成的任务和交付方式对开发人员来说更加容易和快速。在当今市场上的许多 CI/CD 工具中，我认为 CircleCI 有一种最简单直接的方法。利用容器。

然而，在列出编程语言的时候，他们写了一些方便的图片，因为他们忽略了这些图片。网...不管怎样，笑话是在他们身上。我最近在一个项目中探索使用它们，并得出以下结论。

我深入研究了它们的便利映像，发现它们基本上只是将 docker 与该语言的基础映像捆绑在一起。

所以我很容易就想出了一个。网芯。我用过。Net Core 3，但同样的方法应该在以前的版本上工作

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png)[【herocod 3r】](https://github.com/Herocod3r)/[循环。净〔T4〕](https://github.com/Herocod3r/CircleCiDocker.Net)

### CircleCi 便利图片。网络核心

<article class="markdown-body entry-content p-5" itemprop="text">

# CircleCiDocker.Net

CircleCi 便利图片。网络核心

</article>

[View on GitHub](https://github.com/Herocod3r/CircleCiDocker.Net)

建立图像后，我将它推送到 [Docker Hub](jetcipher/circleci-dotnet-core)

### 下一步是添加我的 circle ci 配置文件

在根目录下创建一个名为*的文件夹。circleci* 在其中创建一个名为 *config.yml* 的文件

然后定义以下内容

```
version: 2
jobs:
  build:
    docker:
      - image: jetcipher/circleci-dotnet-core:3.0
    steps:
      - checkout
      - run:
          name: Restore
          command: dotnet restore
          working_directory: MyProject.Api
      - run:
          name: Build
          command: dotnet build -c Release
          working_directory: MyProject.Api
      - run:
          name: Running Tests
          command: dotnet test
          working_directory: MyProject.Tests
      - setup_remote_docker:
          docker_layer_caching: true
      - run:
          name: Build and push Docker image
          command: |
            docker build -t MyProject .
            echo $DOCKER_PWD | docker login -u $DOCKER_LOGIN --password-stdin
            docker tag boku herocod3r/MyProject
            docker push herocod3r/MyProject 
```

我的项目是你项目的名字，我相信你已经知道流程了🙂
在 CircleCI 项目的仪表板上设置环境变量 *DOCKER_PWD* 你的 DOCKER 密码和 *DOCKER_LOGIN* 你的 docker id

提交和推送，瞧，您的构建现在应该运行了！！