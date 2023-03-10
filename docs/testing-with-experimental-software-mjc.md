# 用实验软件测试

> 原文：<https://dev.to/jtenner/testing-with-experimental-software-mjc>

我们每个人都这样做。我们提交一个 git，把它推到一个分支，然后持续集成抱怨一个失败的测试。当我开发名为`as-pect`的汇编脚本[测试软件](https://dev.to/jtenner/testing-with-assemblyscript-and-the-usefulness-of-value-3egn)时，这种情况在我身上发生过很多次。我当时正在设计让*感觉*正确的东西，因为这个软件的每个部分都是新的，我不得不冒险走上一条还没有人尝试过的道路。

这就带来了问题，因为我发现自己在完成编写软件的之后制定了规范*。事后看来，我本可以做一些更好的规划，先做一个规范*。相反，我固执地颠倒了软件开发过程。现在，我将不得不从头开始重新创建我编写的许多 JavaScript 测试。**

 *在这个过程中，我找的一个借口是我没有“预先”解决问题的工具。更糟糕的是，我没有一本可以使用的手册，因为我正在涉足新的领域。这些借口是真的，但它们只会让事情变得更糟。我现在相信我可以通过改变我的思维方式提前写好测试。

让业务逻辑成为你的责任并不是跳过测试的借口。

例如，假设你的雇主想让你知道如何制作一个带有文本框和按钮的网站。当点击这个按钮时，它会计算一个斐波那契数，并显示在一个`alert()`中。(别问为什么！你会得到报酬的！)

你可能想先开始写软件。谁需要斐波那契函数的规范？通常，我们可能会从`npm`开始安装一个库，并让其他人为您完成这项工作。但是，这个函数足够小，应该非常容易编写。

使用这种逻辑，你可以决定冒险在完全没有任何测试的情况下提前编写你的`fib`函数。

```
function fib(index: number): number {
  if (index === 1) return 0;
  if (index === 2) return 1;
  return fib(index - 2) + fib(index - 1);
} 
```

“谁需要*测试*？”你可能会对自己说。“事实上，这个功能看起来非常好用！”

现在，您已经实现了该功能，甚至可以将其交付生产。你的老板现在非常高兴，因为你很快地实现了它，但有些事情可能会在内心侵蚀你。这个函数有问题，除非您正在寻找它，否则您可能会发现自己正盯着一份错误报告...

*亲爱的【你】，*

*当我不小心传递了`fib`函数`-1`时，我发现了这个错误，它导致了无限堆栈递归。当用户决定将`"-1"`放入文本框时，`parseInt`可能会返回负数。*

我们的客户对此很不满意，我们的老板说我们应该先进行一些健全性检查，以确保我们的输入是有效的。

*谢谢！很抱歉给你添麻烦了，*

*【同事】*

啊哦。

这个错误报告说我们的代码有问题。现在，我们必须对一些我们在开发过程中很早就能检查到的事情负起责任。

[https://www.youtube.com/embed/jQvvmT3ab80](https://www.youtube.com/embed/jQvvmT3ab80)

按照你自己的标准决定做正确的事情[是你在发现解决方案的那一刻可以做的事情。除了*只是*修复错误，还有另一条路可以走。不幸的是，这需要从头开始重新实现。考虑以下步骤:](https://dev.to/jtenner/by-your-own-standard-29go)

1.  进入发展的发现阶段，使用`git branch -b new-branch`找到解决方案
2.  创建一个包含您的解决方案的小型原型(大部分是工作的)
3.  在不使用原型的情况下，在 markdown 文档中写下它应该如何工作
4.  考虑所有可能的输入和输出，包括错误和边缘情况
5.  `git reset --hard`

没错。

摧毁你的原型。

这种事情需要大量的训练，因为你可能想要重用你的原型，使用你已经完成的工作。像这样使用一个原型是一个可怕的错误，会给你将来带来麻烦。

如果你不想从零开始`git reset --hard`和完全*，只需自己清除函数实现并使用块注释编写文档。* 

```
/**
 * The Fibonacci function is defined as a function that takes an index
 * and returns the value of the number in the Fibonacci sequence at that
 * index.
 *
 * Typically, the following things must be true.
 * - Index must be a number, otherwise throws TypeError
 * - Index must be a positive integer otherwise throws Error
 * - function returns a non-negative integer
 * - The value at index === 1 is 0
 * - The value at index === 2 is 1
 * - The value at index is fib(index - 1) + fib(index - 2)
 */
function fib() {} 
```

这个过程的捷径阻止你训练你的头脑，并告诉你你不必遵循软件开发的规则。更重要的是，这个功能现在被精确地解释给任何需要调试它的人。干得好！成功了一半。现在是从*开始*实现规范的时候了。

文档的第一行给出了第一个单元测试。这是最重要的单元测试，因为它谦逊地提醒我们，任何人都可能犯最简单的错误。不要跳过它(！)并断言`fib`本身就是一个函数。在你的代码中的其他地方犯错误*是不可避免的。* 

```
describe("the Fibonacci function", () => {
  it("should be a function", () => {
    expect(typeof fib).toBe("function");
  });
}); 
```

这个测试通过了。很容易指出这个测试有多简单，但是 JavaScript 是一个运行代码的危险地方，你不知道会发生什么。

现在您已经编写了您的第一个测试，您可以将它提交到您的新分支。这很好。每一个增量变更都应该导致一个提交，其中包含对*为什么*发生变更的简短描述，以及一些描述正在解决的问题的测试。遵循这样的步骤将会产生一个讲述*故事*的变更日志。这种讲故事的方式对你的经理来说非常好，因为现在他们可以跟着检查你的工作。

接下来，向正确的方向再迈一小步，开始描述函数输入是如何工作的。

```
it("should throw a TypeError when passed null", () => {
  expect(() => fib(null)).toThrow(TypeError);
}); 
```

运行测试套件将显示实现当前没有通过。幸运的是，有一种特殊的方法可以确定一个参数是否不是数字。

```
function fib(index: number) {
  if (typeof index !== "number") throw new TypeError("Invalid Input: index was " + index);
} 
```

这将导致实现通过测试。接下来，编写更多的测试来验证其他无效输入会抛出一个`TypeError`。

```
it("should throw a TypeError when passed undefined", () => {
  expect(() => fib(undefined)).toThrow(TypeError);
});

it("should throw a TypeError when passed a string", () => {
  expect(() => fib("whoops!")).toThrow(TypeError);
}); 
```

所有这些测试都已经通过，所以第二部分已经完成。现在是奖励自己一个`git commit`的好时机。轻拍一下后背后，你回过头来阅读说明书的下一部分。

它非常明确地说它只接受整数。那这是怎么回事？好吧，你可以走捷径，跳过测试，但是你已经走上了一条令人敬畏的道路，而是决定先多写几个测试。

```
it("should throw when value is a float", () => {
  expect(() => fib(42.1)).toThrow(Error);
});

it("should throw when the index is negative", () => {
  expect(() => fib(-1)).toThrow(Error);
});

it("should throw when the index is zero", () => {
  expect(() => fib(0)).toThrow(Error);
}); 
```

这些测试都应该失败，因为在这些情况下 fib 函数不会抛出`Error`。

要解决第一个测试，不要试图一次解决所有的测试，而是快速地在 Google 上搜索，找出如何确定一个数字是否是整数。希望你首先偶然发现了 [mdn 网络文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number/is%20Integer)。

“啊！这正是我需要的。”

```
function fib(index: number) {
  if (typeof index !== "number") throw new TypeError("Invalid Input: index was " + index);
  if (!Number.isInteger(index)) throw new Error("Index Out of Range: index was " + index);
} 
```

这应该会导致第一个测试通过，但不会通过第二个或第三个测试。当然，添加一个简单的布尔表达式来验证索引为正涵盖了这两种情况。

```
 if (!Number.isInteger(index) || index < 1) ... 
```

您的控制台亮起绿色。再做一次，享受一口咖啡。

每多一个绿色勾号，你就给你的大脑注射了一点多巴胺。这正是*你每次实现新东西时想要发生的事情。*

现在让我们再尝试一次实现规范。

```
it("should return an integer", () => {
  expect(Number.isInteger(fib(12))).toBeTruthy();
});

it("should return 0 when index is 1", () => {
  expect(fib(1)).toBe(0);
});

it("should return 1 when index is 2", () => {
  expect(fib(2)).toBe(1);
});

// yes. this is a unit test, not a behavior test
it("should return 165580141 when index is 42", () => {
  expect(fib(42)).toBe(165580141);
}); 
```

太好了。那看起来已经够全面了！让我们一口气完成这个测试套件！

```
function fib(index: number) {
  if (typeof index !== "number") throw new TypeError("Invalid Input: index was " + index);
  if (!Number.isInteger(index)) throw new Error("Index Out of Range: index was " + index);
  if (index === 1) return 0;
  if (index === 2) return 1;
  return fib(index - 2) + fib(index - 1);
} 
```

您终于可以最后一次提交代码了。现在一切都令人满意了，你可以给你的老板写一封电子邮件，告诉他你写了一些测试来确保这个问题不会再次发生。

这种过程有机地指导你的开发，随着每次测试，这个过程会越来越容易。这种挣扎是过程的一部分，你需要采取的步骤比你想象的要小。

代替软件，你已经为自己写了一个行为准则，并通过让你自己对结果负责来测试你关于功能应该如何工作的假设。

当然，这只是一个递归斐波那契函数，但是现在*你*不仅仅是一个开发者。

你很有纪律。

问候，
[@jtenner](https://dev.to/jtenner)**