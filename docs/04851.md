# 重构——哎呀，我一直做错了。

> 原文：<https://dev.to/justindfuller/refactoring-oops-i-ve-been-doing-it-wrong-33ge>

*这个帖子最初出现在[JustinDFuller.com](https://justindfuller.com/posts/2019-01-24_refactoring-oops-ive-been-doing-it-backwards?utm_source=Dev.To&utm_medium=referral&utm_campaign=learning_javascript)。*

欢迎参加我的干预。我是一个重构上瘾者，我不怕承认这一点，但只有一个问题:我一直在逆向进行。你看，我所做的可以更准确地描述为不成熟的代码抽象。

我们都知道重构。如果你读过哪怕一本编程书籍，或者花了很多时间阅读代码博客，你就会对它有所耳闻。这是一个保持代码可理解、可维护和可扩展的重要概念。

至少每个人都这么告诉我。

那么，为什么重构没有实现我的期望呢？

当我写我最近的库时，我花了一些时间来反思我的代码的演变。我意识到，在我有一个完全正常工作的产品之前，在我的单元测试中有一个理想的输出之前，我已经将我的代码重构到了我甚至不确定我是否需要的接口中。我移动了代码，使其可扩展，可重用，但是为什么呢？那段代码会给我所需要的最终输出吗？我当时还不知道。

最后一切都解决了，但是我的代码比它需要的更复杂吗？我想是的。

## 原则高于目的

你听说过[固体](https://en.wikipedia.org/wiki/SOLID)原理吗？我试着紧紧跟随他们。我写的每个函数都旨在让[有一个单一的职责](https://en.wikipedia.org/wiki/Single_responsibility_principle)。我的类和工厂的目标是[对扩展开放，同时阻止修改](https://en.wikipedia.org/wiki/Open/closed_principle)。我也尽量不直接依赖太多东西，所以相反，[我接受依赖作为函数和类中的参数](https://en.wikipedia.org/wiki/Dependency_inversion_principle)。

这像是好代码的秘诀吗？我想是的。当我的代码专注于坚固，或者说[纯粹](https://en.wikipedia.org/wiki/Pure_function)，而不是完成它生来要做的事情时，问题就出现了。当我把原则置于目的之上时，问题就出现了。

例如，我一直专注于确保我的[单元测试没有昂贵的 IO](https://medium.freecodecamp.org/how-writing-tests-can-make-you-a-faster-and-more-productive-developer-f3ad978e3872) (输入和输出)。我偶尔不得不回去修复由于我不正确地模仿依赖项而导致的错误代码。

那么，有什么解决办法呢？

还记得我之前提到的那个倒影吗？这让我想起了那句口头禅，“让它工作，让它正确，让它快速。“我意识到我一直在乱来。我一直在做正确的，快速的，然后让它工作！

## 让它工作

随着我开始写更多的东西，我越来越清楚，好的作品不会凭空出现。首先我必须把我所有的想法都写在纸上。我得看看我的想法会带我去哪里。然后，我必须把它们塑造成某种半连贯的、非散漫的版本。

同样的事情也会发生在代码上。

把这些都放到函数里。首先，不要太担心命名、单一责任或可扩展性——一旦您的功能正常工作，您将会解决这些问题。要明确的是，你不会像这样写你的整个应用程序，只是一小部分。

一旦你得到了你想要的输出(你有单元测试来证明代码是正确的，对吗？)开始重构，但不要走得太远太快！目前，坚持使用重构策略，这些策略属于正确命名、函数只做一件事以及避免变异的范畴；在确定重复模式之前，不要立即开始创建可扩展或可重用的类和工厂。

此时，使用任何具有逻辑优势的重构都是有意义的。这意味着以理解代码或代码可靠为目的的重构。

考虑用只在特定场景下有用的模式推迟重构。

你会想保存这些直到你有一个理由。

## 有原因

有扎实的代码不是理由。拥有功能性或纯代码不是理由。

为什么我们要让代码可扩展？以便相似但不相同的功能可以从基本逻辑中分支出来。

为什么我们要颠倒依赖关系？以便业务逻辑可以被多个实现使用。

希望你能明白我的意思。有些重构是独立的。例如，重构变量的名称以使其更加准确总是有意义的。它的优点是固有的。将函数重构为纯函数通常是有意义的，因为副作用会导致不可预见的问题。这是一个合理的理由。

“使用依赖倒置是最佳实践”不是理由。“好代码是可扩展的”不是理由。如果我只有几个永远不变的依赖项呢？我还需要依赖反转吗？也许还没有。如果不需要扩展我的代码，而且我也没有这样做的计划，该怎么办？我的代码应该增加它的复杂性吗？不要！

看看下面的例子。

```
// not extensible

function getUser() {
  return {
    name: 'Justin',
    email: 'justinfuller@email.com',
    entitlements: ['global', 'feature_specific']
  }
}

// used later

getUser().entitlements.includes['feature_specific']

// Extensible

class User {
  constructor() {
    // initialize here
  }

  hasEntitlement(expectedEntitlement) {
    return this.entitlements.includes(expectedEntitlement)
  }
}

// used later

new User().hasEntitlement('feature_specific') 
```

你喜欢哪一个？你自然倾向于先写哪个？当然，User 类更具可扩展性，因为它可以被另一个类覆盖。例如，如果你有一个`SuperUser`，那么你可以像这样实现`hasEntitlement`:

```
hasEntitlement() {
  return true
} 
```

不要让课程把你弄糊涂了。没有它也能达到同样的结果。

```
function superUser(user) {
  return {
    ...user,
    hasEntitlement() {
      return true
    }
  }
} 
```

不管怎样，`hasEntitlement`的封装允许用户针对不同的用例，利用多态性来扩展——而不是改变——代码。

尽管如此，用户类可能完全是多余的，现在您的代码比以往任何时候都要复杂。

我的建议是坚持尽可能简单的模式，直到你有理由做更复杂的事情。在上面的解决方案中，您可以选择坚持使用同一个简单的用户数据对象，直到您拥有多种用户类型。

## 复杂的顺序

现在，如果你允许的话，我要编造一些东西！我称之为复杂程度，当我做重构决定时，它会帮助我。看起来是这样的:

*   [常量变量](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)

*   [可变变量](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)

*   集合(对象，数组)

*   功能

*   使用[关闭](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures)的功能

*   工厂(返回集合的函数)

*   班级

每当我决定如何组织功能时，我都会参考这个列表。我选择最可能满足我的实现的选择。我不会再次选择，直到它完全不起作用。有时候性能会影响这个选择，但不经常。

通常，我发现我会在一个对象中放置一些东西，而不是一个更简单的常量变量。或者当我只需要一个函数时，我创建了一个工厂。

这个清单让我脚踏实地。它防止我过早地重构。

## 平衡

我最近听说，如果你在一次会议上说，“这一切都是为了找到平衡，”每个人都会对你毫无意义的评论点头，就像你说了一些深刻的东西一样。我很快就要试一试了。

不过，在这里，我认为平衡很重要。作为程序员，我们必须在代码质量、性能、可维护性和完成工作的传统需求之间取得平衡。

我们必须保持警惕，确保两种需求都处于正确的位置。如果我们的代码不能正常工作，它就无法维护。另一方面，很难让糟糕的代码正常工作。

尽管如此，代码可能会被重构，但是如果重构的代码已经超过了有用的程度呢？这些都是需要记住的重要问题。

下次你写代码的时候，请重构！但也可能…不要？

* * *

这是一个转贴，帖子最初出现在[www.justindfuller.com](https://justindfuller.com/posts/2019-01-24_refactoring-oops-ive-been-doing-it-backwards)上。

* * *

嗨，我是贾斯汀·富勒。很高兴你看了我的帖子！我需要让你知道，我在这里写的一切都是我自己的观点，并不代表我的雇主。所有代码样本都是我自己的，与我雇主的代码完全无关。

我也很想收到你的来信，请随时通过 [Github](https://github.com/justindfuller) 或 [Twitter](https://twitter.com/justin_d_fuller) 与我联系。再次感谢阅读！