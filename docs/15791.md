# 从 Ember 迁移到 React 的注意事项

> 原文：<https://dev.to/thisdotmedia/considerations-in-migrating-from-ember-to-react-3cfm>

您是否考虑迁移您的 Ember 应用程序以进行反应？

随着 React 在 JavaScript 社区中的爆炸性流行和广泛采用，许多开发人员一直在探索迁移到当今市场上最流行的技术之一的机会。

随着我们的 Ember 朋友开始将 React 作为他们的迁移目标，我们被问到的一些问题是:

Ember 和 React 有什么不同？

**每个框架或库的优缺点是什么？**

如果我决定迁移我的应用程序，我可以避免哪些错误？

我们希望对这些问题有所了解，并使您的迁移工作更加容易。

## 探索余烬与反应——一般差异

在比较 Ember 和 React 时，一定要认识到 Ember 是一个 ***应用框架。*T3】**

与此同时，反应过来的只是一个 ***库*** 用于渲染视图。

因此，我们这里不是在进行苹果与苹果的比较。

做对比的时候，可以看看 Ember 和 React 的区别，按照以下方式。

*   Ember 是一个固执己见的框架，具有完整的特性。它很成熟，与 Rails 有很多相似之处。

*   React 是一个灵活的、“非固执己见”的库，雇佣和学习的门槛很低。

Ember 被描述为有很多“魔法”——很多事情都是按照惯例运作的。这对于许多开发者来说是天赐之物，毫无疑问是 Ember 最强的属性之一。如果你是一名 Ember 开发人员，你可能也习惯了 Ember 为路由、数据模型和依赖注入提供的一流支持。

虽然您会发现 React 库并没有提供这些 Ember 细节，但是 React 社区已经介入并为 Ember 开发人员已经习惯的许多特性开发了解决方案。

在许多情况下，这些解决方案已经成为事实上的选择，并且是 React 开发的同义词。一些例子包括[反应路由器](https://github.com/ReactTraining/react-router)和[冗余](https://redux.js.org/)。但是即使是这些解决方案也有[流行的](https://github.com/reach/router) [替代方案](https://github.com/mobxjs/mobx)，符合图书馆和社区保持非固执己见和灵活的立场。

## 移植您的 Ember 应用程序以作出反应

React 的一个强大卖点是 React 库本身的专注特性。因为它如此专注于正确地做一件事情——与其他技术集成 React 变得非常容易。

React 非常适合您希望向使用其他技术的应用程序添加动态客户端功能的情况。

例如，Facebook.com 不是传统的 100%客户端应用；它在需要用户交互的地方使用 React 组件，并使用其他技术(如服务器渲染)来生成其他部分。

诚然，我们中的大多数人并没有在构建脸书，但这里的关键是 React 的终极灵活性。

当从 Ember 向 React 迁移现有应用程序时，React 的灵活性意味着您可以增量迁移页面和功能。同时，任何新的页面都可以从头开始用 React 编写。

这种在同一个应用程序中混合 Ember 和 React 的方法有巨大的好处，比如不需要放弃新功能的工作来专注于大规模的重写。尽管如此，它也有自己的巨大成本。

最明显的代价是工程师需要在两种 UI 范例中维护代码。但也许更重要的是潜在的文件大小增加。

如果你只是在 Ember 的同一个包中导入 React，那么你的包大小就增加了大约 [32kB gzipped](https://gist.github.com/Restuta/cda69e50a853aa64912d) 。为了减轻这种膨胀，使用[代码分割](https://reactjs.org/docs/code-splitting.html)很重要，这涉及到从一些预定义的分割点创建多个包，然后根据需要延迟加载单个包(或者使用[预取](https://medium.com/webpack/link-rel-prefetch-preload-in-webpack-51a52358f84c))。使用 React 的页面可以使用这种方法，以便仅在实际使用时下载 React。

### 重写你的应用

另一种方法是齐心协力重写整个应用程序。根据你的团队规模和资源分配，这可能意味着暂停主要的新产品开发。

有些人可能会认为我提到这种可能性是疯了，但这实际上是人们最常见的迁移方式…而且有充分的理由。为什么？

如果你有经验丰富的人才，大规模重写你的应用程序可能会极大地鼓舞工程师的士气，并偿还技术债务。这两个因素可能会导致未来产品更快的迭代，特别是如果您现有的应用程序已经使用了很多年，并且有许多不同的维护者。

### 选择你的方法

对于每种情况下应该采用哪种方法，并没有单一的建议。每个应用和团队都是不同的，所以你需要在你的特定环境中评估决定。

例如，如果你已经想给你的应用一个全新的设计(不仅仅是改头换面)，重写更有可能。

尽管如此，还是要不断提醒自己，即使它有缺点，你现有的代码库也能工作(希望如此)。它可能需要数年的强化、错误修复和性能调整。虽然你可能已经从以前的错误中吸取了教训，但你无疑会犯新的错误。

## 学习反应

值得庆幸的是，学习 React 的在线和离线资源并不短缺。除了[官方文档](https://reactjs.org/docs/getting-started.html)(这很棒)，以下是我们的一些精选:

*   Kent C. Dodds 的 egghead.io 上的免费视频系列

*   |作者 Per Harald Borgen

*   [React 培训工作坊](https://reacttraining.com/) |迈克尔·杰克逊

*   [指导、支持和迁移](https://www.thisdot.co/mentors?utm_source=medium&utm_medium=blog&utm_campaign=ember-to-react&utm_content=v1) |

虽然有许多不同之处，但 modern Ember 遵循了一个非常相似的以组件为中心的模型，该模型得到了普及。然而，在 React 中，不是使用带有自定义语言(Glimmer/Handlebars)的单独模板，而是通常使用 JavaScript 编写标记，或者，可选地，使用 JavaScript 的 [JSX 语法扩展，这实际上只是对 React.createElement()](https://reactjs.org/docs/introducing-jsx.html) 的[常规函数调用的糖。](https://reactjs.org/docs/react-without-jsx.html)

看看这个简单的对比:

## 余烬

*app/templates/components/example . HBS*

```
<button {action "sayHello"}>Click me</button> 
```

*app/组件/example.js*

```
import Component from '@ember/component';

const Example = Component.extend({
  actions: {
    sayHello() {
      alert('hello world');
    }
  }
});

export default Example; 
```

## 做出反应

*src/Example.js*

```
import React, { Component } from 'react';

export class Example extends Component {
  sayHello = () => {
    alert('hello world');
  };

render() {
    return (
      <button onClick={this.sayHello}>Click me</button>
    );
  }
} 
```

Ember 有计算属性和观察者的概念，而 React 没有。你也可以使用[非常相似的](https://mobx.js.org/refguide/computed-decorator.html#-computed) [MobX](https://github.com/mobxjs/mobx) 库，但是我建议你先学习习惯用法 React。你可能不需要 [MobX 或者 Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367) ！

## 结论

总之，从 Ember 到 React 的转变并不像以前那样是一个重大的转变，因为 Ember 已经接受了组件。

然而，选择 React 意味着你可能还要从[大量的社区库](https://www.robinwieruch.de/essential-react-libraries-framework/)中选择，以提供你期望的功能。

位于 [This Dot](https://www.thisdot.co/labs?utm_source=medium&utm_medium=blog&utm_campaign=ember-to-react&utm_content=v1) 的团队已经经历了许多这样的迁移，可以帮助您的团队成功地进行规划和执行。无论是通过[恒星导师](https://www.thisdot.co/mentors?utm_source=medium&utm_medium=blog&utm_campaign=ember-to-react&utm_content=v1)，还是为你的团队提供[干净的架构和动手工程](https://www.thisdot.co/labs?utm_source=medium&utm_medium=blog&utm_campaign=ember-to-react&utm_content=v1)，我们都希望听到你的意见。

[取得联系](https://www.thisdot.co/contact?utm_source=medium&utm_medium=blog&utm_campaign=ember-to-react&utm_content=v1)。

[![[Hire the team at This Dot to help](https://www.thisdot.co/contact?utm_source=medium&utm_medium=blog&utm_campaign=ember-to-react&utm_content=v1)](img/638e908ea9fd04466938e2269a39278e.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--tU_OPqI1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2964/1%2AjTSw9Uf2Qk9I6OuyaG7tEA.png) * [雇佣团队在此打点帮助](https://www.thisdot.co/contact?utm_source=medium&utm_medium=blog&utm_campaign=ember-to-react&utm_content=v1)*

*感谢[罗伯特](https://medium.com/@rob_21155)豹猫*