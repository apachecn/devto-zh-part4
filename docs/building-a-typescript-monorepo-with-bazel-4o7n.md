# 用 Bazel 构建一个类型脚本 monorepo

> 原文：<https://dev.to/lewish/building-a-typescript-monorepo-with-bazel-4o7n>

在 [Dataform](https://dataform.co/?utm_medium=organic&utm_source=dev_to&utm_campaign=bazel_ts_monorepo) 时，我们在一个 monorepo 中维护了几个 NPM 包和一个文档站点，这一切都是通过[巴泽尔](https://bazel.build)完成的。

我将快速介绍 monorepos 和 Bazel，然后通过一些真实的代码示例深入探讨 monorepo 的有趣部分，包括:

*   基底类型 Script 基底
*   在单个 monorepo 中管理多个软件包
*   用 Bazel 构建和发布 NPM 包

我们的项目是开源的，所以你可以查看所有的代码，或者用 Bazel 在:[https://github.com/dataform-co/dataform](https://github.com/dataform-co/dataform)克隆并构建它

## 一次回购你的所有代码

又名 monorepo，*现在这么火*。

我曾经在谷歌工作过，所以我可能有偏见，但是把你所有的代码放在一个仓库里有巨大的价值。您最终会减少花费在重复任务、更新 git 子模块、推出新包、运行 bash 脚本上的时间——这些事情会分散您对手头重要任务的注意力。

有了单一的代码库，在不同的项目之间重用代码和库变得非常容易，但是**你需要一个好的构建系统来使它工作**。

## 数据库

`_{Fast, Correct} - Choose two_` - [https://bazel.build](https://bazel.build)

有没有尝试过克隆和编译一个开源的 repo，只是为了花 30 分钟来解决缺失或损坏的系统依赖、不匹配的版本和大量无法工作的 bash 脚本？是...

Bazel 是一个构建系统。它非常固执己见，很难掌握，但是一旦采用，就会给你留下一个非常快速、密封且可重复的构建过程。

Bazel 仍然相当年轻，但生态系统正在以极快的速度发展。它也建立在坚实的基础上——在谷歌内部使用的 Bazel 被称为 Blaze，有助于支持谷歌的一个庞大的 monorepo(字面意思是所有代码)。

## 问题

我们维护几个相互依赖的 NPM 包。我们的目标是:

*   管理单个存储库中的所有软件包
*   为了使我们的构建快速可靠
*   同时测试对多个包的更改
*   管理所有这些包的版本的简单方法
*   编写尽可能少的 bash 脚本

## 一个基本的巴泽尔 TS 法则

下面是一个如何用 Bazel 构建 TypeScript 库的例子。我们在回购中最简单的包是 [`@dataform/core`](https://github.com/dataform-co/dataform/tree/master/core) ，我们将在大部分帖子中以此为例。

该文件夹看起来像一个普通的 TS 包，除了`BUILD`文件。下面是该文件中实际编译类型脚本的部分:

```
ts_library(
    name = "core",
    srcs = glob(["**/*.ts"]),
    module_name = "@dataform/core",
    deps = [
        "//protos",
        "@npm//@types/moo",
        "@npm//@types/node",
        "@npm//moo",
        "@npm//protobufjs",
    ],
) 
```

`BUILD`文件中的这条规则告诉巴泽尔:

*   名为`core`的图书馆
*   它应该包括该文件夹中的所有`.ts`文件
*   它的(节点)模块名是`@dataform/core`
*   它有一个内部依赖关系`//protos`
*   它有一些 NPM 依赖项，就像一个`package.json`文件

要构建这个 TS 库，您可以运行:

```
bazel build core 
```

*注意:`ts_library`规则和其他与节点相关的规则集不是 bazel 运行时的核心，而是从其他地方导入的。你可以在这里了解更多:https://github.com/bazelbuild/rules_nodejs。*

## 包之间的依赖关系

如果你以前和 NPM 的 monorepo 合作过，你可能用过类似于 [Lerna](https://github.com/lerna/lerna) 的工具。

Lerna 使得在本地链接包变得容易，这样你就可以跨多个 NPM 包测试变化。这也使得管理它们之间的版本更容易。**我们想要那个**。

Bazel 构建和*链接*包，而不需要接近实际的 NPM 包。在我们上面的 [@dataform/core](https://github.com/dataform-co/dataform/tree/master/core) 例子中，`ts_library`规则依赖于`//protos`，而`//protos`是 **[的另一个`ts_library`规则](https://github.com/dataform-co/dataform/blob/master/protos/BUILD)** 。

```
ts_library(
    name = "protos",
    srcs = glob(["index.ts"]),
    module_name = "@dataform/protos",
    deps = [
        ...
    ],
) 
```

`ts_libary`规则发挥了一些神奇的作用，确保构建的包在提供的`module_name`属性下可用，该属性与发布它们的 NPM 包相匹配。

所以在我们的`@dataform/core`包中，我们可以从`module_name`是`@dataform/protos`的`//protos`包中导入如下:

```
import { dataform } from "@dataform/protos"; 
```

当我们发布到 NPM 时，这些导入也会正确解析，因为模块名与包名匹配。

## 管理多个包

Lerna 还帮助你管理多个`package.json`文件，一起更新所有版本并发布它们。我们想在巴泽尔做同样的事情。

为了生成`package.json`文件，我们在 monorepo 中构建了一个小工具[，Bazel 使用 JSON 模板层和字符串替换来生成`package.json`文件。](https://github.com/dataform-co/dataform/tree/master/tools/json-merge)

对于`@dataform/core`包，我们有一个类似于
的`core.package.json`文件

```
{  "name":  "@dataform/core",  "description":  "Dataform core API.",  "main":  "index.js",  "types":  "index.d.ts",  "dependencies":  {  "@dataform/protos":  "$DF_VERSION",  "moo":  "^0.5.0",  "protobufjs":  "^6.8.8"  }  } 
```

任何额外的信息，许可证，主页等-是从基地继承 [`common.package.json`](https://github.com/dataform-co/dataform/blob/master/common.package.json) 所以我们不必保持几个文件同步。

特殊字符串`$DF_VERSION`被定义为 [`version.bzl`](https://github.com/dataform-co/dataform/blob/master/version.bzl) 中 Bazel 构建系统的一部分的全局常量所替换。

## 建筑 NPM 套餐

为了评估这些 JSON 模板，我们编写了一个 Bazel [宏](https://docs.bazel.build/versions/master/skylark/macros.html)来调用我们上面的工具，我们在`@dataform/core` `BUILD`文件中调用它，如下所示:

```
load("//tools/npm:package.bzl", "dataform_npm_package")

dataform_npm_package(
    name = "package",
    package_layers = [
        "//:common.package.json",
        "core.package.json",
    ],
    deps = [":core"],
) 
```

这个[定制的 bazel 宏](https://github.com/dataform-co/dataform/blob/master/tools/npm/package.bzl)从列出的两个模板中生成一个 final `package.json`，并创建一个 output dist 文件夹，其中包含准备发布的已编译的 TypeScript。

要查看这个和最终生成的`package.json`的输出，可以运行下面的命令:

```
bazel build core:package 
```

Bazel 告诉我们，它已经把这个包放在文件夹`bazel-bin/core/package`中，其中有`.js`和`.d.ts`文件，还有最后的`package.json`(这有点像`dist`文件夹)准备发布！

*注意:我们还没有完全自动化这一步，仍然需要确保`BUILD`文件中的依赖项和包名与`package.json`模板中的相匹配，但是完全自动化这一步肯定是可行的。*

## 发布 NPM 套餐

在这一点上发布很容易，并且`rules_nodejs`库已经内置了这一点。要发布一个包，我们可以运行:

```
bazel run //core:package.publish 
```

我们仍然有一个 bash 脚本来为所有的包做这件事，但是它所做的只是调用 Bazel 命令:

```
#!/bin/bash
set -e

# Test all the things.

bazel test //...

# Publish all the things.

bazel run //api:package.publish
bazel run //core:package.publish
bazel run //cli:package.publish
bazel run //crossdb:package.publish
bazel run //protos:package.publish 
```

## 结论

这绝不是一个完整的解决方案，实际上需要你学习很多关于 Bazel 的知识，以便在你自己的项目中使用它。对于任何尝试的人，希望我们的[回购](https://github.com/dataform-co/dataform)可以作为一个很好的参考！

尽管如此，我希望这证明了使用 Bazel 是管理复杂项目和单个存储库中许多节点/类型脚本包的一个很好的解决方案。有了一些额外的 Bazel 规则，你就可以构建一个运行速度很快的 TypeScript monorepo，它将随着你的项目而扩展。

如果你对这篇文章感兴趣，并对 Bazel TypeScript starter pack repo 感兴趣，请联系我们，看看我们能做些什么！