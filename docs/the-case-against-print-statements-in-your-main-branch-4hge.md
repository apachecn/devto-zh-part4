# 反对你的主要分行打印报表的案例

> 原文：<https://dev.to/aryanjnyc/the-case-against-print-statements-in-your-main-branch-4hge>

一位同事(我们代码库的新成员)最近要求我在发生“重要事件”的地方向我们的代码库添加打印语句，特别是`console.log()`语句(因为我们正在使用 React Native)。他解释说这将有助于他更好地理解代码库。

我的膝跳反应是“绝对不行！”然而，我从来没有坐下来充实我的想法，为什么我反对打印声明作为代码库中的永久附件。嗯，在这里。

# 这篇博文是什么

这篇博客文章是我反对在代码库的主要开发分支中只允许信息性打印语句的理由。

# 这篇博文不是什么

这篇博文并不是反对在编码时使用 print 语句。在编写特性或调试现有代码时，打印语句是非常宝贵的。它们提供了在代码运行时到底发生了什么的一瞥。

此外，这篇博文不是关于使用 print 语句来记录未处理的错误。错误打印声明必须通知开发人员任何未处理的错误。这使得它们比单纯的信息打印声明有用得多。

最后，这篇博文并不是要将印刷声明排除在生产之外。它更进了一步，认为它永远不应该成为代码库的主要开发分支的一部分。

# 打印报表在你的开发分支中的负面影响

## ⛷️坐蜡了

允许打印语句进入您的开发分支是一条不归路。是否允许将所有打印报表合并到主分支中？如果不是，哪些是允许的，哪些是不允许的？是什么让一个主观有用的打印声明比另一个更好？谁决定在哪里使用打印报表？如果团队决定在哪里允许打印声明，应该在这个决定上花费多少时间和资源？文件和货物清单在哪里👼对抗邪恶😈保留打印报表？谁将维护上述文件？

现在，我想你明白我的意思了。一个看似微不足道的决定会导致开发团队的大量额外开销。

## 🤯对新开发人员的焦虑和不知所措的感觉

看似随意的打印语句会让完全不熟悉代码库的开发人员不知所措。

随着随机对象和字符串在控制台上运行，焦虑和冒名顶替综合症可能会蔓延开来，因为新开发人员，尤其是初级开发人员，可能会觉得他们永远也不会理解现有的代码库

此外，这些打印语句诱使好奇的开发人员找到他们从哪里来以及他们为什么要解雇(好吧，也许我在这里是代表我分心的自己)，即使他们与他们应该开发的功能或他们应该修复的 bug 没有任何关系。这当然导致了开发时间的损失(尽管你可以说这激发了好奇心，导致了对代码库的更深入的理解)。

## 🙉噪音

添加到代码库中的每个额外的 print 语句都是另一个 print 语句，迟早会被一个或多个开发人员忽略。随着印刷声明的老化，它的有用性急剧下降。最终，即使是将 print 语句添加到代码库中的开发人员也会发现它毫无用处。怎么处理这些噪音？开发人员现在需要决定哪些曾经是“有用的”(参见:好👼打印报表)打印报表必须留下来，现在“没用了”(见:邪恶😈打印报表)必须删除。然后，他们必须发出一个 pull 请求来删除这些“无用”的打印语句。print 语句的垃圾收集成为一项永无止境的重复任务。

此外，代码库中“无用”的打印语句会导致...

## 💔破碎的窗户(也许还有破碎的心)

想象一下，一个充满了许多人认为“无用”的打印语句的代码库。它们是随意添加的，在进入开发分支之前从未清理过(因为，嘿，现在我们的代码库中允许打印语句)。这些邪恶的印刷声明成为必须清理的技术债务(因为它们现在是噪音)。在一个理想的开发世界中，这种技术债务会立即被清理，因为这个看似很小的问题可能会导致大问题:代码库的一部分未修复会导致代码库的另一部分未修复，从而导致另一部分。最终，一个小问题滚雪球般变成许多小问题，滚雪球般变成一个丑陋的代码库，这是一个工作阻力。

这就是[破窗理论](https://www.artima.com/intv/fixit2.html)。不要让打印的语句伤了你的心，导致代码库充满了破碎的窗口。

## 🐛更难调试的代码库

我们已经确定，由于所有这些原因，控制台一片混乱😈邪恶的打印声明尚未通过人类垃圾收集过程。每点击一个按钮都会导致几十个无用的打印语句。

现在，您需要开发一个新特性，并希望添加那些有用的 print 语句(每个开发人员都使用这些语句)来确保函数正常运行、正确发出 HTTP 请求以及服务器响应符合数据协定。您添加了您的 print 语句，该语句现在被一堆无用的 print 语句淹没了。

代码库调试起来更加困难和耗时，因为新的打印语句必须位于其他打印语句的海洋中。任何使代码库更难调试的东西都是巨大的负面影响(因为这会导致代码中更多的 bug)。

## ❌误将日志投入生产

由于代码库中有几十个 print 语句，并且许多语句被多次调用，所以出现重大错误的可能性会增加。更具体地说，记录真实用户的[个人身份信息(PII)](https://en.wikipedia.org/wiki/Personal_data) 的机会增加了。

也就是说，如果你的代码库中允许打印语句，你必须在投入生产之前去掉它们。当然，这是必须完成的另一项维护工作，以支持您亲爱的打印声明。😴

# 结论

像允许在主分支中打印语句这样无关紧要的决定可能会对代码产生巨大的影响。当然，每个代码库和开发团队都是不同的。花时间分析这个决定(以及所有的决定！)和你的团队。

安全编码。