# 加速 Docker 和 CI 的 Go 模块

> 原文：<https://dev.to/evilmartians/speeding-up-go-modules-for-docker-and-ci-4ebe>

最后，Golang world 在生态系统中有一个内置的常规依赖管理器:Go 模块。在 Go 1.11 中作为选择加入功能开始的功能已经被社区广泛采用，当 Go 模块被默认启用时，我们离 Go 1.13 已经很近了。选择“最佳”工具这一令人愉快的难题终于可以解决了。

我不得不提到两个非常贴近我内心的特性:

不再有监禁！根据我多年的经验，我已经习惯于将我所做的一切都存储在主目录中的`~/Projects/`及其子文件夹中，不管是什么编程语言。所以，被迫把我的 Golang 东西放在另一个特定的地方，并尊重路径中的 SCM url 是一种真正的痛苦，并使日常的`cd`操作感觉像是一件苦差事。不再是一个问题！
–不再复仇！依赖关系更新不会产生巨大的 PR 差异，并且存储库更轻。我可以从我的源代码中删除`vendor`文件夹，然后忘掉它。

迁移到 Go 模块[非常简单](https://github.com/golang/go/wiki/Modules#how-to-use-modules)并且不会花费超过几分钟的时间，特别是如果你使用[任何一个支持的包管理器](https://tip.golang.org/pkg/cmd/go/internal/modconv/?m=all#pkg-variables)进行迁移的话。

```
$ go mod init
$ rm vendor/*
$ go test ./...
$ git add .
$ git commit 
```

差不多就是这样！

有了 Go 模块，你的依赖就不再是源代码的一部分了。工具链自行下载它们，保持模块最新，并将它们缓存在本地的`$GOPATH/pkg/mod`中以备将来使用。当您的所有流程都发生在像笔记本电脑这样的有状态环境中时，这听起来很完美，但是在您的 CI 管道或 Docker 中的无状态构建呢？时不时的 Go 会下载你依赖的每一个项目，浪费你宝贵的时间。让我们用一些缓存来解决这个问题吧！

# 在 CI 上缓存

在 CI 上缓存构建之间的依赖关系是一种很常见的情况，因此一些服务提供了一种简化的、特定于生态系统的语法来简化这种情况。唉，我还没有在流行的 ci 上找到具体的 Go 模块缓存，还是手动吧。

如果你用 TravisCI，那就非常简单了。只需将这些行添加到您的`.travis.yml`配置中，您就一切就绪:

```
cache:
  directories:
    - $GOPATH/pkg/mod 
```

在我最喜欢的 CircleCI 上设置依赖项缓存稍微有点冗长。在下面的代码中包装`go mod download`或您的构建步骤。Golang 将处理缺失的依赖项，CircleCI 将根据`go.sum`文件的内容在构建之间缓存它们。

```
 - restore_cache:
          keys:
            - go-modules-v1-{{ checksum "go.sum" }}
            - go-modules-v1
      # get dependencies here with `go mod download` or implicitly 
      # with `go build` or `go test`
      - save_cache:
          key: go-modules-v1-{{ checksum "go.sum" }}
          paths:
            - "/go/pkg/mod" 
```

以下是我在 CircleCI 上的小项目的推进结果:

之前:

```
`go test ./...` => 00:20s 
```

缓存预热后:

```
Restoring Cache => 00:03s
`go test ./...` => 00:06s
Saving Cache    => 00:00s 
```

一点也不差:2 倍的 CI 构建速度，而且是免费的。

# 缓存在 Docker 中

对于如何使用 Docker，有两种完全不同的用例:在开发过程中隔离应用程序及其环境，以及打包产品构建。

## 在发展

如果您遵循测试驱动开发(TDD)缓存，Go 模块可以显著提高您的开发效率。您肯定知道拥有尽可能快的测试套件有多重要。

使用 Docker Compose，将您的模块缓存在一个单独的卷中，并查看性能提升。我节省了 20 秒。对于一个小小的改变来说还不错！

下面是一个最小的`docker-compose.yml`，为了简洁起见进行了简化，突出显示了音量变化:

```
version: '3'
services:
  app:
    image: application:0.0.1-development
    build:
      context: .
      dockerfile: docker/development/Dockerfile
    volumes:
      - .:/app
      - go-modules:/go/pkg/mod # Put modules cache into a separate volume
  runner:
    <<: *app
  test:
    <<: *app
    command: go test ./...
volumes:
  go-modules: # Define the volume 
```

## 生产中

对于生产构建，我们可以利用[层缓存](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache)功能。依赖关系的变化不如代码本身频繁——让我们在构建阶段之前将它作为`Dockerfile`中的一个单独步骤。

```
# `FROM` and other prerequisites here skipped for the sake of brevity

# Copy `go.mod` for definitions and `go.sum` to invalidate the next layer
# in case of a change in the dependencies
COPY go.mod go.sum ./
# Download dependencies
RUN go mod download

# `RUN go build ...` and further steps 
```

# 总而言之

引入 Go 模块是一个激动人心的时刻，对 Golang 社区来说是一个重大的解脱。它给我们带来了许多期待已久的优秀功能。如果您还没有尝试过模块，请不要犹豫。迁移到它非常容易，但是不要忘记更改您的 CI 或 Docker 设置，以避免下载开销并保持您的构建速度极快。