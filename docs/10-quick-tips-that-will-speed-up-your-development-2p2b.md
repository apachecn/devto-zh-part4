# 加速你发展的 10 个快速提示

> 原文：<https://dev.to/theringleman/10-quick-tips-that-will-speed-up-your-development-2p2b>

每个人都想要银弹。如何在 24 小时内学会 x？好吧，我讨厌成为坏消息的传递者，但是编码是困难的。你会想用头撞桌子，你会想哭，你会哭。但总有一天会好起来的。而且还有加速学习的方法。这里有 10 个你可以马上实施的快速提示，它不仅会加快你的学习，还会加快你的发展进程。

### 10。使用调试器。

一些人认为这一知识是[编码](https://softwareengineering.stackexchange.com/a/78154)的基本规则。单单使用这个工具就能帮助你获得关于代码的丰富知识。仅仅使用打印语句是不够的。您必须熟悉代码的执行。这就是你学习的方式。这里有一个关于调试器基本用法的[教程](https://codejourney.io/comfortable-debugger)。

> 虽然简单易学，但我认为调试应该包含在每一所大学的课程中，并在每一家软件开发公司进行实践。–[彼得·富兰克林](https://www.linkedin.com/in/peterjfranklin/)首席执行官普里兹

### 9。要有表现力。

在代码评审中，我喜欢看到`theArrayReturnedFromAnIterator`胜过`i`。事实上，如果我看到这样的变量，我不会批准审查。表达性提供了对代码的快速浏览。不仅仅是为了别人，也是为了未来 6 个月的自己。在这个上下文中，变量是什么意思？是迭代器吗？是 for 循环中的计数吗？我是一个对象吗(我不会根据它的类型来命名所有的变量，也不会根据注释的用途来命名)？对你的变量更加明确。现在写出来可能看起来很乏味，但从长远来看，你是在给自己分红。有时候，那些 Java 开发者有正确的想法。

[![A group working together on code.](img/85cb3c1cae4c52c004841a0800a632ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Z5Lqeoqf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn.codejourney.io/2019/07/03203046/nesa-by-makers-IgUR1iX0mqM-unsplash-1-1024x683.jpg)

### 8。与你的团队沟通。

经常沟通。表达你的想法，不管你对代码有多陌生。如果你来自不同的工作，请表达你的经历。不，我们不认为你在吹牛。您可以为公司节省数百万美元，为您的团队节省无数时间。你知道的比你想象的多。随意分享吧。事实上，我觉得有义务分享它。

### 7。使用 IDE。

如果你允许，你的工具会让你成为更好的程序员。工具的质量很重要。最新的文本编辑器很好，但是 IDE 将允许你深入你的代码。有接近更专业路线的免费选项。VSCode 成为标准是有原因的，首先它很棒。第二，通过几个简单的插件，你可以模拟很多昂贵的 IDE 提供的功能。这只会让重构和调试更容易。当你准备好了，给自己买一个 JetBrains 的许可证。他们的工具教给我的编程知识比我能表达的还要多。

### 6。遵循编码标准。

标准的存在是有原因的。你可能不喜欢 4 个空格，或者 2 个，或者制表符，或者我不在乎。语言标准是什么？我想跳到你的 PHP 类中，知道你所有的属性都是在顶部声明的。你的静态方法低于你的属性。我想看一个 if 语句，并且能够从一个块跳到另一个块。您可以使用 linter 自动完成这项工作。一个 linter 将让你与社区设定的标准保持同步。假以时日，你会盲目地追随它。使用它。爱死了。自动化它。

### 5。保持方法简洁。

嘿，看到那个 650 线控制器动作了吗？虫子就是这样诞生的。

> 大多数情况下，过长的方法是万恶之源。–[源制作](https://sourcemaking.com)

如果你有一个包含嵌套条件的方法，那就把它分开。做 15 个小方法。您可以跳到每个方法，并知道它只做一件事。由于您也遵循了步骤 9，您将能够根据函数的名称准确地说出该方法做了什么。

```
 //This is hard to read, and gets messy quick.
if ($date->before(SUMMER_START) || $date->after(SUMMER_END)) {
  $charge = $quantity * $winterRate + $winterServiceCharge;
} else {
  $charge = $quantity * $summerRate;
}

//This is much better.
//Thank you Source Making for the wonderful examples.
if (isSummer($date)) {
  $charge = summerCharge($quantity);
} else {
  $charge = winterCharge($quantity);
} 
```

相信我，再明确一点，长远来看会让你快很多。

### 4。保持类单独使用。

创建新的类，不，你没有膨胀代码！我患这种恐惧症已经太久了。在大多数现代工具中，在类之间跳转是一个简单的击键。例如，在 PHPStorm 中，将鼠标悬停在一个类名上，按下 cmd + b。会直接跳转到该类。和上一个技巧一样，我想读一下您的类名/名称空间，并马上知道这个类做什么。只用一两个方法创建一个新类。你会及时延伸它，或者你会从它身上学到什么可以延伸。小总是更容易测试，也更容易阅读。如果你想要更深入的解释，请仔细看看[坚实的](https://en.wikipedia.org/wiki/SOLID)原则。

### 3。打碎东西。

版本控制有一个很好的理由。创建新的分支。打破一切！测试你的想法。看它哪里成功，看它哪里失败。这是你学习的方式。当我第一次被雇佣时，我害怕尝试任何与我的前辈不同的东西。我认为我的代码需要像他们的一样。当我最终变得自信时，是的，我会打破东西，这是正常的。我开始学习。这是软件的乐趣之一。你可以在一个放错位置的[分号](https://www.theverge.com/2017/3/2/14792442/amazon-s3-outage-cause-typo-internet-server)里把互联网带出去。不过没关系，它总是可以恢复的。毕竟只是一个网站 [![😉](img/d01d77fce0ff5b3851e7fc16aa3e21dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CDd67yc0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f609.png) 。

### 2。毫无歉意地编写测试。

如果你的公司不做测试，那就改变吧。带头冲锋。测试您的代码。

> 你不应该要求写单元测试，就像你不应该要求做好工作一样。–[泰德·本迪克森](https://medium.com/@theobendixson/how-not-to-convince-your-boss-to-let-you-write-unit-tests-155bd970d7d1)

没有什么比运行单元测试更自由的了。这可能需要更长的时间，但是一旦测试覆盖范围扩大，您就可以按照自己的意愿进行重构。你会知道你的代码是有效的。最棒的是，你可以责怪那边的 Chad，当他添加代码而不运行测试套件时…

### 1。构建个人项目。

这是你能为自己做的最重要的事情。工作之外学习。建立一个你内心深处知道会点燃你激情的项目。你将对你的语言、工具和过程有如此深刻的理解。这是无价的。我无法在这篇短文中表达这有多重要。我恳求你，如果你想变好，在工作之外建立。追逐激情。然后把它变成别人会用的东西。分享它，享受它，热爱它。

### 关闭

其中一些步骤需要你花时间去学习，单元测试。其他的可以使用调试器立即实现。关键是，如果你使用这些步骤，你会在开发中变得更快。请在评论中告诉我你的想法，以及你是否有其他对你有帮助的建议！

如果你想加入我的[代码之旅](https://codejourney.io)，请来[访问](https://codejourney.io)，阅读一些帖子并加入对话。我希望看到和听到更多的你！