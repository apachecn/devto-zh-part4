# VAR、LET 和 CONST-提升和范围

> 原文：<https://dev.to/hyper_debugger/var-let-and-const-hoisting-and-scope-3jgp>

***JavaScript*** ，像很多更现代的语言一样，为变量声明提供了大量不同的方法。JavaScript 具有用于变量声明的 ***var*** 、 ***let*** 和 ***const*** 关键字，并且每个关键字都有不同的用例。本文旨在深入探讨在使用这些变量声明方法时可能会遇到的注意事项和细微差别。

关于更多的上下文，请参见[官方 ECMAScript 规范](https://www.ecma-international.org/ecma-262/6.0)和 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript) 中定义的每个关键字的定义。

> **Var** 关键字声明一个变量，该变量的作用域是其当前执行上下文，可以选择初始化为一个值。
> **让**关键字声明一个块范围的变量，可选地将其初始化为一个值。
> **Const** 关键字声明了块范围的常量，很像使用 **let** 定义的变量，但是常量的值不能改变。 **const** 声明创建一个值的只读引用。

## 吊装

从概念上讲，提升意味着将变量和函数声明移到代码的顶部。从技术上来说，所发生的是变量和函数声明在编译阶段被放入内存中，但停留在您在代码中键入它们的地方。提升的主要重要性在于，它允许您在代码中声明函数之前使用它们。

提升定义中的关键点是

*   被移动的是变量和函数声明。变量赋值或初始化从不围绕移动。

*   ***声明并没有完全移动到代码的顶部；相反，它们被放进了记忆*。**

在 JavaScript 中，所有用 ***var*** 关键字定义的变量都有一个初始值 ***undefined*** 。这是由于提升将变量声明放入内存并用 ***未定义*** 的值初始化它们。这种行为可以用下面的例子来说明

[![line 2 throws a ReferenceError: y is not defined](img/1a27fcd141de78adf04f12c08af0ba21.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--5R3Jsycd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ALYZsYfSi_pNpUCHDo0z9lg.png) *第 2 行抛出一个 ReferenceError: y 未定义*

但是，用 ***let*** 或 ***const*** 关键字定义的变量在提升时，并没有用 ***undefined*** 的值进行初始化。相反，它们处于被称为**时间死区**的状态，并且直到它们的定义被评估时才被初始化。

[![line 1 throws ReferenceError because it is still in Temporal Dead Zone](img/78573ddbcee4b8504ffb52b896584e78.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--p_S9GiXv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A6_UuTjFttH5H_iuKttyeOg.png) *第 1 行抛出 ReferenceError，因为它仍处于时态死区*

接下来的代码片段演示了 ***let*** 和 ***const*** 变量的提升。

[![line 3 throws a ReferenceError because x in line 4 has been hoisted within the block](img/c6064ccc2a3282f96102483b68d1d2a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--k7rK1dF1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ADpEAcywKXt-x_9rnueuwwQ.png)

用 ***定义的 ***x*** 变量让*** 关键字在块中被提升并优先于用 ***var 定义的 ***x*** 变量。*** 但是在***console . log(x)***中被引用时仍然处于时间死区，因此抛出引用错误。

## 范围

用 ***var*** 关键字定义的变量的作用域是它当前的执行上下文。它们不是块范围的，所以可以从定义它们的块外部访问，只要它仍然在其执行上下文的范围内。 ***Let*** 和 ***const*** 然而，变量是块范围的，不能从块外访问。这个概念如下所示

[![some variables are referenced outside of their scope](img/aadc705ae3c2807ca066e371f77dedbd.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--loSCZLBr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A8BZU5l9Nq5Gs4DIKi0LFsw.png) *一些变量在其作用域之外被引用*

此外，当您用 ***var*** 关键字声明一个全局变量时，该变量会附加到全局上下文中(浏览器中的**窗口**和节点中的**全局**)。用 ***let*** 和 ***const*** 声明的全局变量就不是这样。

## 明白了

*   当你没有声明一个变量而是给这个变量赋值时，这个变量被创建并附加到全局执行上下文中(浏览器中的 ***窗口*** ，节点中的 ***全局*** )。但是，强烈建议不要这样做，因为这会使调试变得非常困难。

[![the variables get attached to the global context](img/6be5f58a8888b1e077f50cdce6d769c0.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--8tj9vVjn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A0QTwonrSzclzRSU8J5W7YQ.png) *变量依附于全局语境*

*   用 ***var*** 关键字声明的变量可以在代码中的任何地方重新声明，即使是在同一个执行上下文中。对于用 ***let*** 和 ***const*** 关键字定义的变量，情况并非如此，因为它们只能在其词法范围内声明一次。

[![cannot redeclare let and const variables.](img/a93a9f6a719921022ab5d857586643a4.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--wUDFF4Z9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AOAzGqNuoFE6aO12c8LpKsw.png) *不能重新声明 let 和 const 变量。*

如果您使用 ***let*** 或 ***const*** 在多种切换情况下声明一个变量，这尤其会引起问题。

[![foo in both cases reside in the same block scope](img/d37edb9581be38c0d1676c7eeda0c754.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s---AWhU1XH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ABS3MFChsa1IvKtX_0AQ4fQ.png) * foo 在两种情况下都驻留在同一个块范围内*

当然，可以通过在案例周围使用花括号来定义不同的块来避免这种情况，但这可能需要重构。

[![foo in both cases reside in different block scopes](img/d791665980f0dbfea25c7403b0caa4ac.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--I_tt75_Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2Asrw2q2qH8ir8iwgOJy1YAw.png) *两种情况下的 foo 驻留在不同的块作用域*

*   另一个需要记住的关于常量的问题是，即使它的值不能被重新赋值，它仍然是可变的。如果值是一个对象，那么对象的属性可以被修改。

[![variables defined with const are still mutable.](img/517d89a4e261b4d1b3151779d5284a29.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Haeh7xVf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A5GcP_6Bf3fzBBGcOcS9hxQ.png) *用 const 定义的变量仍然是可变的。*

是的，这基本上总结了使用任一变量声明方法的定义和问题。