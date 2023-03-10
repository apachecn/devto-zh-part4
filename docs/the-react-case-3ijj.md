# 哦，我的反应

> 原文：<https://dev.to/alfredosalzillo/the-react-case-3ijj>

嗨前端开发者和无，

我很高兴向大家介绍一位意大利开发人员的令人毛骨悚然的冒险经历，这是一部基于真实事件的开发系列片。

# 第 1 集:哎呀我的反应

## 前奏

一年前，我换了工作，从一家令人寒心的 IT 咨询公司(我将在另一集谈到这一点)到一家更好的公司。在新公司的第一个项目是一个 React/Spring 微服务和微前端应用，我负责前端部分。

我的团队和我的公司并不是这个项目的唯一参与者，我的前公司(幸运的我)和一家非常大的全球 IT 咨询公司(从现在开始简称为**莉莉斯公司**)也参与了这个项目。

由于微前端和微服务架构，我的团队不必直接与莉莉斯的员工或我的前公司员工一起工作，但在某些情况下，他们会向我的团队寻求帮助。

其中一次，在前端反应部分寻求帮助时，莉莉斯的员工给了我们他们那部分项目的代码库。

我必须做一个介绍:莉莉斯的员工中没有人使用过 React，或者使用过 ES6、Typescript、Webpack(等等)，他们都是遗留开发者。

## 没有圣人来祈祷

在检查他们的代码库之前，我意识到我会发现一些狗屎代码。但现实与我的想象相去甚远。

任何一生中至少使用过一次 React 的人都知道，这也被写入了 [React 文档](https://en.reactjs.org/docs/composition-vs-inheritance.html):

> # 那么遗传呢？
> 
> 在脸书，我们在数以千计的组件中使用 React，我们还没有发现任何我们会建议创建组件继承层次的用例。
> 
> Props 和 composition 为您提供了所需的所有灵活性，以一种显式且安全的方式定制组件的外观和行为。记住，组件可以接受任意的属性，包括原始值、反应元素或函数。
> 
> 如果您想在组件之间重用非 UI 功能，我们建议将其提取到一个单独的 JavaScript 模块中。组件可以导入它并使用该函数、对象或类，而不扩展它。

他们做了什么？

```
class BasePage extends Component { 
  // Hi every one,
  // 
  // i'm a fucking class with only one useless method,
  // but they like so much class extentions
}
class CrudBasePage extends BasePage { ... }
class CrudDialogBasePage extends CrudBasePage { ... } 
```

Enter fullscreen mode Exit fullscreen mode

再一次...

```
class SearchBasePage extends BasePage { ... }
class SearchDialogBasePage extends SearchBasePage { ... } 
```

Enter fullscreen mode Exit fullscreen mode

我可以继续忍受几百次这种事。

## 拜托，给我毒品或者一个解释

在看到他们的代码库后，在与莉莉斯的开发人员的通话中，我们问他们为什么在连 React 文档都不推荐使用继承的情况下使用继承，以及为什么他们没有使用组合、上下文、redux 或钩子(我们已经在使用钩子)来共享功能。

> 我们想用继承。

他们回答道。

我和我的团队帮助他们解决问题，我们去喝酒来忘记我们在他们的代码库中看到的所有令人毛骨悚然的代码。

## 最坏的情况永远没有极限

另一些令人心寒的事实:

*   如果可以用`var`为什么要用`let`或者`const`？他们总是用`var`，从来没用过`const`，只有一次我们发现了一个`let`。
*   代码胜于语言:

```
 var YesNo = ['Yes', 'No'];
    // the original version
    var SiNo = ['Si', 'No']; 
```

Enter fullscreen mode Exit fullscreen mode

*   这比`filter`好多了

```
 let id = -1;
    let selected = this.state.dialog.selected;
    for (let i = 0; i < selected.length; i++) {
      if (BasicUtils.isEqual(selected[i], row)) {
        id = i;
        break;
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

感谢阅读，下集见。