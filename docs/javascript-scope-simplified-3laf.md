# 范围

> 原文：<https://dev.to/azrulaziz/javascript-scope-simplified-3laf>

范围和其他一些东西，我总是忘记它是关于什么的，所以我决定写下来，希望它能留在我的脑海里。

## **作用域单行定义:**

作用域是一种规则，它决定了在哪里可以找到变量以及如何使用变量。

### 范围&词法环境

词汇环境在决定功能范围及其外部环境方面起着重要作用。词汇环境基本上意味着代码物理上所处的位置。它是关于代码写在哪里以及围绕它的是什么。

考虑一下这个:

```
function a() {
    let num = 1;
    b()
}

function b() {
    console.log(num) // 0
}

let num = 0;
a() 
```

Enter fullscreen mode Exit fullscreen mode

调用 *b* 会发生什么？它将记录 0 作为*号*的值。这是因为 *b* 位于全球范围内。它没有任何其他外部环境。所以它直接去全局寻找一个不存在于自己作用域内的变量。

函数的外部环境取决于函数在词汇上的位置，换句话说，函数在物理上的编写位置。

让我们看一个例子:

```
function a() {
    let name = 'lily';

    function b() {
        console.log(name) // lily
    }

    b()
}

let name = 'zahra';
a() 
```

Enter fullscreen mode Exit fullscreen mode

这里 *b* 物理上位于 *a* 内部。当 *b* 尝试记录变量*名称*时，它没有在自己的作用域中找到它，所以它将引用它的词法外部环境 *a* 。在那里它会找到名为的变量*并使用它。*

##### **范围&执行上下文**

考虑作用域的另一种方式是查看哪个执行上下文创建或初始化了函数。在上面的代码中， *b* 在 *a* 的执行上下文开始之前是不存在的。相比之下， *a* 是在全局执行上下文中创建的。执行上下文决定了一个函数是在哪里创建的，因此它自动成为该函数的外部环境。

### 封锁范围

Block 作用域仅在使用 let 或 const 声明变量时适用。每当在一对花括号内声明一个变量时，都会为该变量创建一个新的作用域。

```
let num = 2;
if (true) {
    let num = 1;
    console.log(num) // 1
}
console.log(num) // 2 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，定义了两个同名的变量，但是两个变量的值不同。只有当我们使用 let 或 const 时，块范围才有可能。如果我们使用 var，第一个变量将被覆盖:

```
var num = 2;
if (true) {
    var num = 1;
    console.log(num) // 1
}
console.log(num) // 1 
```

Enter fullscreen mode Exit fullscreen mode

本质上，变量可以在全局范围、函数范围或块范围内。这完全是由它在哪里被创造或者它的物理位置决定的。