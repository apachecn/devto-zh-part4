# 文档很糟糕，你可能做得太多了

> 原文：<https://dev.to/wagslane/documentation-sucks-and-you-probably-do-it-too-much-4e51>

我经常听说科技行业需要更多更好的文档。从那以后，我开始相信大多数文档都被高估了。

## 文档烂的时候

不正确的文档比没有文档更糟糕，多余的文档毫无价值。让我们去除干扰。

[![](img/8d4e82f60649ac01c5be032f1b333c2d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5B3qQoYR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/09/images.jpeg)

**不正确的文件**

当给开发人员一段代码时，他们通常会选择阻力最小的路径。当提供一个带有注释的函数时，大多数开发人员会阅读注释而不是阅读代码本身。

```
// changeDelimiter changes a comma delimited piece of textual
// data to a colon delimited one
func changeDelimiter(sentence string) string {
    words := strings.Split(sentence, ",")
    return strings.Join(words, " ")
} 
```

Enter fullscreen mode Exit fullscreen mode

当另一个开发人员决定使用这个函数时，他们希望逗号被冒号代替。然而，正如这段代码清楚显示的那样，逗号将被替换为空格。由于不正确的注释，代码变得更加难以理解。读者不知道是代码有 bug，还是评论过时了。

上面的例子很小很简单，但是当函数更大更复杂时，问题就更严重了。

**冗余文档**

[![](img/27bd3623a526b43bb73a697735098de2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_X_lgR99--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/09/new-hire-clones-top.jpg)

完全省略上面例子中的注释会更好。开发人员将不得不求助于主要的信息来源(代码)，并正确地解释其含义。

评论和文档在某种程度上要遵循干巴巴的原则(不要重复自己)。如果代码清楚地说明了发生了什么，为什么还要添加注释呢？当功能发生变化时，有两件事情需要更新！如果代码没有清楚地说明发生了什么，那么开发人员的第一反应应该是让代码更易读，而不是添加注释。

冗余也完全是浪费时间。开发人员最擅长编写代码、构建系统和创建测试。只有在绝对必要的时候才应该写文档。

## 文档什么时候好？

**用户文档**

上面的例子只适用于内部文档，即注释和阅读材料，它们的目标读者是同一代码库的未来开发人员。

API、CLI 和库文档很重要。针对代码用户而不是代码维护者的文档是必要的，也是鼓励的。

**注释应该解释“为什么”而不是“如何”**

并非所有面向维护人员的文档都是不好的。

解释为什么事情会发生的注释和文档是非常重要的。解释代码如何工作的注释很可能是多余和懒惰的。例如，

```
func exampleEndpoint(input string){
    input = strings.ReplaceAll(input, "^", "-")
    input = strings.ReplaceAll(input, "?", "_")
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，通过阅读代码可以清楚地看到，所有的省略号和问号都被破折号和下划线代替了…但是为什么呢？？

```
func exampleEndpoint(input string){
    // clean input for use in a valid regex format
    input = strings.ReplaceAll(input, "^", "-")
    input = strings.ReplaceAll(input, "?", "_")
    ...
} 
```

Enter fullscreen mode Exit fullscreen mode

解释正则表达式中删除了括号和问号以便以后使用的注释是一个很好的文档的例子，它不容易用代码表达。(这只是为了举例，我意识到这是一种可怕的清除正则表达式数据的方式)

这些只是我在决定何时写文档时应用的一些简单的经验法则。它们绝不是教条式的规则，但是我经常看到管理人员和开发人员在参与一个项目时要求额外的文档，这些文档可能不应该被创建。

由莱恩·瓦格纳 [@wagslane](https://twitter.com/wagslane)

下载 q vault:[https://q vault . io](https://qvault.io/)

星我们的 Github:[https://github.com/q-vault/qvault](https://github.com/q-vault/qvault)

后[文档很烂，你可能做得太多了](https://qvault.io/2019/09/03/documentation-sucks-and-you-probably-do-it-too-much/)首先出现在 [Qvault](https://qvault.io) 上。