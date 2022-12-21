# 何时不使用 package-lock.json

> 原文：<https://dev.to/gajus/stop-using-package-lock-json-or-yarn-lock-3ddi>

我在 GitHub 上维护着超过 [200 个仓库，我收到的最常见的 PRs 之一是有人添加了`package-lock.json`或`yarn.lock`。这些 PRs 在没有合并的情况下被关闭，因为依赖关系锁文件不是为本身依赖于其他包的包而设计的。](https://github.com/gajus)

## 怎么了？

NPM 官方文档鼓励将`package-lock.json`文件提交给源代码版本控制:

> 强烈建议您将生成的包锁提交到源代码控制:这将允许您的团队、您的部署、您的 CI/持续集成中的任何其他人，以及在您的包源代码中运行 npm install 的任何其他人获得与您开发时完全相同的依赖树。此外，这些变化的不同之处是人类可读的，它会通知您 npm 对 node_modules 所做的任何更改，因此您可以注意到是否有任何可传递的依赖项被更新、提升等。

–[https://docs . npmjs . com/files/package-locks # using-locked-packages](https://docs.npmjs.com/files/package-locks#using-locked-packages)

将`package-lock.json`提交给源代码版本控制意味着项目维护人员和 CI 系统将使用依赖关系的特定版本，该版本可能与`package.json`中定义的版本匹配，也可能不匹配。因为`package-lock.json`无法添加到 NPM 注册表中(按设计；参见 [NPM 包膜](https://docs.npmjs.com/cli/shrinkwrap))，依赖于使用`package-lock.json`的项目的项目将自己使用`package.json`来解决项目的依赖性，也就是说，当项目被用作依赖性时，对项目维护者/ CI 系统有用的东西可能就不起作用了。

这种误用的来源是 NPM 文档。相反，应该说明当项目不是其他项目的依赖项时，`package-lock.json`应该只提交给源代码版本控制，即`package-lock.json`应该只提交给顶级项目(最终用户使用的程序，而不是其他程序)的源代码版本控制。

我已经[要求 NPM 更新文档](https://github.com/npm/npm/issues/20603)，但是它被存档了，没有任何行动。

## 回应批评

一些评论认为`package-lock.json`的最大优势是它允许复制开发环境。

我会支持`package-lock.json`的一个变体，如果它仅仅适用于`devDependencies`的话。我可以看到*一些*(虽然很小，但有所权衡)的好处，如果你的依赖关系中有一个损坏的版本，希望你的开发环境不会损坏。我个人更希望我的环境被打破，并意识到我的工具包中的依赖性需要关注(并根据问题的性质提供帮助、订阅问题或替换依赖性)。毕竟，如果出于开发目的需要锁定一个特定的版本，您可以很容易地修补您的依赖树。

然而，没有这样的选项，并且目前使用锁文件将会产生本文中描述的风险——即您使用的依赖项与您的用户将依赖的那些不匹配。负责任的开发要求您的脚本能够与 semver 所满足的最新版本的依赖项一起工作(是的，包括可传递的依赖项)。