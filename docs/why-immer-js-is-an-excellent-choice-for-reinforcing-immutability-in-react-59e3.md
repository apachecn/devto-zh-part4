# 为什么 Immer.js 是 React 中增强不变性的绝佳选择

> 原文：<https://dev.to/praxentsoftware/why-immer-js-is-an-excellent-choice-for-reinforcing-immutability-in-react-59e3>

除非你已经是 immutable.js 的专家，否则考虑使用 immer.js。它有一个更简单的 API，而且与不容易与 JavaScript 互操作的 immutable.js 不同，immer.js 允许开发人员相当容易地使用原生 JavaScript 结构。

* * *

## 什么是 Immer.js 和不变性？

在 React 中，Immer.js 是一个越来越受欢迎的用于增强不可变数据结构的工具。不变性是指一种不可改变、不可更改的状态。即使使用 Redux，也要确保不变性得到正确实施，这一点很重要。

这是因为有可能改变 Redux 存储，从而导致错误和问题。像 immer.js 和 immutable.js 这样的不变性库的存在是为了在已经稳定的 Redux 容器后面提供一层额外的力量。这无疑确保了状态的准确更新，防止了其他可能的突变。

## 使用 Immer.js 的三大好处

在总部位于奥斯汀的 5000 强公司 Praxent 最近的一个 web 应用程序开发项目中，软件开发人员 Peter Elbaum 评估了风险，并明智地决定使用 immer.js 而不是默认的 immutable.js。

当被问及是什么吸引他选择 immer.js 而不是 immutable.js 时，他给出了两个主要原因:

**1。更简单的 API**
Immer 的 API 很好用。Peter 只需查看文档几次，就能掌握核心概念。这与掌握 immutable.js 的陡峭的学习曲线形成了鲜明的对比。

**2。与原生 JavaScript**
互操作 Immutable.js 涉及使用非原生 JavaScript 的数据类型。为了有效地使用它，开发人员必须花费数小时学习一种新的数据类型，否则这种数据类型是不相关的。对于像 Immer 这样的替代方案，将时间和脑力投入到学习 immutable.js 上是没有意义的。

另一方面，Immer.js 相当容易学习。它解决了与 immutable.js 相同的问题，但是以一种更加简化和有效的方式。

**3。非常适合编写 Redux Reducers &测试**
Immer.js 语法提供了输出和输入的清晰表达，这意味着开发人员可以避免大多数对象的创建和析构。

## 缺点:Immer.js 不支持 JavaScript 类

虽然 immer.js 明显优于 immutable.js，但是它有一个缺点:它不支持 JavaScript 类。

为了让 immer.js 与 JavaScript 类一起工作，开发人员需要通过添加一个特殊的标志来启用类副本。结果，构造函数丢失，导致类的行为像普通对象一样。

不幸的是，这导致了彼得项目中难以诊断的状态突变和错误。这部分是由于 Praxent 的 React 样板文件的兼容性问题，它使用 OOP 来建模数据库资源。

Immer.js 文档很清楚缺乏对 JavaScript 类的支持，声称类支持会与不可变状态树背后的架构相冲突。提前做好准备和计划。

## Immer.js 的积极成果

经过测试，immer.js 被证明是增强 React 中不变性的可靠选择。它消除了对复杂字符串路径的需求，为 immutable.js 提供了一个简单、流畅的替代方案。

* * *

*> >企业应用上的主控版本控制。[访问我们的白皮书](https://info.praxent.com/version-control-e-book)，深入了解 Git 之外的版本控制，并在 Praxent 了解更多关于[定制软件开发](https://praxent.com/build/custom-software-development)的信息。*