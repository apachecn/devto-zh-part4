# 完整的提交前工作流

> 原文：<https://dev.to/brunosabot/a-complete-pre-commit-workflow-49de>

### 这是怎么回事？

我真的喜欢做出完美的提交，我尤其讨厌因为一个指导方针的错误而追加或创建一个新的提交。所以我需要一个工具，当我要犯错误的时候，它会对我吠叫。

互联网上到处都是解释[是什么](https://githooks.com/) [Git](https://git-scm.com/book/uz/v2/Customizing-Git-Git-Hooks) [钩子](https://www.digitalocean.com/community/tutorials/how-to-use-git-hooks-to-automate-development-and-deployment-tasks) [是](https://hackernoon.com/automate-your-workflow-with-git-hooks-fef5d9b2a58c) [的教程，但是我仍然不得不花很多时间来为我的 JavaScript 应用程序构建一个良好的工作流，以便正确使用它们。这是我今天在每个项目中使用的工具箱的概述。](https://medium.com/the-andela-way/git-hooks-beautifully-automate-tasks-stages-bfb29f42fea1)

第一个专注于工作流自动化的库是[Husky](https://github.com/typicode/husky)——因为一只对我吠叫的狗引起了我的注意。Husky 是一个 JavaScript 库，它使 Git 挂钩变得更加容易。它提供了将它们直接集成到我们的 JavaScript 项目中的可能性，使我们不必在存储库初始化时处理启动指南或启动脚本。

用哈士奇真的很简单。我们只需要在`package.json`文件中添加一个新的`husky`键。这个新条目包含一个键/值对象，`hooks`,它表示我们的 Git 挂钩，以及我们想要执行的脚本:

[![Here is a very simple  raw `package.json` endraw  file with the husky hooks. For simplicity sake and because the husky version might change, it didn’t include the  raw `devDependencies` endraw .](img/bf51a8346ba25c182aa1bed988f40165.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Xn6jMmIq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/brunosabot.dev/img/1__JUYFGIOYiHTMvgPzLE72ig.png) 
下面是一个非常简单的`package.json`文件，带有沙哑的挂钩。为了简单起见，因为哈士奇版本可能会改变，它不包括`devDependencies`。

### 阐明我们的需求

当我在 React 应用程序上工作时，我使用几个工具来保持一致的代码并简化团队工作:

*   在 JS、JSX、JSON、CSS 和 MD 文件上，把我的代码格式化成开发者一致的东西。
*   [Eslint](https://eslint.org/) ，与 [Airbnb 配置](https://github.com/airbnb/javascript/tree/master/packages/eslint-config-airbnb)，在我的申请上提出一个健壮的林挺。然而，我用`[eslint-config-prettier](https://github.com/prettier/eslint-config-prettier)`优先考虑漂亮者的规则。
*   [Jest](https://jestjs.io/) ，用于我的应用程序单元测试。
*   为了保持我的 CSS 模块干净。

不幸的是，对于每种类型的 Git 挂钩，Husky 只支持一次执行一个命令。

我还必须检查每一个修改过的文件，以应用适当的工具，这是一项繁琐的工作。

谢天谢地，已经有一个很棒的工具可以帮我做到这一点: [lint-staged](https://github.com/okonet/lint-staged) 。

lint-staged 非常棒，因为它只在修改过的文件上执行所需的脚本，这使得钩子运行得非常快。多亏了 lint-staged，我们现在能够在增量提交阶段运行非常快速的工作流，因此大大减少了所有脚本的通常持续时间。

与 Husky 一样，lint-staged 是我们的`package.json`文件中的一个新的键/值对象，它表示要执行的脚本。

[![Same as before, I have only included the interesting part of my package.json file](img/e3ac3ab25e71f7082456901e87968d2f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_wJXNIUE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://storage.googleapis.com/brunosabot.dev/img/1__FsOI8QuZx28kau15BLGUkQ.png) 
和以前一样，我只包含了我的 package.json 文件中有趣的部分

现在，一切看起来都很简单。我只需要告诉 Husky 使用 lint-staged，并告诉 lint-staged 我需要对我的文件做什么。

### 最终结果

综上所述，这就是我的`package.json`文件的样子: