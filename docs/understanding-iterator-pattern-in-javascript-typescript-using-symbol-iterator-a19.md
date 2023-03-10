# 使用符号理解 JavaScript/Typescript 中的迭代器模式。迭代程序

> 原文：<https://dev.to/carlillo/understanding-iterator-pattern-in-javascript-typescript-using-symbol-iterator-a19>

在我最近写的关于[迭代器模式](https://medium.com/swlh/understanding-design-patterns-iterator-using-dev-to-and-medium-social-networks-25c453b083ac)的[设计模式系列](https://www.carloscaballero.io/understanding-software-design-patterns-serie/)中，我以一种友好的方式阐述了四人组(GoF)的 23 种设计模式。这种模式具有不可思议的潜力，可以将我们的数据结构从算法中分离出来。

这种模式在不同的编程语言中被广泛实现，比如 JAVA 有 **Iterable 接口**。

在一些语言中，有一些高级控制结构允许你迭代对象，而不需要创建**迭代器模式**(因为它们默认提供)。然而，修改这种内部模式以使用该语言的全部潜力，而不需要像 JAVA 那样冗长，可能是有用的。

了解[迭代器模式](https://carloscaballero.io/design-patterns-iterator/)并阅读我写的文章是很重要的，因为解释本文的例子来自上一篇文章。

ES6 引入了一种与 JavaScript 数据结构交互的新方式——迭代。迭代器模式有两个核心概念:

1.  **Iterable** 是一种数据结构，它提供了一种向公众公开其数据的方式。在 JavaScript 中，实现是基于一个方法，它的关键字是 *Symbol.iterator* 。真的，Symbol.iterator 是一个迭代器工厂。

2.  **迭代器**是一个包含指向迭代中下一个元素的指针的结构。

因此，iterable 必须是带有函数迭代器的对象，函数迭代器的键是 Symbol.iterator。

[![](img/e55fb0d74621f591f2d39fa500ad044c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2S6W3nQ6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A9SB92sVK0TKaP6OM2xnowg.png)

此外，迭代器必须是带有名为 next 的函数的对象，该函数返回带有键的对象:

1.  值:迭代中的当前项

2.  done:如果迭代已经完成，则为 true，否则为 false。

[![](img/4d3d5346874ae675fa9ce118b5e1bf67.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pzhYvmmb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1000/1%2AA6Rl8bgXt7YNYIkEiH8ZVA.png)

因此，前面的代码定义了一个 iterable 和一个 iterator，它们的用法如下:

[![](img/d1cedf061c22d119872f4e832fc56fbd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4uCjCxy3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2336/1%2AiUEGIJb-W0_-Bwl8xY-83A.png)

当你使用 for-of 循环时，这种语言是在使用一个 iterable，并创建它的迭代器。它继续调用 next()，直到 done 为真。

JavaScript 中有一个 iterables 列表，它使用了前面提到的概念。

1.  数组和类型遍历数组中的每个元素。

2.  每个字符上的字符串。

3.  覆盖它们的元素。

4.  映射到它的键值对。

JavaScript 中使用 iterables 的其他一些结构如下:

[![](img/b9243e960212272f54dd21e473bbd6e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3wHLALh7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A3SUNH-jvX1OaL_9tMbRf3w.png)

[![](img/ac401e41fe15615cedfbd17aaf151b0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FD0os0WL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2200/1%2Ap2DximEvaiItlsmoMDomOQ.png)

我现在将向您展示如何使用 JavaScript/TypeScript 符号实现这种模式。在我们的例子中，我虚构了一个问题，其中有一个名为 WordsCollection 的类，它定义了一个单词的列表(items)及其 get 和 add 方法集(getItems 和 addItem)。此类由使用控制结构(如 for 或 forEach)的客户端使用。下面的 UML 图展示了我刚刚描述的使用经典迭代器模式的场景。

[![](img/8d587af411f7a3d6df6a19a8caba9d08.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6RD-r4qj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2146/0%2A4UfhA3tic0ZeDYaB.jpg)

现在我将向您展示新的 UML 图，它保持了与定制迭代器的兼容性，并实现了本机迭代器。

[![](img/a7a91acfddfe112b998e81982300c7bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VVIFukPD--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2896/0%2ASpMtRjShyX2irpGt.jpg)

首先，让我们从无需实例化任何类就能本机使用迭代器的客户机开始。它将直接从 for-of 循环开始执行迭代。

[![](img/6d1cc2c1db9a37ddbac8059771a1fc32.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--un63WW5o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2608/1%2Ae7j2i5U0jyy6XP-APOTQ5A.png)

请注意，语言清理器上迭代器的本机使用，因为它与编程语言的控制结构集成在一起。

关联的单词集合代码如下:

[![](img/2f80f7c0e84af2c165717f9795e00f70.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xWzB2Omd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2976/1%2AE1nQW2pru0u_5LvRpTlAnQ.png)

我们必须观察的第一件事是，我们需要实现两个接口:

1.  **聚合器**用于自定义可迭代，定义对象可迭代的必要方法。这里我们必须实现迭代器的构造方法。

2.  **迭代器**是语言原生使用的，是定义函数【Symbol.iterator】:()的那个。

本机迭代器的关键是本机迭代器的工厂函数，它不是直接实现，而是被提取到一个新的类中，以保持代码像以前的解决方案(nativealphabeticarolderiterator)一样干净。

[![](img/2ecdfbddf87f1dbf7b760701297d286c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KtUYhVV2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2840/1%2A62rCLfhJ7Oyj0JuzTqKdug.png)

最后，本地迭代器和定制迭代器都从实现其余方法的抽象迭代器扩展了它们的 next()方法。迭代器的 next()方法之间的主要区别是它们各自返回的值，因为在本机迭代器中，迭代器接口必须满足，它返回 IteratorResult，而自定义迭代器直接返回值。

这些代码甚至可以被重构，与其他设计模式一起使用，比如[模板方法](https://carloscaballero.io/design-patterns-template-method/)或者[策略](https://carloscaballero.io/stategy-pattern-in-javascript-typescript/)。然而，我认为最好不要因为添加了这些模式而使示例变得更加复杂。

[![](img/be610cb79bcd6c953664308c373c2478.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gaSQGq80--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3348/1%2AfpQhe6wjOtW44nrzCEmRAA.png)

客户端从 WordsCollection 类的内部结构中分离出来(*单责任*)，你可以扩展软件实现新的迭代器(*开/闭*)。

我创建了两个 npm 脚本，它们在应用迭代器模式后运行这里显示的代码示例。

npm 运行示例 1-问题
npm 运行示例 1-迭代器-解决方案-1

下面的例子在下面的文章中有详细的描述，因此我建议你[阅读这篇文章](https://carloscaballero.io/design-patterns-iterator/)来理解这一点。然而，只是为了让你知道我们正在开发什么，我会给你一个问题的简要描述。

想象一下，我们必须创建一个软件，允许我们向社交网络中的联系人发送电子邮件，并考虑到我们将区分发送的邮件类型。在我们的人际网络中，我们有两类联系人:朋友和同事。要发送的电子邮件会更正式，这取决于电子邮件要发送到的联系人的类型。

一开始我们有来自两个著名社交网络的联系人:Dev.to 和 Medium。每个社交网络的数据结构的实现是不同的，因为在 Dev.to 中使用数组来维护联系人，而在 Medium 中使用地图。

你可以在下面的 gif 中找到使用我们整个结构的客户端(我已经做了一个小的 CLI 例子)。

[![](img/354f5d0a12e7ee6f9084ba831b00f711.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wtVFeWwu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/2000/0%2ASYr5M9EO9UIBZxvm.gif)

在下面的 UML 图中，您可以看到使用 CustomIterator 为该问题提出的解决方案:

[![](img/edea05d4d4e497a84fa82f322b3bdec5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oDthfFXP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2428/0%2AGXFj7GBzekOSaF1x.jpg)

与包含本地迭代器的解决方案相关的图表如下。无论如何，我们继续保持与自定义迭代器的兼容性。

[![](img/1242ad4fad558bb299f6826a5657bb9b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iuSEgBgV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2336/0%2Aoi9coPqmVqfMJGJo.jpg)

开发解耦软件的主要优势在于，当我们的软件增长时，它不受变化的影响。事实上，我们的应用程序的客户端仍然是同一段代码，因为它是基于接口并使用依赖注入构建的。

[![](img/3256278159ba1c8d9678cd71cace9428.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_RvsJrQ7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3584/1%2AtVMGu3IDbQSwyosVl0UAUg.png)

sendSpamToFriends 和 sendSpamToCoworkers 方法使用迭代器，可以是自定义的，也可以是本机的。

[![](img/863698af1245d0876ace483a2430bb4e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gl8R911j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3244/1%2AhYv380IDwsoBNeFhIYS9Tg.png)

在这个方法中，我们使用自定义和本地迭代器。原生迭代器的创建被委托给迭代器工厂，以区分朋友或同事。

社交网络(dev.to 或 medium)必须通过 Iterable 类型的对象创建本机迭代器来满足 SocialNetwork 接口。与社交网络相关联的代码如下:

[![](img/4c42739f4b16ea686194f70b22f244f1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3qUgrsz4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3448/1%2APb61cugkh3PbIPPuPw9NEw.png)

[![](img/bb6ebe3ae687309b3b6418cde30e990b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xuZtIP99--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3480/1%2AUomKF-Nt40_9sYdQMkgKYg.png)

每个社交网络(dev.to 或 medium)必须通过 Iterable 类型的对象创建本地迭代器来满足 SocialNetwork 接口。

下面是与迭代器相关的代码。两个迭代器(自定义迭代器和本地迭代器)都是从父迭代器扩展而来的，在父迭代器中实现了共同的方法。虽然迭代器接口在我们的例子中有几个方法，但是我们只需要实现 next()方法。

[![](img/33dbb99fc9270778de07c72c36dfea21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fpTsCSPt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3380/1%2ABdQ9bfthOaBDY9vihHWrGA.png)

与 medium 关联的迭代器对应于与 dev.to 相同的接口，如下所示:

[![](img/24473464ad53378b073778d67cf7c2fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--19Zg0997--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3180/1%2AOtzc8d8U0BCDiKAdDWMWrQ.png)

我创建了一个 npm 脚本，在应用了迭代器模式和 CLI 接口后，它运行这里显示的示例。

npm 运行示例 2-迭代器-解决方案 1

迭代器模式可以避免项目中的耦合代码。当集合中有多种算法和数据结构时，迭代器模式非常适合。你的代码将会更干净，因为你应用了两个著名的原则，比如**单一责任**和**打开/关闭**。

有时，我们有兴趣了解我们正在开发的编程语言，并检查**迭代器模式**是否在 JavaScript(贯穿本文)、Python 或 PHP 等语言中固有地实现。

最重要的事情不是实现我向你展示的模式，而是能够识别这个特定模式可以解决的问题，以及你何时可以或不可以实现所述模式。这一点至关重要，因为实现会因您使用的编程语言而异。

*最初发布于[https://www . carloscaballero . io](https://www.carloscaballero.io/understanding-iterator-pattern-in-javascript-typescript-using-symbol-iterator)2019 年 6 月 20 日。*