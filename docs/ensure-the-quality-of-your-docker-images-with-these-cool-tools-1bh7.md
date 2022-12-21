# 使用这些酷工具确保 Docker 图像的质量

> 原文：<https://dev.to/brpaz/ensure-the-quality-of-your-docker-images-with-these-cool-tools-1bh7>

当我们谈论软件测试时，大多数时候是关于测试应用程序代码，以保证它的安全性，我们定义的代码质量度量和功能/业务需求。

同样的概念，如单元测试或集成测试，也可以应用于基础设施相关的代码。

测试虚拟机和裸机系统可能会很复杂，而且非常耗时耗资源，但使用容器，这一过程就容易多了。

接下来，我将介绍一些可以用来确保 Docker 图像质量的工具。

## 哈顿

Hadolint 是 Dockerfiles 的缩写。

它可以用来确保你的 Dockerfile 不包含结构错误，并遵循官方的[最佳实践](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)。

它还使用外壳下的 [ShellCheck](https://github.com/koalaman/shellcheck) ，这是一个用于 shell 脚本的静态分析工具，用来验证 docker 文件“RUN”指令中的 shell 代码，这非常简洁。

拥有一个写得好的文档是获得高质量图片的第一步。

## 容器结构试验

[容器结构测试](https://github.com/GoogleContainerTools/container-structure-test)是 Google 开发的一个工具，用来验证你的图片的结构。它可以被看作是容器的单元测试框架。

这些测试可用于检查映像中命令的输出，以及验证文件系统的元数据和内容。

您在一个 YAML 文件中定义您的测试，然后针对本地或某个注册表中的现有映像运行该工具。

一个简单的元数据测试，检查环境变量、标签、端口等的存在，可以这样定义:

```
metadataTest:
  env:
    - key: foo
      value: baz
  labels:
    - key: 'com.example.vendor'
      value: 'ACME  Incorporated'
  exposedPorts: ["8080", "2345"]
  volumes: ["/test"]
  entrypoint: []
  cmd: ["/bin/bash"]
  workdir: "/app" 
```

Enter fullscreen mode Exit fullscreen mode

## Goss 和 DGoss

Goss 是另一个基于 YAML 的工具，用于验证服务器的配置。它内置在 Go 中，可以用来测试从虚拟机到容器的各种系统。 [Dgoss](https://github.com/aelsabbahy/goss/tree/master/extras/dgoss) 这是一个方便容器使用 goss 的包装器。

下面是一个使用 Goss 的测试定义的例子:

```
port:
  tcp:22:
    listening: true
    ip:
    - 0.0.0.0
  tcp6:22:
    listening: true
    ip:
    - '::'
service:
  sshd:
    enabled: true
    running: true
user:
  sshd:
    exists: true
    uid: 74
    gid: 74
    groups:
    - sshd
    home: /var/empty/sshd
    shell: /sbin/nologin
group:
  sshd:
    exists: true
    gid: 74
process:
  sshd:
    running: true 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，这很容易理解。

它与“容器结构测试”非常相似，但是具有更大的功能集，允许您验证诸如服务、用户、包、组甚至 HTTP 端点之类的东西。你也许可以通过编写自己的命令，使用容器结构测试来做到这一点，但是 goss 提供了现成的。

我觉得两个都是不错的选择。我还没有在实践中尝试过 Goss。实验一下，看看自己更喜欢什么；)

## 厨房词

测试基础设施代码的流行工具之一是 [Kitchen CI](https://kitchen.ci/) 。

Kitchen 提供了一个测试工具，可以在一个或多个独立的平台上执行基础设施代码。

驱动程序插件架构用于在各种云提供商和虚拟化技术上运行代码，如 vagger、Amazon EC2 和 Docker。

它支持许多测试框架，如 [InSpec](https://www.inspec.io/) 或 [Serverspec](https://serverspec.org/) 。

它基于 Ruby，比 Goss 或 Structure Test 复杂得多。它还需要更多的依赖。然而，这是一个非常强大的工具，你可能想看看。

## 用 Clair 进行安全测试

使用容器时，有时会忘记一个非常重要的方面，那就是安全性。Docker 镜像仍然有一个像 Ubuntu 或 Alpine 这样的操作系统，其中可能有已知安全漏洞的软件包，需要监控和修补。

Clair 是一个开源项目，可以帮助您在 docker 映像中找到这些漏洞。

它包含一个定期更新的数据库，因此可以找到最近发现的问题。

您可以轻松地将它集成到您的 CI 渠道中，以便在出现任何漏洞时得到通知。

最近我还发现了 [Anchore](https://anchore.com/) 非常相似，但是除了它的开源解决方案之外，它还提供了一个带有仪表盘和其他东西的企业解决方案。

## 使用 Dive 控制图像尺寸

为了更快地构建，拥有小而优化的映像是非常重要的。

Dive 是一个非常酷的工具，可以让你探索 docker 图像，查看其图层内容，等等。

这有助于了解图像图层的组织方式，并找到缩小最终图像尺寸的方法。

更酷的是，您可以在您的 CI 系统上运行它，并根据与映像大小相关的一些指标配置它使构建失败。

* * *

## 结论

除了应用程序代码之外，测试基础设施代码对于避免意外问题非常重要。

在更传统的系统中，这并不总是容易的，而且在时间和资源方面的成本很高。

有了 Docker 和这些工具，实现一些基本的测试来保证所需的包被安装，指定的端口被监听，所需的服务正在运行，真的很简单和快速。

进行这些测试可以给你额外的信心，让你相信系统作为一个整体会像预期的那样工作。

* * *

你知道还有什么我在文章中没有提到的很酷的工具吗？随意分享。

感谢阅读。