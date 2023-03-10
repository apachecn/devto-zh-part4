# 保护子句:如何清理条件句

> 原文：<https://dev.to/bootdotdev/guard-clauses-how-to-clean-up-conditionals-2fdm>

开发人员首先学习的技术之一是 [if/else 语句。由于显而易见的原因，if/else 语句是创建逻辑树的主要方式，在逻辑树中，根据输入变量的不同，计算会得到不同的处理。然而，复杂和嵌套的 if/else 语句成为推理的认知负担，下一个开发人员可能很难快速理解。](https://www.w3schools.com/js/js_if_else.asp)

## 守护条款

Guard 子句是一种利用从函数中提前返回(或通过循环中断/继续)的能力来使嵌套条件更加一维的方法。

我主要是一名 [go 开发人员](https://golang.org/)，go 中处理错误的方式自然会鼓励开发人员利用保护条款。当我开始编写更多的 javascript 时，我很失望地看到我正在编写的代码中存在许多嵌套的条件。

<figure>[![](img/a683cd47837ad91e7275ec7669322c66.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RfaJTd8Y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/08/golang-gopher-1024x683.png) 

<figcaption>走的是屎</figcaption>

</figure>

我们来看一个假的 javascripty 例子:

```
function getInsuranceAmount(status) {
  let amount;
  if (!status.hasInsurance()){
    amount = 1;
  } else {
    if (status.isTotaled()){
      amount = 10000;
    } else {
      if (status.isDented()){
        amount = 160;
        if (status.isBigDent()){
          amount = 270;
        }
      }
    }
  }
  return amount;
} 
```

Enter fullscreen mode Exit fullscreen mode

可以用保护子句代替:

```
function getInsuranceAmount(status) {
  if (!status.hasInsurance()){
    return 1;
  }
  if (status.isTotaled()){
    return 10000;
  }
  if (status.isDented() && status.isBigDent()){
    return 270;
  }
  if (status.isDented()){
    return 160;
  }
  return 0;
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子可能仍然不是实现这个功能的最佳方式，但是它更容易阅读和理解。在编写代码时，通过减少读者在任何给定时间需要考虑的事情的数量来减少他们的认知负荷是很重要的。

在第一个例子中，如果开发人员试图确定何时返回 **270** ，他们需要考虑逻辑树中的每个分支，并试图记住哪些情况重要，哪些情况不重要。对于更一维的结构，只需按顺序遍历每个案例即可。

我希望这能帮助我们创建更多可读的代码！我认为学习我们不熟悉的编程语言中存在的模式和最佳实践是很棒的，因为很多时候这些技术可以应用于一般的编程。

莱恩·瓦格纳

下载 q vault:[https://q vault . io](https://qvault.io/)

订阅我们的 RSS 源:[https://qvault.io/blog/feed/](https://qvault.io/blog/feed/)

星我们的 Github:[https://github.com/q-vault/qvault](https://github.com/q-vault/qvault)

帖子[守护子句:如何清理条件句](https://qvault.io/2019/08/16/guard-clauses-how-to-clean-up-conditionals/)最早出现在 [Qvault](https://qvault.io) 上。