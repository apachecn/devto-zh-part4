# 零安全(真的)给企业带来任何价值吗？

> 原文：<https://dev.to/schwusch/does-null-safety-really-bring-any-value-to-businesses-39m7>

[![Beating a dead horse](img/d41c67bf65002c34b3706d27e6d21f05.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--_w8Zfgqu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/WTDnnwE.gif) *这里又是一篇关于 null 的文章*

#### <TL；博士>

在我看来，在类型系统中包含可空性在操作安全性和成本效益方面是一个明显的技术进步。要么是市场滞后，要么是我高估了它的价值。也许我属于那种大胆而天真的少数人，他们过于相信安全类型的系统。

#### </TL；博士>

空安全在主流平台已经变得相当时尚。像 Swift 和 Kotlin 这样的语言被苹果和谷歌认可用于他们的平台。Rust 是系统编程和 WebAssembly 中所有的炒作。C#在其第 8 版中接受了不可空的类型，TypeScript 作为 JavaScript 的替代品正在受到关注。Dart 团队正在努力进行一次类似的旅程，他们称之为[非空默认(NNBD)](https://github.com/dart-lang/language/issues/110) 。

与此同时，我遇到过一些开发团队，他们并不谈论这个话题，就好像许多语言创作者正在解决的这个*空问题*并不是一个问题。许多开发人员在他们的 Java/C++/JavaScript 代码库中四处奔波，按照预期为他们的公司增加价值，而不是采用这种*零安全的时尚*。在衡量最常用/最流行的语言时，如 [TIOBE index](https://www.tiobe.com/tiobe-index/) 和 [Github Octoverse](https://octoverse.github.com/projects#languages) ，空安全语言很少出现在前 10 名中，C#(不衡量 C#8 及更高版本的使用)和 TypeScript(在开源项目中衡量)除外。

> "我们设法在没有无效安全性的情况下很好地构建了软件，非常感谢你."-稻草人先生，为了这场争论

有一百万个理由不去解决这个理论问题，我很同情其中的很多。引入新的技术决策总是有风险的，必须有相等或更大的收益来平衡风险回报的规模。将这一范式飞跃与类似规模的范式转变进行比较可能是一个好主意。面向对象的范例带来了一个[命名类型系统](https://en.wikipedia.org/wiki/Nominal_type_system)，它已经被广泛采用，并在静态类型化时带来了许多好处:

```
// Java
class Cat {
    void meow() {}
}

class Dog {
    void bark() {}
}

Dog doggie = new Dog();

doggie.meow(); // Compiler error 
```

这个例子可能是显而易见的，直观地解释了为什么它不会，也不应该工作。很难量化名义类型系统给企业带来了多少价值，但从统计数据来看，其受欢迎程度是显而易见的。面向对象语言以压倒性优势赢得了市场。

很难推理，也很难解释为什么这应该能够编译:

```
// Java
Dog doggie = null;

doggie.bark(); // Compiles but explodes at runtime 
```

似乎没有什么好的理由允许这样做，但这就是大多数流行语言的工作方式。对于一个新手来说，一个用来保护程序员不犯常见错误的类型系统允许这种“影子”类型系统似乎有点混乱。一个额外的维度，作为一个程序员，你必须像一个编译器一样找到这些编程地雷。地雷每天困扰着生产中的应用程序，因为人类在发现类型系统错误方面不如编译器。

“但是`null`是一个值而不是类型，应该在运行时计算”，有人可能会补充说。从某种意义上说确实如此，但是大多数类型系统不允许将一个`Cat`的实例赋给一个`Dog`类型的变量。我认为实例化的`Cat`是一个值，至少在赋给一个`Cat`类型的变量时是这样。

### 东尼·霍尔说这是个坏主意，而且是他的发明

大多数人都听过或读过东尼·霍尔宣称空引用是他“十亿美元的错误”，这值得重复一遍。这么多语言处理这个问题是有原因的。一个微弱但丰富多彩的类比是火器，在大多数情况下，火器具有所谓的 [*安全锁扣*](https://en.wikipedia.org/wiki/Safety_(firearms)) ，以在一定程度上降低意外发射的风险。当用户需要在威胁情况下尽可能快地发射武器时，安全锁扣是否是一个好主意是值得怀疑的。尽管不是全部，但大多数军用和警用武器都有某种安全锁扣。如果不考虑人的因素，这看起来似乎是徒劳的，但当考虑到用户可能有压力，甚至睡眠不足时，这无疑是一个好主意。

当情况危急时，我们需要通过工具的安全性来补偿人为因素。在决定大多数人使用的工具之前，必须进行经典的[风险与后果分析](https://en.wikipedia.org/wiki/Risk_assessment)。
这让我想知道选择没有空安全捕捉的编程语言背后的基本原理。程序员忘记手动空值检查的概率是否低得令人无法接受，或者最终的后果是否可以容忍？

### 不是所有的事故都可以预防的

尽管使用类型安全语言实现时，武器安全和应用程序崩溃或中断，流弹仍然会发射。为什么语言创造者仍然费心创建这些复杂的编译器试探法，为我们提供这些隐含的安全感？有人可能会说，它让程序员在专注于手头的主要任务时，内心平静。实用主义者说，为什么要做可以交给编译器的认知工作。愤世嫉俗者会争辩说，编译器永远不可能捕捉到所有的错误，所以最好自己去正确地检查它。

对企业来说，投资回报(ROI)是一个核心概念，技术决策也不例外。如果保险栓的价格是枪的四倍，那么就有理由训练士兵更加小心。如果选择一种类型安全的语言，开发的代价会与安全性的收益不成比例，那就难怪 Java 和 C++仍然流行了。然而，衡量替代发展的成本并不像衡量不同武器配置的成本那样容易。