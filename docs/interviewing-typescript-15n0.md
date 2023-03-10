# 面试打字稿

> 原文：<https://dev.to/ketoaustin/interviewing-typescript-15n0>

有时候 JavaScript 试图猜测你的意思。

例如:

```
true + []

// returns "true" 
```

Enter fullscreen mode Exit fullscreen mode

你可以说 JavaScript 试图通过不问任何问题和只得到结果来提供帮助，但有时这些结果是有代价的。有时，直到您的代码进入生产阶段，您才可能发现 JavaScript 猜测产生的错误。TypeScript 通过更及时地提供错误信息来帮助解决这个问题

例如:

```
true + []

//error TS2365: Operator '+' cannot be applied to types 'true' and 'never[]'. 
```

Enter fullscreen mode Exit fullscreen mode

# 一个场景

想象一下，有一天你发现自己在抱怨，一旦你的 JavaScript 代码已经投入生产，你就会频繁地出现类型错误。这让我们很头疼——我们不能雇人来处理这些 JavaScript 问题吗？难道我们不能找一个人和我们的 JavaScript 代码一起工作，并在运行前捕捉类型错误吗？

# 面试流程

你在 Hacker News 上发布了一个招聘信息，24 小时内 TypeScript 的简历就会进入你的收件箱。他们有一封像样的求职信，所以你打电话给他们要一个电话屏幕。这个候选人对你很突出，因为他们说他们会在你写程序的时候在你的文本编辑器中显示错误信息。你邀请 TypeScript 来现场。

在现场，TypeScript 做了一个关于你的代码在运行前如何进行类型检查的完整演示。但是，你仍然有很多关于一切到底是如何工作的问题。

你说，“嘿，我们走一遍。我知道现在我的代码就是这样编译的……”

你抓起一支记号笔，在黑板上写下:

JavaScript 代码如何运行的高级概述:

1.  JavaScript 源代码由一个名为编译器的程序解析，编译器将代码转换为 AST(抽象语法树)，这是一种忽略空白、注释和空格的数据结构。
2.  AST 被转换成字节码。
3.  字节码被输入到另一个名为 runtime 的程序中，这时代码被求值。

TypeScript 点头同意，并说，“是的，JavaScript 代码就是这样运行的。当我参与时，有一些额外的步骤允许编译器对代码进行类型检查。

1.  TypeScript 源代码被编译成 TypeScript AST
2.  **类型脚本 AST 已经过类型检查**
3.  TypeScript AST 被转换为 JavaScript
4.  JavaScript 被转换成 JavaScript AST
5.  JavaScript AST 被转换成字节码。
6.  字节码是在运行时计算的。

TypeScript 补充道，“请注意，我提高了您在运行前捕捉类型错误的能力，但不会改变运行代码的结果。从语法上讲，所有有效的 JavaScript 都是有效的 TypeScript。换句话说，通过使用 TypeScript，您实际上并没有改变 JavaScript 代码的工作方式。”

> TypeScript 有时用第三人称称呼自己。:D

## 技术问题

事情进展顺利，一切似乎都在顺利进行。你决定深入挖掘，问一些更尖锐的问题。

你问“你是动态类型的还是静态类型的？

TypeScript 回答说，“我并不完全是其中之一。您可以使用类型注释来定义类型，或者您可以简单地省去它们，我将尽最大努力来推断类型。当我在编译时知道程序中所有东西的类型时，我处于最佳状态，但是如果我没有所有这些信息，我仍然可以编译所有东西。

你接着问，“如果我们迁移到你那里，为了让我们的代码工作，我们必须把所有东西都转移到 TypeScript 吗？”

TypeScript 傻笑了一下，然后回答道，“您不必从一开始就拥有完整的类型覆盖，您可以逐渐引入我。这与我之前提到的相联系:所有的 JavaScript 都是有效的类型脚本。”

短暂的停顿后，TypeScript 说，“看，我要坦率地告诉你，当编译器对你的代码进行类型检查时，它需要被修复。有些人可能会说我有点爱抱怨，但我只是直言不讳。"

你有点被傲慢拒之门外，但你还是感谢他们的透明。接下来，通过询问他们在发现错误后是如何交流的，你又回到了更多的“软技能”上。

他们回答说，“好吧，如果你有一个还算不错的文本编辑器，你会在需要修改的地方看到一些红色的曲线。这又回到了我如何在编译时进行类型检查，在编译时而不是运行时给你语法和类型错误。”

## 报价延期

打字稿，你被录用了！

 [![drawing of shiba raising hands in celebration wearing a shirt that says typescript](img/1cb6c5eda9c24768967af8555680182a.png)
T4】](https://imgur.com/6RPKIuS)

*来源:[编程打字稿](http://shop.oreilly.com/product/0636920158059.do)，2019 年 5 月。鲍里斯·切尔尼。第二章。打字稿:10000 英尺的视野*