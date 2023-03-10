# 去 SonarQube

> 原文：<https://dev.to/remast/go-for-sonarqube-4iho>

静态代码分析是发现 bug、竞争条件、代码气味或检查代码是否符合编码约定的一种非常好且简单的方法。我将解释为什么使用 SonarQube 对 Go 代码进行静态分析是有用的，并向您展示它是如何做到的。

## Go Vet

Go 已经附带了工具 *vet* ，它对 Go 代码进行静态代码分析。要使用*兽医*只需运行`go vet source/directory/*.go`。Vet 将执行各种检查，如检测隐藏变量、nil 函数比较和许多其他检查(完整列表见`go doc cmd/vet`),并列出所有违规。兽医是伟大的，因为它执行许多基本检查非常快。

## 去棉绒

然而，如果我们想要检测样式错误、重复代码或者甚至检查安全问题，仅仅通过 vet 是不够的。所有这些检查都可以由像 [Go Meta Linter](https://github.com/alecthomas/gometalinter) 或 [GolangCI-Lint](https://github.com/golangci/golangci-lint) 这样的棉绒机来完成。这些是[经典的 linters](https://en.wikipedia.org/wiki/Lint_(software)) ，它们对 go 代码执行静态代码分析，并以标准化的格式报告结果。这些 linters 可以集成在编辑器中，如 [VisualStudio Code](https://code.visualstudio.com/) 、 [Vim](https://www.vim.org/) 或 [GoLand](https://www.jetbrains.com/go/) 。因此，这些 linters 执行 Go 代码的深入分析，并可以检测多种违规。根据 linter 的配置，林挺可能会在您的构建管道中花费很长时间。

静态代码分析对于计算源代码的关键指标也很有用，比如代码行数或圈复杂度。与代码覆盖率或单元测试计数相结合，这些关键指标可以为您的源代码状态提供良好的第一印象。它们可以显示在项目中类似[地图册](https://bitbucket.org/atlassian/atlasboard)的仪表板上，供所有人查看。

但是，我们从哪里获得这些关键指标呢？像 Go Meta Linter 这样的 Linter 可以计算大多数关键指标，如[循环复杂度](https://en.wikipedia.org/wiki/Cyclomatic_complexity)。单元测试计数的代码覆盖率等其他关键指标是从测试中计算出来的。这样，我们可以在持续集成过程中计算关键指标。

## 棉绒不够吗？为什么是 SonarQube？

如果我们想知道源代码的关键指标是如何随着时间的推移而演变的呢？自从上一个版本发布以来，我们已经能够提高代码覆盖率了吗？每次冲刺我们要添加多少行代码？我们项目中的哪些部分是最大的？

这就是 SonarQube 发挥作用的地方。SonarQube 使用静态代码分析、代码覆盖率和单元测试来分析源代码。这样 SonarQube 就能回答上面所有的问题。您可以看到版本 1.0.0 有 1.562 行代码，覆盖率为 85%，而现在您在版本 2.1.0 有 3.842 行代码。如果你仔细观察，你会发现软件包 *auth* 增长最多。在 SonarQube 中，您可以看到您的关键指标和源代码是如何随着时间和特定版本的变化而变化的，如下所示。

[![SonarQube results over time](img/74d7efa035ec5fbfbdeb15d6c3101847.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wIph_x0N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5y4hi16l3dtraeqlwjyv.png)*sonar cube 随时间的结果*

如果您有一个项目或一家公司在用不同的编程语言开发服务，那么使用 SonarQube 作为关键源代码度量还有另一个好处。SonarQube 能够为许多不同的编程语言计算这些关键指标，如 Go、Java、C#、JavaScript 和许多其他语言。这样，你甚至可以为使用许多不同编程语言的项目或公司计算组合的关键指标。例如，您您的项目总共有 12.346 行代码，总代码覆盖率为 76%。JavaScript 前端有 3.704 行代码，大约是 Java 后端的三分之一。

让我们看看如何使用 SonarQube 来分析我们的 Go 代码。

## SonarQube for Go

要使用 sonar cube 分析 Go 代码，您需要一个正在运行的 sonar cube。您可以使用 docker run-name sonar cube-p 9000:9000 sonar cube 在本地运行 sonar cube。Go 插件自带 SonarQube，所以不需要手动安装。现在你已经准备好分析你的源代码了。为此，我们使用 SonarQube 提供的[声纳扫描工具](https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner)，它需要一个 Java 运行时环境。接下来，我们需要一个名为 *sonar-project.properties* 的配置文件，放在项目的根目录下。配置文件告诉 SonarQube 要分析哪些资源，项目的名称和版本，以及在哪里可以找到测试覆盖。参见下面的示例 *sonar-project.properties* :

```
sonar.projectKey=de.red6:service_sonar
sonar.projectName=service_sonar
sonar.projectVersion=1.0.0
sonar.host.url=http://localhost:9000
sonar.login=**SECRET**

sonar.sources=.
sonar.exclusions=**/*_test.go,**/vendor/**,**/testdata/*

sonar.tests=.
sonar.test.inclusions=**/*_test.go
sonar.test.exclusions=**/vendor/**
sonar.go.coverage.reportPaths=bin/cov.out 
```

Enter fullscreen mode Exit fullscreen mode

在使用 SonarQube 分析您的代码之前，您需要运行测试并记录代码覆盖率(例如使用`go test -short -coverprofile=bin/cov.out `go list./..|grep -v vendor/``)。SonarQube 将使用记录的代码覆盖率。

现在你可以使用*声纳扫描仪*运行你的第一个声纳立方体分析。您的代码将被分析并上传到 SonarQube，包括代码覆盖率。

## 举例

你可以找到一个样例项目 [service_sonar](https://github.com/remast/service_sonar) ，它为 SonarQube 提供了完整的设置。在示例项目中，您可以使用`make sonar`运行 SonarQube 分析。如果你的机器上没有安装*声纳扫描仪*工具，你也可以使用声纳扫描仪的 dockerized 版本(参见[示例项目](https://github.com/remast/service_sonar/blob/master/README.md))。dockerized 版本非常适合 CI 环境，如 [GitlabCI](https://about.gitlab.com/product/continuous-integration/) 。

配置文件 *sonar-project.properties* 中的所有设置也可以使用命令行提供。这对 SonarQube 主机和登录尤其有意义。因此，在 CI 管道中，您可以运行`sonar-scanner-Dsonar.host.url="http://sonar.mycompany.com" -Dsonar.login="**MY_TOKEN**"`。

示例项目还包括一个在*的 Gitlab CI 配置。gitlab-ci.yml* 。Gitlab CI 管道在舞台声纳中执行声纳立方体分析。它需要环境变量`SONAR_HOST`和`SONAR_API_TOKEN`中 SonarQube 的主机和登录。

运行 sonar cube 分析后，您会在 sonar cube 中看到结果:

[![Result of first SonarQube analysis](img/d5a61e72e80e75bd350449533893d43f.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--IJ8p_Rs_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7dkvjldnj2compu3c9g8.png) *第一次声纳组分析结果*

[![Overview of SonarQube results](img/0637639063012b9edd5c5a159eb633a3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mmcaNfD1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yupeh3xi94taacasvzfg.png)*sonar cube 结果概述*

## 总结

我们已经学习了如何使用内置工具 *vet* 静态分析 Go 代码，比如 Go Meta Lint 或 Go 的 SonarQube 插件。所有这些工具都能够计算源代码的关键指标。

SonarQube 有两个好处:

*   首先，它可以跟踪关键指标随时间的变化，从而提供关于代码质量如何发展的有用见解。

*   第二，SonarQube 可以分析许多不同编程语言的代码，因此你可以计算整体关键指标。

所以你绝对应该试一试 SonarQube。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [复赛](https://github.com/remast) / [服务 _ 声纳](https://github.com/remast/service_sonar)

### 用 SonarQube 分析 Go 代码的例子(包括 Github 动作)。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![SonarCloud](img/28f67cee3fbda21bafa7ee43119c603d.png) ](https://sonarcloud.io/dashboard?id=de.red6%3Aservice_sonar) [ ![](img/9d8a0041e2bccf4c961b945e6b435e76.png)](https://github.com/remast/service_sonar/workflows/Main%20Workflow/badge.svg)

# 带 Go 的 SonarQube 示例

使用 sonar cube 对博客文章[的 Go 代码进行静态分析的例子。这个例子使用 Go 模块，所以需要设置`GO_MODULES="on"`。](https://medium.com/@remast/go-for-sonarqube-ffff5b74f33a)

## 启动 SonarQube

```
docker run -d --name sonarqube -p 9000:9000 sonarqube 
```

## 运行 SonarQube 分析

```
docker run --rm --network host --mount type=volume,src="$(pwd)",dst=/opt/app,type=bind -w=/opt/app red6/docker-sonar-scanner:latest sonar-scanner -Dsonar.login=**SECRET** 
```

## Github 操作

该项目还使用了 Github 动作，如[用 SonarCloud](https://github.com/SonarSource/sonarcloud-github-action) 扫描代码中所述。

代码覆盖率在测试过程中被分析，然后使用[上传工件](https://github.com/actions/upload-artifact)和[下载工件](https://github.com/actions/download-artifact)报告给 SonarCloud。

</article>

[View on GitHub](https://github.com/remast/service_sonar)

*本帖原载于[媒体](https://medium.com/red6-es/go-for-sonarqube-ffff5b74f33a)。*