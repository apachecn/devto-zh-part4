# 更简单的角度单位测试

> 原文：<https://dev.to/wescopeland/easier-angular-unit-testing-4aic>

【2020 年 5 月通知:虽然本文中介绍的工具(大部分)很棒，但我不再同意所写的测试策略。是的，测试实现细节可以增加您的覆盖率并打开一些绿灯，但是它通常在验证代码的正确性方面不是特别有价值。更多详情请阅读[这里](https://testing-library.com/docs/guiding-principles)，一定要看看我现在推荐的:[角度测试库](https://testing-library.com/docs/angular-testing-library/intro)。

* * *

在这篇文章中，我们将学习如何用另一个测试工具包来提升我们的角度单元测试体验。我将带您了解如何将您的项目从 Karma 切换到 Jest，如何在 Angular 项目中设置 Wallaby，以及如何结合旁观者和浅层渲染来测试您的组件和服务。

警告:我的单元测试方法是固执己见的。就像软件开发中任何固执己见的事情一样，有很多人[同意](https://hackernoon.com/why-i-always-use-shallow-rendering-a3a50da60942)而[不同意](https://kentcdodds.com/blog/why-i-never-use-shallow-rendering)。我的角色是展示在我的团队和项目中运行良好的方法，并给你一个如何开始使用它的路线图。

* * *

### [T1】简介](#intro)

> 难测代码难爱代码。

使用 Angular CLI 提供的工具包测试您的应用程序是很困难的。通过一些跑腿的工作，我们可以对我们的测试开发体验做一些戏剧性的改进，这可能包括:

*   🔥测试可能会运行得更快！
*   👀我们可以在编辑器中自动查看测试何时通过或失败，甚至不需要在命令行中运行测试！
*   ✨:我们可以很容易地只运行受即将到来的 git 提交影响的测试！
*   🥳:我们可以用更少的样板文件编写测试，帮助我们作为一个团队或工程组织更快地前进！

通过在我们的 Angular CLI 项目中利用替代工具，这一切都是可能的。让我们开始吧。

* * *

### 👉改进 1:使用 Jest 作为测试人员。

[![Jest logo](img/17961da894b2b871cabdffcf81ce8ac5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bFH2pskP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/nF8eMPG.png)

脸书基于节点的快速测试运行程序 Jest 可以在几个关键方面极大地改善 Angular 应用程序的测试开发体验:

*   测试是并行执行的，而不是同步执行的，这使得大型项目的测试执行速度更快。
*   测试在模拟的 DOM 环境中直接在命令行中运行。您不再需要启动浏览器实例来运行您的测试。
*   受当前 git 差异影响的测试可以很容易且智能地被隔离。

通过切换到 Jest，我们的项目中将不再需要 Jasmine。不过这没什么好担心的！Jest 的 API 和 Jasmine 的几乎一模一样。

虽然有[示意图可以立即将项目切换到 Jest](https://github.com/briebug/jest-schematic) ，但在撰写本文时，他们有[已知的问题](https://github.com/briebug/jest-schematic/issues/12)与 Angular 8，还没有正确地重新配置您的`ng test`命令。由于这些不幸，我们将手动转换我们的 Angular 8 项目。

#### [t1 是设置指南](#jest-setup-guide)

**1。删除所有 Karma 依赖项和配置文件。**

在工作区的根文件夹中执行以下命令:

`rm karma.conf.js src/test.ts`

`npm rm -D karma karma-chrome-launcher karma-coverage-istanbul-reporter karma-jasmine karma-jasmine-html-reporter`

**2。在您的 tsconfigs 中删除死的 *test.ts* 文件引用。**

在 *tsconfig.app.json* 和 *tsconfig.spec.json* 中，删除对*“src/test . ts”*的引用。

**3。添加 Jest 依赖项。**

在工作区的根文件夹中执行以下命令:

`npm i -D jest jest-preset-angular @angular-builders/jest @types/jest`

**4。添加笑话类型到你的 *tsconfig.json* 。**

在 app root 中，打开 *tsconfig.json* ，添加一个*类型*数组到你的*编译选项*对象:

```
"compilerOptions": {
  ...
  "types": ["jest"]
} 
```

Enter fullscreen mode Exit fullscreen mode

**5。在你的根文件夹中添加一个 *jest.config.js* (在你的 tsconfigs 旁边)。**