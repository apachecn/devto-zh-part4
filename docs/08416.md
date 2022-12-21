# 副作用和组成

> 原文：<https://dev.to/s_anastasov/side-effects-and-composition-153o>

*照片由 [Ryan Yoo](https://unsplash.com/@ryan_yoo) 在[Unsplash](https://unsplash.com/search/photos/lego)T5 上拍摄*

函数式编程是惊人的。它限制了你能做的事情(没有空值，没有异常，没有副作用…)，作为回报，你得到了一些好处。FP 的一些好处很容易解释，而另一些就不那么容易了。我玩 FP 已经一年多了，就在几周前，当我开始阅读 Scala 中的[函数式编程时，我发现了一个惊人的例子纯函数的好处。](https://www.manning.com/books/functional-programming-in-scala)

## 问题

为咖啡店建立一个软件。对于初始 MVP，要求是:

*   卖咖啡
*   用卡支付

所以代码的第一稿看起来是这样的:

```
import coffee.*

fun buyCoffee(cc: CreditCard): Coffee {
    val cup = Coffee()
    cc.charge(cup.price)
    return cup
} 
```

`buyCoffee`函数接收一个`CreditCard`作为输入，并返回一个`Coffee`作为输出。它创建一个`Coffee`对象，然后执行一次充电并返回`Coffee`对象。

## 可测性

调用`cc.charge`使用 SDK/API 与信用卡公司对话。它涉及授权、网络调用、在数据库中保存记录。`buyCoffee`函数返回一个`Coffee`，充电发生在侧面，因此称为“副作用”。

副作用使得`buyCoffee`很难单独测试。不需要和信用卡公司交谈，也不需要实际收费，就可以进行测试，这将会很好(也更便宜)。人们也可以争辩说，`CreditCard`不应该知道它是如何被充电的。

可测试性问题可以通过依赖注入来解决。

```
fun buyCoffee(cc: CreditCard, p: Payments): Coffee {
    val cup = Coffee()
    p.charge(cc, cup.price)
    return cup
} 
```

向信用卡收费的逻辑被提取到`Payments`对象中。在生产代码中，使用真正的`Payments`实现，并为测试注入一个模拟版本。

## 新要求

几周后，新的需求出现了。有些人会买不止一杯咖啡，因此软件的下一个版本有额外的要求:

*   买 N 杯咖啡
*   每张信用卡一次收费

## 作文

买一杯咖啡的问题已经用`buyCoffee`解决了。如果能重用一杯咖啡的代码来购买多杯咖啡，那就太好了。代码很复杂(可能是)，经过测试和调试。重用现有的解决方案也可以节省时间。

## 购买多份咖啡

`buyCoffees`的初稿是这样的:

```
fun buyCoffees(
    cc: CreditCard,
    p: Payments,
    n: Int
): List<Coffee> = List(n) { buyCoffee(cc, p) } 
```

它需要一个额外的参数(与`buyCoffee`相比)，要购买的咖啡数量。它像预期的那样返回一个`List<Coffee>`。它使用`buyCoffee`用`n`咖啡填充列表。

不幸的是，这个代码多次从信用卡上扣费。这对客户和企业都不利(每笔交易都要花钱)。

问题还是在于副作用。

```
p.charge(cc, cup.price) // <-- Side effect 
```

## 副作用和参照透明度

我多次提到*副作用*这个词。是时候解释一下是什么了。副作用是破坏参照透明性(RT)的东西。这就引出了一个问题:什么是*参照透明性*？

> 如果一个表达式可以在不改变程序行为的情况下被相应的值替换，那么这个表达式被称为引用透明的。——[维基百科](https://en.wikipedia.org/wiki/Referential_transparency)

### RT 举例

给定这个版本的`buyCoffee` :

```
fun buyCoffee(cc: CreditCard, p: Payments): Coffee {
    val cup = Coffee()
    p.charge(cc, cup.price)
    return cup
} 
```

RT 意味着`coffeeA`可以用它的结果 a `Coffee`代替，而不改变程序。

```
val coffeeA: Coffee = buyCoffee(cc, p)  // returns Coffee()

val coffeeB: Coffee = Coffee() 
```

`coffeeA`和`coffeeB`不等价。用`coffeeA`你的信用卡付费，用`coffeeB`你可以得到一杯免费咖啡。这意味着`buyCoffee`是不透明的。

## 解决问题

我可以把问题推给支付处理器。我可以创建`BatchPaymentsProcessor`，在执行之前对付款进行批处理。这就引出了这样的问题:

*   它要等多久
*   它批量处理多少笔付款
*   `buyCoffee`是否负责指示批次的开始/结束

解决了代码重用问题，但也带来了一系列新问题。我们能做得更好吗？

## 去除副作用

副作用是阻止合成。如果我把它拿掉，也许我能恢复创作。

```
fun buyCoffee(cc: CreditCard): Pair<Coffee, Charge> {
    val cup = Coffee()
    val charge = Charge(cc, cup.price)
    return Pair(cup, charge)
} 
```

现在副作用没了，`Payments`对象也没了。返回类型更改为`Pair<Coffee, Charge>`。除了`Coffee`之外，还返回了一个`Charge`对象。`Charge`对象是一个指示应该收费的纯值(数据类)。`Charge`还包含执行它所需的信息，如抄送号码和价格。

`buyCoffee`函数只负责创建纯粹的`Charge`(没有副作用)。执行它的责任在程序的另一部分。

## 合并收费

因为`Charge`是一个常规值，所以可以很容易地合并。

```
fun combine(c1: Charge, c2: Charge): Charge =
    if (c1.cc == c2.cc) Charge(c1.cc, c1.price + c2.price)
    else throw IllegalArgumentException(
        "Can't combine charges with different cc"
    ) 
```

两项费用，假设他们有相同的信用卡，相加他们的价格。

## 重访购买多种咖啡

我现在可以用纯的`buyCoffee`来实现`buyCoffees`函数，就像这样:

```
fun buyCoffees(
    cc: CreditCard,
    n: Int
): Pair<List<Coffee>, Charge> {
    val purchases: List<Pair<Coffee, Charge>> =
        List(n) { buyCoffee(cc) }
    val (coffees, charges) = purchases.unzip()
    val charge = charges.reduce { c1, c2 -> combine(c1, c2) }
    return Pair(coffees, charge)
} 
```

返回类型是`Pair<List<Coffee, Charge>>`,包含购买咖啡的清单和所有咖啡的单一费用。为了创建这个，`buyCoffee`函数用于填充一个带有`n`咖啡项目的列表。`unzip`功能用于将咖啡与装料分开。使用上面定义的`combine`功能合并费用。

再次，执行购买的关注被推到程序的另一部分。

获得的好处是:

*   更好的可测试性——功能无需模仿即可测试
*   组合——较大的功能是根据较小的功能实现的
*   关注点分离——购买决策与购买执行是分离的

## 结论

通过做一件简单的事情，比如将产生副作用的决策和执行它分开，代码就更具可组合性和可重用性。这也提高了可测试性，因为纯函数可以在不使用模拟的情况下进行测试。意识到副作用影响组合的方式有助于编写可组合的代码。