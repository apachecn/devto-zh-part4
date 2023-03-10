# 你可以获得阿波罗 11 号的源代码，并学习相关课程

> 原文：<https://dev.to/pluralsight/you-can-get-the-source-code-for-apollo-11-and-take-a-course-on-it-2gdi>

在软件开发中，你会听到“登月”这个术语。如果某样东西是“登月”，那它就是非常困难的东西，比如登上月球。我们这样说是因为一些应用程序做了一些很酷的事情，但是那些...让我们登上了月球？最初的“月球拍摄”是关于什么的？

## 把我们送上月球的软件

[![](img/7c5d7f4cd12217bf5d963fe5a08c2ba2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JHE7McZx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xcknrbgy3o8hkqte6jcy.jpg)

遇见[玛格丽特·哈米尔顿](https://en.wikipedia.org/wiki/Margaret_Hamilton_(software_engineer))。她是麻省理工学院仪器实验室的软件工程主任，该实验室负责为阿波罗太空计划开发机载软件。这是她站在一堆用来把我们送上月球的源代码旁边。今天，当 Visual Studio 运行缓慢时，我们会抱怨。

如果你是任何类型的开发人员，你应该感谢她，因为在她出现之前，“软件工程师”甚至不是一个术语。

想象一下，你必须编写一个第一次运行就能正常工作的软件，而且要完美地运行，否则就会有人死亡。听起来压力很大，对吧？玛格丽特和她的团队做到了。他们建造了它，而且成功了。它成功了。

他们让一台机器在月球着陆，其计算机的处理能力只有 iPhone 的 0.08%。

你可以在这里获得更多[背景和细节。](http://bit.ly/2JDO9Wi)

### 源代码

几年前，罗恩·伯基发布了阿波罗 11 号的源代码。他为此做了大量工作。这是阿波罗制导计算机的源代码。

你不仅可以下载源代码，而且他为 AGC 创建了一个模拟器，在[虚拟 AGC 页面](http://www.ibiblio.org/apollo/)。你可以深入研究这些系统以及它们是如何工作的，相信我，这是极客们的天堂。这里有很多很棒的东西。甚至还有一个[对 AGC](http://www.ibiblio.org/apollo/ForDummies.html) 更友好、更温和的介绍，你可以去看看熟悉一下。

AGC 源代码是用汇编语言写的，对我们大多数人来说是陌生的。我已经玩够了 x86 汇编程序，知道这不是我的职业，但是仔细阅读大量的源代码，你可以拼凑出这些东西是如何工作的。

### 科曼奇人和杰出人物

如果你深入研究代码，你会看到它分为两部分，科曼奇和卢米娜里。

“科曼奇”是“T0”指令舱“T1”的软件，“卢米娜”是“T2”登月舱“T3”。

**指令舱**是包含船员和重要设备的圆锥体，是返回地球的飞船。

[![](img/b460e6a5d1ba68aad0dc10e7c30460ec.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GPPsidYr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ag5nyqkixbrlm2m1okk8.jpg)

登月舱是登陆月球的模块。

[![](img/423e81feeb697f1b06e28330b3fe549a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B9Ucmbq4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bxc4xhzjxgwhz3jrwhhr.jpg)

观察这些系统如何相互作用是非常有趣的。当你浏览源代码时，你可以看到很多很酷的提示，告诉你一切是如何工作的。

### DSKY

DSKY 是计算机的用户界面。你可以通过一个类似计算器的界面输入命令，它直接连接到 AGC。这些命令包含一个动词和一个 T2 名词。

[![](img/e4f8fcb94327a67d86d4fd3d78140f85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UYajXFW0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/plgeozasvsxv3sedks4j.jpg)

如果你足够深入地研究源代码，你会看到很多对 DSKY 和相关命令的引用。这是当时工程界的奇迹。当然，如果你想玩的话，还有一个 DSKY 模拟器。

### 我怎么知道这么多这个？

我可能听起来像一个经验丰富的专家，但我刚刚参加了关于阿波罗 11 号代码的免费课程，然后开始挖掘代码和研究东西。

[![](img/43bf4737bf1fcca20211b97fd18b291f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6AbrG-kN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b0l3q1qp2pn2xj17ijwy.jpg)

**[点击此处免费参加本课程](http://bit.ly/2JDO9Wi)**

为了纪念阿波罗 11 号登月 50 周年，Simon Allardice 在 Pluralsight 上创建了这个探索 AGC 的精彩课程。这是一个快速课程，它不会教你如何编写 AGC 专用汇编代码，但它会让你很好地了解当时的工作原理以及整个过程是如何配合的。看完这个你会对源代码有更好的理解，并开始寻找复活节彩蛋。

## 结论

如果你像我一样是个书呆子，你会喜欢这样的东西。很明显，这不是某种积极开发的代码，你可以做拉请求和添加功能。没有人会雇用你，因为你把 AGC 知识放在简历上。但是浏览这段代码，弄清楚事情是如何工作的，并看看代码中滑稽的注释，确实很有趣。如果你真的对这类东西感兴趣，这很有趣，值得花时间去探索。

如果你想进一步讨论，你可以在推特上对我大喊。

尽情享受吧！