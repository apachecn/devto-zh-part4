# 如何在 JavaScript 中掷骰子？

> 原文：<https://dev.to/rocambille/how-to-roll-a-dice-in-javascript-51j0>

让我们一步一步打造终极骰子。

*最后更新:2022 年 5 月 11 日*

## 以 Math.random()为基础

骰子是一种工具，每次你掷骰子时它都会提供一个随机整数。类似这样的:

```
function rollDice() {
  return /* some randomly generated number */
} 
```

Enter fullscreen mode Exit fullscreen mode

每种编程语言都有一个内置的随机函数。在 JavaScript 中是 [`Math.random`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random) :

```
function rollDice() {
  return Math.random();
} 
```

Enter fullscreen mode Exit fullscreen mode

这是一个好的开始:返回一个随机数。记住`Math.random`对于像密码学或赌场游戏这样严肃的事情来说不够“随机”——如果那是你的业务，请阅读关于 [Crypto.getRandomValues](https://developer.mozilla.org/en-US/docs/Web/API/Crypto/getRandomValues) 的内容。`Math.random`公平到可以和朋友掷骰子。让我们试试:

```
>> function rollDice() {
     return Math.random();
   }
>> rollDice();
<- 0.7367823644188911 
```

Enter fullscreen mode Exit fullscreen mode

这个`0.7367823644188911`并不是我们真正想要的…根据文档，`Math.random`返回一个介于 0(包括 0)和 1(不包括 1)之间的十进制数。对于 6 面骰子，我们需要一个从 1 到 6 的整数。作为第一个猜测，你可以乘以 6:

```
>> function rollDice() {
     return Math.random() * 6;
   }
>> rollDice();
<- 4.3380209914241235 
```

Enter fullscreen mode Exit fullscreen mode

所以我们会有一个介于 0(含)和 6(不含)之间的随机十进制数。到目前为止，一切顺利。下一步是获取整数值:

*   如果 0 ≤ `Math.random() * 6` <为 1，则返回 1
*   如果 1 ≤ `Math.random() * 6` < 2，则返回 2
*   如果 2≤`Math.random() * 6`T1】3，返回 3
*   如果 3 ≤ `Math.random() * 6` < 4，则返回 4
*   如果 4 ≤ `Math.random() * 6` < 5，则返回 5
*   如果 5 ≤ `Math.random() * 6` < 6，返回 6

这可以使用 [`Math.floor`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/floor) 来完成。让我们再试一次——使用一个到 console.log 多卷的 for 循环:

```
>> function rollDice() {
     return Math.floor(Math.random() * 6);
   }
>> for(let i = 0; i < 5; i++) console.log(rollDice());
   5
   1
   4
   2
   0 // WTF? 
```

Enter fullscreen mode Exit fullscreen mode

再一次，这不是我们想要的...我们在这里得到的是:

*   如果 0 ≤ `Math.floor(Math.random() * 6)` < 1，则返回 0。不是 1。
*   如果 1 ≤ `Math.floor(Math.random() * 6)` < 2，返回 1。不是 2。
*   如果 2 ≤ `Math.floor(Math.random() * 6)` < 3，返回 2。不是 3。
*   如果 3 ≤ `Math.floor(Math.random() * 6)` < 4，返回 3。不是 4。
*   如果 4 ≤ `Math.floor(Math.random() * 6)` < 5，返回 4。不是 5。
*   如果 5 ≤ `Math.floor(Math.random() * 6)` < 6，返回 5。不是 6。

为了用`Math.floor`得到想要的结果，我们必须在返回之前加 1:

```
function rollDice() {
  return 1 + Math.floor(Math.random() * 6);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有一个函数来模拟我们的 6 面骰子:)

> 是的，但是……如果我们想要一个 4 边、8 边、12 边或 20 边的呢？

没什么大不了的:您可以更改代码中的幻数 6 作为参数，传递骰子的最大值。大概是这样:

```
function rollDice(max) {
  return 1 + Math.floor(Math.random() * max);
}

const rollDice4 = () => rollDice(4);
const rollDice6 = () => rollDice(6);
const rollDice8 = () => rollDice(8);
const rollDice12 = () => rollDice(12);
const rollDice20 = () => rollDice(20); 
```

Enter fullscreen mode Exit fullscreen mode

## 终极骰子

我曾经受到一个愿景的启发:伊万·萨瑟兰(Ivan E. Sutherland)于 1965 年创作的《终极展示》(T1)。其中，我喜欢这句话:

> 没有理由为什么计算机显示的对象必须遵循我们所熟悉的物理现实的普通规则。

我们用一个参数来代替骰子的边数。为什么不去掉另一个幻数呢？这个难看的 1 可能会成为另一个参数:

```
function rollDice(min, max) {
  return min + Math.floor(Math.random() * (max - min + 1));
}

const rollDice4 = () => rollDice(1, 4);
const rollDice6 = () => rollDice(1, 6);
const rollDice8 = () => rollDice(1, 8);
const rollDice12 = () => rollDice(1, 12);
const rollDice20 = () => rollDice(1, 20);
const rollSomeUltimateDice = () => rollDice(42, 42); 
```

Enter fullscreen mode Exit fullscreen mode

这个最终版本允许模拟一个不是从 1 开始的骰子。此外,`max`允许模拟超越“物理现实的普通规则”的统一公平骰子。想象一个七边形的。你可以模仿你最喜欢的骰子游戏遵循其普通规则。但是如果你能想象一个，掷一个现实中永远不会存在的骰子；)