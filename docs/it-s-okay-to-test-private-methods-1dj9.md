# 测试私有方法是可以的

> 原文：<https://dev.to/renegadecoder94/it-s-okay-to-test-private-methods-1dj9>

谷歌一下短语“[我应该测试私有方法](http://lmgtfy.com/?q=should+I+test+private+methods)”，你会得到一大堆归结为“不”的意见。幸运的是，我在这里说测试私有方法是可以的。

## 有什么了不起的？

目前，我正在大学里接受教授软件课程的培训，并且我正在用 Java 编写一个实用程序类，其中有大量的私有 helper 方法。在我的例子中，除了`main`之外，实际上没有任何公开的公共方法，我发现编写与输入流交互的测试很有挑战性。因此，我想写一些 JUnit 测试来证明私有方法的功能。

然而，当我求助于谷歌时，我发现大多数专家都说不要测试私有方法:

*   [为什么 OOP 中不鼓励白盒测试？](https://softwareengineering.stackexchange.com/questions/351140/why-is-white-box-testing-discouraged-in-oop)
*   私有方法应该被测试吗？
*   如何对私有方法进行单元测试？
*   你对私有方法进行单元测试吗？

相反，他们认为我们应该测试调用私有方法的公共方法。在接下来的小节中，我将尝试分解他们的论点。

### 私有方法是实现细节

反对测试私有方法的一个常见理由是私有方法是实现细节:

> 私有方法是一个实现细节，应该对该类的用户隐藏。测试私有方法会破坏封装。
> 
> <cite>[jop](https://stackoverflow.com/a/105021) ，2008</cite>

换句话说，从测试的角度来看，解决方案是如何实现的是无关紧要的。最终，我们希望根据用户的预期行为来测试我们的解决方案。

### 私有方法测试是脆弱的

由于私有方法是实现的细节，我们可以自由地修改这些细节，几乎不需要任何成本。然而，如果我们选择测试我们的私有方法，我们就冒着破坏测试的风险。结果，我们的测试变得脆弱，意味着它们很容易被破坏。事实上，我认为一位堆栈溢出用户说得最好:

> 这里的问题是那些“未来的代码变更”总是意味着重构某个类的内部工作。这种情况经常发生，以至于编写测试会给重构制造障碍。
> 
> <cite>[不法程序员](https://stackoverflow.com/questions/105007/should-i-test-private-methods-or-only-public-ones#comment19029_105209)，2008</cite>

换句话说，脆弱的测试会阻碍重构，这为代码改进设置了障碍。

### 私有方法测试失败可能无关紧要

我见过的一个更有趣的论点是这样的:

> 如果你不能破坏公共方法，私有方法做什么真的重要吗？
> 
> <cite>[钻机](https://softwareengineering.stackexchange.com/questions/100959/how-do-you-unit-test-private-methods#comment272367_100959)，2012 年</cite>

换句话说，我们也许能够破坏我们的私有方法，但是公开的方法可能处于不同的约束下，这导致私有方法中的错误永远不会出现。

## 测试私有方法的案例

总的来说，我同意所有反对测试私有方法的观点。事实上，如果我没有遇到自己测试私有方法的需求，我可能会站在那一边。然而，和往常一样，这个问题有点微妙。

### 公共方法依赖于实现细节

当我们争论说我们不应该关心实现细节时，我们冒着错过我们的公共方法崩溃的边缘情况的风险。换句话说，了解我们的系统是如何设计的对于确保它正确工作是至关重要的。否则我们怎么证明它有效呢？

作为一个愚蠢的例子，想象一个斐波那契数列方法，它根据某个索引输出数列中的项。如果我们测试这种方法，我们如何知道要尝试多少输入来验证这种方法是否有效？对于黑盒测试，我们必须全部尝试。对于白盒测试(这取决于实现细节)，我们只需触及所有分支。

当然，我不认为有人会认为公共方法不应该进行白盒测试，但这确实让我想到了我的第二个观点:公共方法测试和私有方法测试一样脆弱，而且它们通常很臃肿。

### 公共方法测试是脆弱的，并且经常是臃肿的

由于私有方法测试依赖于实现细节，测试可能会随着需求的变化而中断。也就是说，我不确定公共方法在这方面是否清晰。

例如，有时方法会影响对象的状态。我们通常调用这些实例方法，因为它们直接与对象的实例交互。为了测试一个实例方法，我们通常必须设置该对象的状态，这样我们就可以在调用该方法时监控它的行为。

由于我们在测试期间坚持使用公共方法来设置我们的对象，我们可能会遇到测试依赖于多个方法的行为的情况——不一定是被测试的方法。如果我们能够访问私有方法(例如 setters ),我们就能够设置对象的状态，而不需要依赖其他可能工作也可能不工作的公共方法。

更糟糕的是，白盒测试变成了一场噩梦。突然之间，我们不得不将各种各样的数据输入到我们的公共 API 中，希望我们能够获得适当的代码覆盖率。直接测试私有方法并在不再需要私有方法时扔掉这些测试会容易得多。

单就可读性而言，想象一下为一个方法命名 50 多个不同的测试。在几轮重构之后，你甚至不知道哪些测试值得删除。私有方法测试保持了清晰的责任分离。

最后，想象一下放弃一个由 50 多个测试组成的公共方法。不仅所有这些测试都被浪费了，而且[沉没成本谬误](https://youarenotsosmart.com/2011/03/25/the-sunk-cost-fallacy/)基本上保证了我们会拒绝因为公共方法背后的大量测试而贬低它。积累的测试用例的势头会阻止我们改进代码。

### 私有方法测试失败关系重大

最终，我们来到最后一个论点:如果公共方法有效，谁会关心私有方法在做什么？换句话说，只要 API 正常工作，谁会关心某个内部特性是否通过了测试。至少，我觉得这就是论点所在，对吧？

对我来说，私有方法测试失败应该很重要，因为错误可能会在以后出现。毕竟编码是一个动态的过程。换句话说，一个潜在的问题可能不会在今天显现出来，但它可能会有 3 个版本。因此，主动忽略一个可能有 bug 的私有方法就是一个定时炸弹。

此外，我也不喜欢这种观点所表达的情绪。老实说，如果同样的争论发生在其他工程学科，我真的很担心。例如，我希望飞机制造商能彻底测试他们的设备，即使他们有三重冗余来应对故障。

也就是说，我确实认为最初的论点最有说服力。我们可以整天争论测试私有方法的价值，但是许多软件并不是关键任务。当今世界，软件发展很快，公共方法测试可能就足够了。见鬼，比起遥测技术我更喜欢那个。

## 测试私有方法没事

当我开始写这篇文章时，是为了回应网上铺天盖地的关于测试私有方法是个坏主意的文献。老实说，我觉得这有点奇怪。毕竟，我曾经遇到过这样的情况，公共方法是建立在私有方法的基础上的，所以测试公共接口成为隔离 bug 的一种非常低效的方式。换句话说，我们如何知道如何编写正确的测试来测试一些底层私有方法的所有分支呢？

无论如何，测试私有方法是否实际可行是一个完全不同的问题，但是我不会说私有方法测试是好是坏。像计算机科学中的许多争论一样，这个问题更加微妙。

当然，在写这篇文章的过程中，我也在 Kotlin 中开发一个应用程序，我发现只测试公共 API 要实用得多。毕竟，底层的私有方法都非常小，很容易推理。然而，我不能对我写的每个项目都这么说，所以我把选择权交给你:做有意义的事情，仅此而已。

现在，[成为叛离编码者](https://therenegadecoder.com/members/)的高级会员是有意义的！有了高级会员，你可以完全访问博客，所以你可以更好地了解我。如果你需要更多的时间来理清事情，看看下面的一些文章:

*   [Java 中](https://dev.to/renegadecoder94/the-behavior-of-i--i-in-java-ch1) `i=i++` [的行为](https://dev.to/renegadecoder94/the-behavior-of-i--i-in-java-ch1)
*   [如何教授计算机科学中的数组](https://dev.to/renegadecoder94/how-to-teach-arrays-in-computer-science-2f5n)

既然你在这里，为什么不分享一下你对私有方法测试的感受呢？你严格避免它吗，或者他们的情况你认为有意义吗？

帖子[测试私有方法没问题](https://therenegadecoder.com/code/its-okay-to-test-private-methods/)最先出现在[这个叛逆的编码者](https://therenegadecoder.com)身上。