# 如何从命令行运行 npm 软件包

> 原文：<https://dev.to/ccleary00/how-to-run-a-npm-package-from-the-command-line-1f73>

***原载于 [coreycleary.me](https://www.coreycleary.me/how-to-run-a-npm-package-from-the-command-line/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我也定期发送备忘单和其他赠品。*

你有没有遇到过这样的指导教程？

> 运行“npm install knex ”,然后运行“knex migrate:make migration_name”

你可以从命令行运行刚刚安装的 npm 包(在这里是 knex ),这很好，但是通常忽略的是你如何真正地去做这件事。

有一天要做的是将 node_modules 二进制文件夹添加到您的路径中，使用类似 PATH=$(npm bin)的东西。但是有时在路径中添加更多的东西会很烦人，而且不总是能很好地处理相对/绝对路径。

根据您使用的 npm 或 nvm 的版本，如果您已经对 PATH 进行了任何更改，如果您全局安装该软件包，那么您可以从命令行运行它。但是这会污染你的全局模块。如果不*需要*一个全局安装的包，为什么要这么做？

另一种方法是通过指定模块的完整路径从命令行运行包。假设这是一个我们试图运行的本地安装包——从命令行来看应该是:

`$ projects/my-db-project/node_modules/knex/cli.js migrate:make migration_name`

但是必须为每个想要从命令行运行的节点模块键入这些内容吗？还要记住他们所有的路？这比添加$PATH 变量更烦人...

### npx 来拯救

如果您使用的是版本> = 5.2.0 的 npm，它会附带一个名为`npx`的强大工具。npx 允许您从本地 node_modules/.bin 运行命令，而且它真的很容易使用——我们上面的`knex`命令很简单:

`npx knex migrate:make migration_name`

### 测试软件包

npx 还允许我们安装“临时的”(不是全局安装的)包，如果它们还不存在的话。

例如，如果您没有安装`create-react-app`,但是想要测试它，您可以执行`npx create-react-app my-app`(传递选项，就像您已经安装了它一样), npx 会安装这个包，然后为您运行这个命令。

所以下次你需要从命令行运行一个 npm 包并且想要一个非常简单的解决方案时，请使用 npx！

如果你觉得这篇文章有帮助，[这里是链接](https://www.coreycleary.me/about/)订阅我的时事通讯！