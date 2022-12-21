# JavaScript 解码的 var、let 和 const...

> 原文：<https://dev.to/gemhar/var-let-and-const-decoded-30k2>

当我掌握 JavaScript 的基本原理时，我遇到了三种声明变量的方法，即通过 *var* 、 *let* 和 *const* 语句。所以在这篇文章中，我试图总结我所有的发现，来区分每一个声明。

要真正理解 *var* 、 *let* 和 *const* 的区别，我们必须理解以下四个概念:

*   变量声明
*   变量初始化
*   范围
*   提升

## 变量声明

变量声明是在程序中引入一个新标识符的过程；具体到我们的作用域(我稍后会谈到作用域)。在 JavaScript 中，当用 *var* 关键字声明时，标识符默认被赋予一个 undefined 值(这是由解释器自动完成的)。

```
var foo; // declaration
console.log(foo); // logs-->undefined 
```

Enter fullscreen mode Exit fullscreen mode

## 变量初始化

变量初始化是最初给标识符赋值的过程，所以当我们用 *var* 关键字声明一个绑定时，解释器自动将其初始化为 undefined。

```
var foo; //declaration
console.log(foo); // logs -->undefined

foo = "something"; // Initialization
console.log(foo); // logs -->something 
```

Enter fullscreen mode Exit fullscreen mode

## 范围

变量的作用域实际上定义了变量和函数在程序中可访问和可引用的上下文。范围定义了变量和参数的可见性和生存期。如果一个变量不在“当前范围内”，那么它就不可用。作用域也可以在层次结构中分层，以便子作用域可以访问父作用域，但反之则不行。

基本上，有两种类型的范围界定

*   功能范围
*   块范围

### 功能范围:

在函数内部声明的变量的作用域是该函数和所有后续的嵌套函数；不考虑区块；

```
function foo() {

  if(true) {
    var v = "var inside the if block";
    console.log(v);  //logs -->var inside the if block
  } 
  v = "var outside the if block";
  console.log(v);  //logs -->var outside the if block
}

foo(); 
```

Enter fullscreen mode Exit fullscreen mode

### 封锁范围

在块内声明的变量的作用域仅限于该块和所有后续嵌套块，而不是该块之外，甚至不是在同一个函数中；此处的块包括 if...else 块或循环块。

```
function bar() {

  if(true) {
    let l = "let inside the if block";
    console.log(l);  //logs -->let inside the if block
  }

console.log(l); // Uncaught Reference Error: l is not defined
}

bar(); 
```

Enter fullscreen mode Exit fullscreen mode

## 吊装:

MDN 将吊装定义为:

> “提升是一个你在 ES2015 语言规范之前的任何规范性规范中都找不到的术语。提升被认为是一种思考执行上下文(特别是创建和执行阶段)在 JavaScript 中如何工作的一般方式。然而，这个概念一开始可能会有点混乱。
> 
> 例如，从概念上讲，提升的严格定义表明变量和函数声明被物理地移到了代码的顶部，但事实上并不是这样。相反，变量和函数声明在编译阶段被放入内存中，但停留在您在代码中键入它们的位置。”

```
console.log(foo); //logs -->undefined 

//it does not throw an error but logs -->undefined;
//this happens because of hoisting

var foo = "something"; //Initialization
console.log(foo); //logs -->something 
```

Enter fullscreen mode Exit fullscreen mode

对于上面的代码，JS 解释器如何求值可以简化为:

```
var foo; // Hoisted declaration of 'foo'

console.log(foo); logs -->undefined;
foo = "something";
console.log(foo); //logs -->something 
```

Enter fullscreen mode Exit fullscreen mode

## [t1**存在于**](#var)

**var** 语句声明一个变量，可选地将其初始化为一个值。任何用 *var* 语句声明的变量都是函数作用域的，并且用 *var* 关键字声明的标识符都用未定义的
进行提升和初始化

```
console.log(foo); //logs -->undefined
var foo;

//the above code does not throw an error because of hoisting; 
```

Enter fullscreen mode Exit fullscreen mode

## **让**

**让** 语句声明一个局部变量。任何用 *let* 语句声明的变量都是块范围的。用 *let* 关键字声明的标识符被提升并且不被初始化

```
let foo;
console.log(foo); // Uncaught Reference Error: l is not defined

//the above code throws an error because identifiers declared with let keywords are not initialized; 
```

Enter fullscreen mode Exit fullscreen mode

*let* 绑定创建在包含声明的(块)作用域的顶部，通常称为“提升”。与用 var 声明的变量不同，它将从值 undefined 开始，let 变量直到它们的定义被求值后才被初始化。在初始化之前访问变量会导致引用错误。

## **常数**

*const* 语句声明了一个局部变量，非常类似于 *let* 语句，但它增加了一个属性，即:它们不能被重新分配；这意味着一旦*常量*绑定被初始化，它就不能被重新分配任何其他值。

由于上述原因，一个 *const* 绑定在声明时总是必须被初始化，否则它会抛出一个错误。

```
const foo = "something";
foo = "other thing"; // Uncaught TypeError: Assignment to constant variable. 

const bar; //Uncaught SyntaxError: Missing initializer in const declaration 
```

Enter fullscreen mode Exit fullscreen mode

**注:**
这里要观察的一点是，当我们用 *const* 绑定一个对象时，这个对象本身不能改变，会继续指向同一个对象，那个对象的内容可能会改变。

```
const score = {visitors: 0, home: 0};

score.visitors = 1; // This is okay 
score = {visitors: 1, home: 1}; // Uncaught TypeError: Assignment to constant variable.
// This isn't allowed 
```

Enter fullscreen mode Exit fullscreen mode

## 最后一个有趣的事实:

在没有声明关键字的函数中声明的绑定将成为全局变量。我举个例子来解释一下:

```
function funFact() {
  isGloballyAvailable = true;
}

funFact();
console.log(isGloballyAvailable); // logs true 
```

Enter fullscreen mode Exit fullscreen mode

为了理解这一点，我们必须回到我们的提升概念，通常发生的情况是，每当我们在代码中初始化一个变量时，解释器就去搜索提升的变量，然后分配或重新分配变量的值，但是当解释器在函数中找不到变量时，它就去搜索它的父函数的提升变量，这个过程一直重复到全局范围；

在我们的例子中，解释器即使在全局范围内也找不到我们的“isGloballyAvailable”绑定，所以解释器自动将变量添加到全局范围内。

这是一个极其危险的过程，必须不惜一切代价避免；所以请记住，我们不能在代码中的任何地方声明没有关键字的绑定: *var* 、 *let* 或 *const* 。

## **那么什么时候应该用 *var* ， *let* 或者 *const* ？**

ES2015 (ES6)引入了 *let* 和 *const* ，为什么 JavaScript 设计师会引入它们？也许是为了修正一些关于*变量*的问题，或者是为了更好的可读性...对吗？

*var* 的一个主要问题是它允许在代码中重新声明，这不会抛出错误，而错误会在代码中产生意想不到的副作用。

### 这个流行和我一样的看法是:

如果分配给我们的变量的值不会改变，我们应该总是倾向于使用 *const* ，这告诉未来的开发者标识符有一个常量值。
另一方面，如果稍后标识符需要改变它的值，就使用 *let* ，但是我看不出我们应该使用 *var* 的用例。