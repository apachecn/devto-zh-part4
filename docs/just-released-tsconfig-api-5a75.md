# 刚刚发布:tsconfig-api

> 原文：<https://dev.to/ethanarrowood/just-released-tsconfig-api-5a75>

刚刚发布了一个名为 **tsconfig-api** 的新项目。该 API 接受 TypeScript CLI 选项，并通过微服务 API 使它们可用。数据基于[编译器选项](https://www.typescriptlang.org/docs/handbook/compiler-options.html)手册文件(在这里找到降价版本[)。API 是构建在 Node.js 上的微服务，它使用现在的](https://github.com/microsoft/TypeScript-Handbook/blob/master/pages/Compiler%20Options.md)[进行构建和部署。](https://zeit.co/now)

通过向`https://tsconfig-api.matterhorndev.now.sh/tsconfig?option=<compiler option>`发出 GET 请求来使用该 API

不要忘记将有效的编译器选项传递给`option`查询参数！

查看 GitHub 上该项目的自述文件,获取完整的选项列表。

创建此项目是为了准备将来使用 TypeScript 编译器选项的工具。目前计划的是一个 web 应用程序和一个命令行界面，用于了解和创建 tsconfig 文件/ tsc 编译命令。

我希望你喜欢使用它！