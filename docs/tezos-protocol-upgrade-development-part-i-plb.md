# Tezos 协议升级开发(上)

> 原文：<https://dev.to/tzemanovic/tezos-protocol-upgrade-development-part-i-plb>

Tezos 的一个关键定义特性是在协议本身中包括协议升级，以创建被称为自修改密码分类账的东西。升级通过区块链提交，但在部署之前，它们必须通过多级投票，这也发生在区块链。在本帖中，我们将看看构建协议升级的过程。

[![Jumping spider portrait](img/6b15afb16c341ebf499c62c48d606347.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KpYVfr3p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://tzemanovic.gitlab.io/posts/tezos-protocol-upgrade-development-part-1/iStock-584236788-m.jpg)

## 开始之前…

假设你有一个改变协议的想法，并且你有能力实现它。太好了！但是，对于一个成功的协议升级来说，当然不仅仅是编写不应该被忽略的代码。考虑其他团队(目前是[游牧实验室](https://nomadic-labs.com/)和[美国，Cryptium 实验室](https://cryptium.ch/about-us))致力于发展协议，并与他们取得联系或在主 [Tezos 库](https://gitlab.com/tezos/tezos)上提出问题。这引发了围绕你想提出的改变的讨论，你会得到一些反馈。毕竟，你的提议必须说服涉众投票支持，所以最好尽早测试你的设计。此外，可以将您的更改与其他提议捆绑在一起，而不是与它们竞争(当时只有一个升级可以通过提议阶段)，并可能更快地部署升级。

## 发展先决条件

显而易见的部分应该是学习 OCaml。您将希望能够自如地阅读和编写 OCaml。在这个过程中，有许多资源可以提供帮助:- [真实世界 OCaml 书籍](https://realworldocaml.org/) - [用 Objective Caml 开发应用](https://caml.inria.fr/pub/docs/oreilly-book/ocaml-ora-book.pdf)-[OCaml 中的函数式编程](https://www.cs.cornell.edu/courses/cs3110/2019sp/textbook/) - [练习 OCaml 跟踪](https://exercism.io/tracks/ocaml) - [OCaml 演讲](https://discuss.ocaml.org/)

Tezos 文档涵盖了许多你想熟悉的主题，尤其是在开发者部分。[数据编码](https://tezos.gitlab.io/mainnet/developer/data_encoding.html)和[误差单子](https://tezos.gitlab.io/mainnet/developer/error_monad.html)库都被广泛使用。对于协议升级本身，有必要阅读[协议 Alpha 部分](https://tezos.gitlab.io/mainnet/developer/entering_alpha.html)以及[协议如何被沙箱化](https://tezos.gitlab.io/mainnet/developer/protocol_environment.html)(在协议中，您只能使用从[lib _ protocol _ environment](https://gitlab.com/tezos/tezos/tree/20ce5a625781c6abbaaefdb9e7c8896aba799a7a/src/lib_protocol_environment)中公开的模块和接口，虽然这看起来可能有限制，但有很好的理由)。在阅读文档的同时查看代码，您可能会获得更好的洞察力。

确保你能从资源中[构建。对于开发，调用`make build-dev-deps`而不是`make build-deps`来安装](https://tezos.gitlab.io/mainnet/introduction/howtoget.html#build-from-sources) [Merlin](https://github.com/ocaml/merlin) 来完成代码等等。

如果您还不熟悉 Tezos 客户端，您可以在[沙盒模式](https://tezos.gitlab.io/mainnet/user/sandbox.html)(一个节点数量可配置的本地专用网络)中探索许多可用的[命令](https://tezos.gitlab.io/mainnet/api/cli-commands.html)和 [RPC 接口](https://tezos.gitlab.io/mainnet/api/rpc.html)，这对于快速试验您对协议所做的更改也非常有用。

## 协议代码概述

对于协议升级，通常可以从`master`开始您的分支。您的提案代码从`src/proto_alpha/lib_protocol`中的协议 Alpha 开始，它将包含最新的活动协议的代码(实际上，在成功投票和注入后，将拥有您的提案的代码)。这是[章鱼](https://tezos.gitlab.io/mainnet/whitedoc/the_big_picture.html#the-big-picture)的绿眼，在前面提到的[协议 Alpha 部分](https://tezos.gitlab.io/mainnet/developer/entering_alpha.html)中有更详细的描述。

简而言之，

*   注意,`TEZOS_PROTOCOL`文件定义了所包含模块的链接顺序，它的分组有助于理解代码的结构
*   `Alpha_context`模块通常通过将具体的`*_repr`(表示)和`*_storage`模块粘合在一起来重新定义抽象模块。例如，模块`Vote`由`Vote_repr`和`Vote_storage`组成。`Vote_repr`包含数据定义及其编码，而`Vote_storage`向键值存储公开访问器
*   所有的存储存取器都是在`Storage`模块中定义的，使用了来自`Storage_sigs`的许多存储签名变体以及来自`Storage_functors`的存储构建器
*   许多协议逻辑是使用`Apply`(协议的主要入口点)、`Baking`和协议`Amendment`模块中的`Alpha_context`抽象构建的
*   模块处理迈克尔逊智能合同，当合同代码运行时，其代码被翻译成中间表示法进行解释
*   最后，`*_services`模块定义了协议 RPC API

Tezos 文档更加详细，我们建议您熟悉它。我们希望这能给你一个很好的代码库和如何导航的介绍性概述。如果你有问题或遇到困难，不要犹豫，立即寻求帮助！

* * *

在下一部分中，我们将研究如何处理引入结构变化的协议升级，这需要进行数据迁移，以将以前协议的变化过渡到新协议。