# 我如何建立一个新的角度项目

> 原文：<https://dev.to/mokkapps/how-i-set-up-a-new-angular-project-4b7d>

在我看来， [Angular](https://angular.io/) 是大型企业应用的最佳选择。使用 [Angular CLI](https://cli.angular.io/) 生成的基本项目设置很好，但我更喜欢用另一种方式来建立一个新项目。在这篇博文中，我想谈谈这些话题:

*   使用 Nx 代替 Angular CLI
*   类型脚本配置
*   国际化
*   UI 组件资源管理器
*   模型的领域驱动设计

## Nx

[Nx](https://nx.dev/angular/getting-started/what-is-nx) 不是 Angular CLI 的替代品，但它使用 Angular CLI 的功能，并通过附加工具对其进行增强。你可以用 Angular CLI 做的任何事情也可以用 Nx 来完成，你可以用`angular.json`配置文件来配置你的项目。

基本上，我喜欢 Nx 是因为这些事实:

*   我可以很容易地将现代工具如[柏树](https://cypress.io)、[玩笑](https://jestjs.io/)和[漂亮](https://prettier.io/)整合到我的棱角分明的项目中
*   我可以使用谷歌、脸书和微软首创的有效的开发实践

我们先来说说[柏树](https://cypress.io)和[杰斯特](https://jestjs.io/)在棱角项目中的用法。

### 为什么要考虑用柏树代替量角器？

[看看这个不错的对比](https://techblog.fexcofts.com/2018/09/24/end-to-end-e2e-angular-testing-protractor-vs-cypress/)，了解更多关于这两种技术之间差异的信息。

赛普拉斯是现代而有趣的，因为它不是基于硒。Selenium 通过网络执行远程命令，而 Cypress 运行在与应用程序相同的运行循环中。此外，它的速度很快，并且有一些很好的特性，例如:

*   时间旅行
*   可调试性
*   实时重新加载
*   自动等待
*   间谍，存根和时钟
*   网络流量控制
*   一致的结果
*   截图和视频

在[官方功能网站](https://www.cypress.io/features)上，您可以找到这些功能的更多详细信息。

在我看来，Cypress 最大的缺点是它没有与 SauceLabs 和 BrowserStack 等工具完全集成，并且不支持 Chrome 以外的其他浏览器。这在将来可能会改变，但在撰写本文时，这些特性还不可用。

在我看来，Cypress 并不是每个 Angular 项目的完美选择，但我完全建议你应该尝试一下，并做出自己的决定。

### 为什么要考虑用 Jest 而不是 jasmine？

根据我的经验，当项目越来越大时，使用 Karma + jasmine 的测试体验会越来越差:

*   缓慢的构建时间(尤其是最初)
*   重新编译不可靠
*   像[karma-jasmine-HTML-reporter](https://www.npmjs.com/package/karma-jasmine-html-reporter)这样的 HTML 报告器容易出错

Jest 是由脸书创建的，它比其他测试运行程序更快，因为它是并行测试。此外，它提供了一个 CLI，并且比其他测试框架需要更少的配置工作。

Jest 与 Karma + jasmine 相比的一些优势:

*   测试运行速度更快，因为它可以在不构建整个应用程序的情况下执行测试
*   使用 CLI 可以通过文件名或正则表达式进行过滤，从而减少了对`fdescribe`的需求
*   几乎不需要任何配置就可以开始
*   稳定测试
*   语法类似于 jasmine
*   提供[快照测试](https://jestjs.io/docs/en/snapshot-testing)
*   更活跃的社区

老实说，我还没有在我的任何 Angular 项目中使用 Jest，但我肯定会在我的下一个项目中尝试一下。我还没有使用它的主要原因是，我用许多 jasmine 测试在现有的代码基础上工作，没有必要/时间/预算将它们移植到 Jest。

### 有效的开发实践

使用 Nx，你可以以一种“monorepo”的方式构建你的应用程序。谷歌、脸书、Twitter 等大型软件公司都使用这种方法来简化多应用程序和库的工作。以下是单一回购方式的一些优势:

*   你提交了一个工作软件，它可能包括多个部分，如前端和后端
*   一个工具链设置
*   依赖性管理更容易
*   代码可以分成可组合的模块
*   一致的开发人员体验

我还喜欢在 Nx 中创建应用程序和库的可能性，这为构建更大的应用程序提供了一种很好的方式:

> 应用程序是可以在浏览器或服务器上运行的任何东西。它类似于二进制。*   A library is a piece of code with a well-defined public API. One library can be imported into another library or application. You cannot run the runtime.

例如，为每个 REST API 端点定义一个包含角度服务和相应模型的单独的库是很有用的。

此外，您可以使用特定于工作空间的示意图和代码格式化程序在项目中实施最佳实践。

参见[官方文档](https://nx.dev/angular/fundamentals/develop-like-google)阅读更多关于如何在 Nx 中使用这些机制的信息。

## 打字稿配置

我更喜欢从[这个 tslint 配置](https://github.com/mgechev/tslint-angular)开始，因为它使用了 [Angular CLI](https://github.com/angular/angular-cli) 的 tslint 配置，并与 [Angular style guide](https://angular.io/guide/styleguide) 对齐。

在我的`tsconfig.json`文件中，我启用了 [`strictNullChecks`](https://basarat.gitbooks.io/typescript/docs/options/strictNullChecks.html) ，这使得代码库在运行时对可能的`null`或`undefined`错误更加健壮。

```
{
  "compilerOptions": {
    "strictNullChecks": true
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

来自[官方文件](https://www.typescriptlang.org/docs/handbook/compiler-options.html):

> 在严格的空值检查模式中，空值和未定义值不在每个类型的域中，只能赋给它们自己和 any(唯一的例外是 undefined 也可以赋给 void)。

## 国际化(i18n)

我从项目一开始就配置国际化，即使产品只针对一个国家。这有两个原因:

*   您已经习惯于将翻译后的文本存储在一个文件中，而不是作为硬编码的字符串存储在整个应用程序中
*   如果应用程序需要被翻译成另一种语言，你要为此做好准备

我总是在我的 Angular 项目中使用 [ngx-translate](https://github.com/ngx-translate/core) ,特别是因为它在应用程序运行时提供了语言之间的切换。如果你需要在你的应用程序中实现一个语言切换器，这会变得很方便。

## UI 组件浏览器

如果你开发自己的组件，用所有可用的组件创建一个自定义视图或者使用像 [StoryBook](https://storybook.js.org/) 这样的现有解决方案会很有帮助。

在一些项目中，我在应用程序中创建了一个单独的页面(只对某些人可见)，显示所有可用组件的列表。该页面用于手动测试会话，并提供了一种快速查看新特性是否对任何现有组件有影响的方法。此外，可以单独测试组件。

## 为你的模型使用领域驱动设计

领域驱动设计背后的一个主要思想是将业务逻辑(领域)从应用程序或实现细节中分离出来。这可以使用 TypeScript 在 Angular 中轻松实现。

我们的领域模型的目标是表示业务逻辑。我们希望避免某些业务逻辑被分割到多个组件和服务中，但是在某个地方是可用的。这样，如果业务需求发生了变化，我们可以很容易地做出反应并更改逻辑。

这种领域模型的一个例子可能是这样的:

```
export class User {
  private firstName: string;
  private lastName: string;
  private age: number;

  get firstName() {
    return this.firstName;
  }

  get lastName() {
    return this.lastName;
  }

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  get age() {
    return this.age;
  }

  constructor(firstName: string, lastName: string, age: number) {
    this.setName(firstName, lastName);
    this.setAge(age);
  }

  setName(firstName: string, lastName: string) {
    if (this.validName(firstName) && this.validName(lastName)) {
      this.firstName = firstName;
      this.lastName = lastName;
    }
  }

  setAge(age: number) {
    if (age >= 18) {
      this.age = age;
    } else {
      throw new Error('User age must be greater than 18');
    }
  }

  private validName(name: string) {
    if (name.length > 0 && /^[a-zA-Z]+$/.test(name)) {
      return true
    } else {
      throw new Error('Invalid name format');
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

例如，如果最小年龄应该从 18 岁改变到 16 岁，那么这个逻辑只需要在这个域模型类中改变。

[本文](https://coryrylan.com/blog/rich-domain-models-with-typescript)提供了进一步的细节和一个在前端应用中处理服务器端业务逻辑的好方法。

## 结论

在这种固执己见的情况下，同意你的团队是很重要的。我会向团队提出这种方法，讨论备选方案、优点和缺点，并尝试找到一个好的折中方案。最后，项目应该是可伸缩的，团队应该能够快速交付特性。

本文向您展示了我建立一个新的 Angular 项目的方法。这是不完整的，也许不是一个完美的方法，但这是我的经验，所以你的建议总是欢迎在评论中。