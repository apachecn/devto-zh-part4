# 疯子的 Haskell:设置

> 原文：<https://dev.to/drbearhands/haskell-for-madmen-setup-4cj9>

在我们开始写任何代码之前，让我们确保我们的环境设置正确。

### 1。安装依赖项

构建 Haskell 代码和管理依赖关系最简单的方法是使用
[栈](https://docs.haskellstack.org/en/stable/README/)。

我们还需要码头工人。这个
并不是绝对必要的，但是它会让一些事情变得容易得多，所以我
会广泛地使用它。

最后，我将演示如何在
[Gitlab](https://gitlab.com) 上设置您的构建管道。您可以使用其他存储库和 CI/CD 工具，
但是您必须自己适应本教程中的知识。

我将假设您已经成功安装了 stack、docker 和
[git](https://git-scm.com/) 。您可能需要在这个过程中安装一些额外的
依赖项，但是这对于消费者系统来说很少见。

### 2。创建项目

让我们来测试你已经正确安装了栈。在控制台中运行这个:

```
stack new haskell-tutorial
cd haskell-tutorial 
```

Enter fullscreen mode Exit fullscreen mode

并且第一次运行项目，这将构建项目的
依赖项，包括编译器，第一次可能需要一段时间。

```
stack run 
```

Enter fullscreen mode Exit fullscreen mode

### 3。初始化存储库

在 gitlab 上创建一个新的存储库，然后在本地初始化您的存储库并
进行第一次提交:

```
git init
git remote add origin <your repository URL>
git add .
git commit -m"initial commit"
git push -u origin master 
```

Enter fullscreen mode Exit fullscreen mode

### 4。设置 CI/CD 管道

因为 Haskell 有如此强大的静态检查，所以拥有一个 CI 管道是非常有意义的，即使你没有对结果做任何事情。

创建一个`.gitlab-ci.yml`文件，用你选择的编辑器打开它。

缓存依赖关系的基本 CI 管道如下所示:

```
stages:
  - build

variables:
  DOCKER_DRIVER: overlay2
  STACK_ROOT: ${CI_PROJECT_DIR}/.stack

build-backend:
  stage: build
  image: haskell:latest
  cache:
    paths:
      - ${STACK_ROOT}
      - .stack-work
  script:
    - stack
      --stack-root ${STACK_ROOT}
      build 
```

Enter fullscreen mode Exit fullscreen mode

您可以将这个配置推送到 Gitlab，它会构建您的项目。第一次运行需要一段时间来下载和安装编译器 ghc。

#### 4.1 基于阿尔卑斯山的图像(可选)

你可能(和我一样)不喜欢使用`haskell`图像，而是根据 alpine 推出自己的
。这有点复杂，只针对那些已经熟悉 gitlab 和 docker 的
用户。

首先，我们将为构建 Haskell
应用程序创建自己的容器映像。姑且称之为`dockerfiles/buildenv`。

```
FROM alpine:latest

ENV PATH ${PATH}:/root/.cabal/bin:/root/.local/bin

RUN apk add --no-cache ghc curl musl-dev zlib-dev postgresql-dev
RUN curl -sSL https://get.haskellstack.org/ | sh 
```

Enter fullscreen mode Exit fullscreen mode

让我们在 CI 渠道中构建和使用它。

```
stages:
  - build-requirements
  - build

variables:
  DOCKER_DRIVER: overlay2
  BUILD_ENV_IMAGE_TAG: ${CI_REGISTRY_IMAGE}/buildenv
  BUILD_ENV_IMAGE_TAG_VERSIONED: ${BUILD_ENV_IMAGE_TAG}:${CI_COMMIT_SHA}
  STACK_ROOT: ${CI_PROJECT_DIR}/.stack

build-environment:
  stage: build-requirements
  when: manual
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker login -u gitlab-ci-token -p ${CI_BUILD_TOKEN} registry.gitlab.com
    - docker build -f dockerfiles/buildenv -t ${BUILD_ENV_IMAGE_TAG} .
    - docker tag ${BUILD_ENV_IMAGE_TAG} ${BUILD_ENV_IMAGE_TAG_VERSIONED}
    #push twice, both latest and versioned tags
    - docker push ${BUILD_ENV_IMAGE_TAG}
    - docker push ${BUILD_ENV_IMAGE_TAG_VERSIONED}

build-backend:
  stage: build
  image: ${BUILD_ENV_IMAGE_TAG}:latest
  cache:
    paths:
      - ${STACK_ROOT}
      - .stack-work
  script:
    - stack
      --stack-root ${STACK_ROOT}
      build
      --system-ghc 
```

Enter fullscreen mode Exit fullscreen mode

很有可能你现在遇到了一个类似这样的错误:

```
No setup information found for ghc-8.6.5 on your platform.
This probably means a GHC bindist has not yet been added for OS key 'linux64-ncurses6'.
Supported versions: ghc-7.10.3, ghc-8.0.1, ghc-8.0.2, ghc-8.2.1, ghc-8.2.2 
```

Enter fullscreen mode Exit fullscreen mode

这是因为文件`stack.yaml`指定了使用哪个解析器(
堆栈管理的依赖项列表),这反过来决定了我们需要的 ghc
的版本，而那个版本还没有被移植到 alpine。然而，
错误消息中提到的版本是*而不是*我们可以使用的版本，因为
我们使用的是- system-ghc，正确的版本应该列在创建您的构建环境的
CI 作业的日志中。我的情况:

```
(25/37) Installing ncurses-terminfo-base (6.1_p20190518-r0)
(26/37) Installing ncurses-terminfo (6.1_p20190518-r0)
(27/37) Installing ncurses-libs (6.1_p20190518-r0)
(28/37) Installing ghc (8.4.3-r0) 
```

Enter fullscreen mode Exit fullscreen mode

我需要使用 ghc-8.4.3 指定一个解析器。于是，在`stack.yaml`里，我把
`resolver: lts-13.27`改成了`resolver: ghc-8.4.3`。在 https://www.stackage.org/你可以找到解决者的列表

# 5。绳索

我不打算涵盖 ide，因为这是一个我没有兴趣涉足的热门话题。然而，我发现使用连字对 Haskell 中的
代码可读性有很大帮助，所以我推荐使用支持
的 IDE。