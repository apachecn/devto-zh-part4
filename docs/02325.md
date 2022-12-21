# ES6: Const & Let

> 原文：<https://dev.to/taorep/es6-const-let-10gl>

[~查看 jayess.lifesandwich.co 的原文~](https://jayess.lifesandwich.co/blog/2019/08/javascript-es6-const-let/)

谁使用 var？希望不是你。ES6 (ES2015)更好的干掉了它。取而代之的是两个新的变量赋值关键字；const & Let——它们的工作原理与 var 略有不同。

* * *

**Const:** 您 99%的时间都是首选。

**Let:** 只在需要的时候使用。

* * *

那么有什么区别呢？为什么它们比 var 好那么多？

答案是 Const & Let 是块范围的，而 var 是函数范围的。

* * *

**块作用域:**仅存在于一对花括号{ //code }之间

**函数作用域:**仅存在于当前函数内。

* * *

这里的主要区别是在 if 语句或循环中使用 const 或 let 命名变量，基本上是将变量保存在内部，而不允许它出现在外部。

所以这个:

*if(true){
var x = 2；
}
console.log(x)*

日志 2，但这:

*if(1 = = = 1){
const y = 2；
}
console.log(y)*

抛出一个错误。

这是因为用 var 创建的 x 不关心 if 语句{ }，而 const(和 let)关心。

这是一件好事，因为这意味着如果您不小心将两个变量命名为相同的名称，您不会得到意外的行为，因为 if/ function/ loop 内的变量将与那些{ }外的变量(命名为完全相同的东西)完全不同。这有助于避免命名冲突。

### **常量 vs Let**

let 和 var 是一样的，除了我们刚刚谈到的块范围。

然而，const 是 constant 的缩写，意味着你不能重新分配它。

所以:

*设 a = 1
a = 2*

很酷。
但是这个:

*常数 b = 1
b = 2*

是不行的

您还必须用一个值初始化 const:

*设 c；
c = 3*

很好
但是:

*const d；
d = 4；*

不是。(第一行会出错)

这很好，因为这意味着您不会意外地重新赋值(尽管您可以通过 push、pop 等方式改变数组和对象)。

## **//重述**

Var 并不是很好，因为它是函数作用域和可重赋值的。理论上没问题。但是它为坏运气(名称空间冲突、意外值)打开了大门

const 和 let 是块范围的，const 也是不可重赋值的，这就是为什么 const 应该一直使用，除非你确实需要重赋值(比如在循环中递增)。

抛弃 var 是一个简单的步骤，可以让你的代码经得起未来的考验，减少错误，你的用户会因此而感谢你。

* * *

[~查看 jayess.lifesandwich.co 的原始帖子~](https://jayess.lifesandwich.co/blog/2019/08/javascript-es6-const-let/)