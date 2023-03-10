# 对象制造对象

> 原文：<https://dev.to/davidrpolk/objects-making-objects-46hj>

在这篇文章中，我将提供一些关于 JavaScript 中原型继承的信息。因为它是语言的一个定义性特征，所以理解它是很重要的。这里给出的例子将保持简单，更侧重于整体概念。

# 构造函数

[![](img/f11940cc67d39e84f15433f146f6e3ab.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oCUDxpF6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7dunpj9vqbpx3evucey6.png) 
使用构造函数可能是利用原型继承的最常见方式。在这种情况下，使用构造函数创建对象的新实例，该构造函数将其属性传递给新实例。

这个例子使用了一个伪经典的实例化模式。这不是做同样事情的唯一方法，但是这个概念对于函数和类实例化是相同的。
[![](img/9856cafa19ca8d9b0025a0eb26114c59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZUt6lq0D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rktoobkgpgpwnyhgusk3.png) 
由于继承，如果我们试图访问一个“bobject”不包含的属性或方法，那么查找就会被委托给“bobject”的原型(创建“bobject”的对象)，在这种情况下，就是构造函数“Bob”。如果“Bob”没有我们正在寻找的属性或方法，那么查找将被委托给“Bob”的原型，以此类推...直到这个链以找到我们要找的东西或者以“null”结束(这将使查找失败)。这种委托与实例化模式无关，构造函数也不是利用继承的唯一方式。

# 对象串联

是的，串联不仅仅是字符串和数组！可以使用“Object.assign()”来连接对象。
[![](img/39053db8d6d075c3819cca2a234af2ad.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--ArCCCjbS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jj4ht83m6m9ch31jxjb6.png)

正如您在这个例子中看到的，我们新创建的对象“bobCat”包含创建它的对象的属性和方法。与我们的另一个例子不同，这个例子没有给“bobCat”分配一个“构造函数”(除了全局对象)。

# 闭合

[![](img/01dda0779498ba6802a837548e384da0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tHtPmwYZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1zmmwh87hvywfjcuvpya.png) 
起初可能看起来不明显，但是使用闭包实际上是一种继承形式。由于闭包是由引用其自身范围之外的东西的函数创建的，所以对该引用的任何查找都将委托给包含该函数的对象！
[![](img/492e96574eb1894d1420efed5d694403.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--d9UUzmXs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8pmsk9fagkdm0eptur8t.png)

总之，原型继承是 JavaScript 的一个关键特性，应该得到相应的对待！

while(life . left . length > 0){
life . do = coding()；
}