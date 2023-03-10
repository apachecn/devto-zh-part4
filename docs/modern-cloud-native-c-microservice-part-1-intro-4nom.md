# 现代云-原生 C++微服务。第 1 部分—简介

> 原文：<https://dev.to/andreybronin/modern-cloud-native-c-microservice-part-1-intro-4nom>

自从我加入 Insolar 团队并从 C++转到 Golang，一年多过去了。十多年来，C++一直是我的主要编程语言。在 Insolar，我们正在为企业开发一个区块链平台。

我知道一些公司正在将他们庞大的代码库从 C/C++迁移到现代语言，比如 Go 和 Rust。他们还将整体服务拆分为微服务。

我决定提高我在软件架构、产品管理和领导力方面的技能，我开始了一个[开源项目](https://github.com/v2g-demo)。我找了几个远程程序员，组建了一个开发团队。人们希望获得经验和公开投资组合，但他们都使用不同的语言。正因为如此，我决定使用微服务。

一个人想使用 C++，我让他搜索 Cpp 云原生微服务的好例子，但他没有找到。然后我在谷歌上搜索了几天，但也没有找到。我会试着[写我自己的](https://github.com/AndreyBronin/cpp-sample-microservice)。

C++在 web 开发中并不流行，但是现代的 C++17 和 C++20 标准非常酷和有用。

> GCC 9.1 发布了，现在离支持新标准又近了一步。

为什么程序员必须用不同的语言重写他们的代码？他们可以使用熟悉但现代的工具。你怎么想呢?

后端也有 Java，Ruby，JS，PHP 的经验。我将尝试从不同的世界带来最佳实践。

C++在云应用中并不流行，原因有几个。

*   语言很复杂，内存管理

*   标准库不提供网络功能

*   依赖性和第三方管理

*   复杂的建筑系统、连接和潜在的 ABI 问题

*   关于代码风格的圣战

*   工具、代码覆盖率、linters、代码检查器很难设置

我们来谈谈这个原因。

### 语言复杂

我同意，这种语言对于手动内存管理来说很复杂，但是它遵循零开销原则。

> “不用的东西，不用付钱”

这种语言也有 [C++核心准则](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)，遵循[“超集的子集”原则](https://stackoverflow.com/questions/41267021/what-is-subset-of-superset-principle/41267394)。这意味着在大多数情况下你应该使用简单安全的语言子集。

内存泄漏是一件非常糟糕的事情，尤其是在整体架构中。但是今天你有工具来解决这个问题。首先，是开发人员工具，如概要分析和代码清理程序。

### 标准库

C++于 1985 年发布，仍然不提供标准库中的网络功能，但我们有 Boost、POCO、Qt 和几个现代网络库，例如 [Pistache](http://pistache.io) 、 [restbed](https://github.com/Corvusoft/restbed) 、 [evpp](https://github.com/Qihoo360/evpp) 。

### 依赖关系管理

如果你使用任何现代语言，它都有包管理器。在 c++世界中，人们使用几种方法:操作系统包管理器(apt、yum、homebrew)、git 子模块和自己的用 python、perl、bash 等编写的自行车脚本。还有几个特殊的包经理:C [奥南](https://conan.io)，[猎人](https://github.com/ruslo/hunter)，[牛仔](https://buckaroo.pm)， [cpm](https://github.com/iauns/cpm) ， [qpm](https://www.qpm.io) 。

我认为柯南是 cpp 包装管理的最佳和生产就绪的解决方案。我选择它作为我的例子。柯南有两大回复:官方[柯南中心](https://bintray.com/conan/conan-center)和社区[手工艺者](https://bintray.com/bincrafters/public-conan)。

### 构建系统

在 C++世界中有几个构建系统，但是 CMake 成为了事实上的标准。CMake 食谱不容易阅读和理解，但如果你看看 WebPack 或 Gradle，你会同意它也很复杂。开发者应该好好学习生态系统工具。

### 编码风格和文化

一些语言有默认的社区认可的编码风格:Python PEP-8，PHP PSR-2，但是 C++没有。我工作的所有团队都有自己的编码风格，通常基于 [Google C++风格指南](https://google.github.io/styleguide/cppguide.html)。现在我很高兴 Golang 开发人员使用内置的格式工具，而不是争论骆驼，埃及括号等。

开发人员使用哪种语言并不重要，他们应该遵循良好的实践:代码审查、单元测试、自动化代码分析。有很多工具可以做到这一点，见下文。

### 工装

您应该选择工具并建立自己的 CI 渠道。一些好的工具是有偿的。

*   Clang 和 GCC 消毒剂

*   代码覆盖:lcov，[靶心](https://www.bullseye.com)， [CoCo](https://www.froglogic.com/coco/)

*   静态代码分析器:cppcheck，clang-tidy，pvs-studio

### 未来篇

*   云原生 C++微服务。第 3 部分—依赖项、包管理器、Conan.io

*   云原生 C++微服务。第 4 部分—可观察性

*   云原生 C++微服务。第 5 部分—数据库

*   云原生 C++微服务。第 6 部分—JWT 认证

*   云原生 C++微服务。第 7 部分— API，REST，GRPC