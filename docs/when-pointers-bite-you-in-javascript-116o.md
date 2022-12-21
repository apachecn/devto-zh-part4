# Javascript 里指针咬你的时候。

> 原文：<https://dev.to/corvusetiam/when-pointers-bite-you-in-javascript-116o>

那会很短很快。如果你已经有了**任何**低级语言的背景，那么就没有什么新东西了。尽管如此，我还是想听听你的意见。
如果你还认为编程中的指针是:

a)屏幕上的白色箭头
b)怪异的黑魔法

别担心。有了 webdev，我们可以远离机器，进入完美的(几乎是)网络浏览器世界。

有时这些抽象泄漏，或者更好地说，显示了幕后发生的奇怪事情。坦白说，那些东西一点都不奇怪。机器就是这样制造的，记忆也是这样工作的。

好吧，首先。什么是指针？

> 指针可以理解为间接。不同对象/变量/函数等的地址。地址本身并不存储数据*，但通过地址我们可以访问原始值。

我为什么要关心它？

让我从简单的例子开始。

```
let arr = [1, 2, 3, 4, 5];
let o = { something: arr }

o['something'][0] = 4;

console.log("%o", o);
console.log("%o", arr); 
```

刚刚发生了什么？
为什么分配工作如此怪异？或者说，谁在乎呢。事情就是这样的，多。

因为每当你处理复杂的数据结构时，它都是非常重要的，我不是在谈论一些复杂的树。我说的是`Date`、`Array`、`Object`。任何不是原始类型的东西。

为什么原语可以工作？因为，它们复制起来很便宜。对象不是，您必须强制 JS 为您制作副本，因为它在引用(也称为。你东西的别名。

就像指针一样，它们不是值本身，而是指向你的原始变量。JS 不会在语法上显示它们，它们总是开着的。如果你的代码看起来像这样，它们会非常有用:

```
class A {
   constructor(config) {
    this.options = config.a;
   }
}

class B {
   constructor(config) {
    this.options = config.b;
  }
}

var config = { a : { 'name' : 'X' }, 'b' : { 'name' : 'Y' } } 
```

改变你的`config`，它也会改变`new A(config).options`。

参考只是一个别名。只有一个关键的区别。不能给这些引用分配新值。

等等，什么？

```
let arr = [1,2,3,4];
function a(arr /* reference to Array object */) {
    arr = "hello"; /// it becomes local variable arr. Original is not changed 
}
function b(arr /* reference to Array object */) {
    arr[1] = "hello"; /// it changes inner element
}

console.log(arr); 
```

猜猜看，执行`a(arr)`和`b(arr)`后 arr 里面会是什么。

请记住，在 JS 中，默认情况下传递别名或引用，而不是副本。只有布尔、数字和字符串这样的原语会被复制。

别名和引擎盖下的指针没什么不同。更安全，功能更少，更容易使用-是的。它仍然只是其他值指针的别名。

再见！