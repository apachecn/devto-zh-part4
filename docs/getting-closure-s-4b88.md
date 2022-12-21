# 获得结束

> 原文：<https://dev.to/moresaltmorelemon/getting-closure-s-4b88>

# 什么是闭包？

> 从其周围的局部作用域引用绑定的函数称为闭包。[雄辩的 JavaScript](https://eloquentjavascript.net/03_functions.html)

一个简单的定义，但不是一个没有更大的背景下提供理解。

> 闭包是一个持久的作用域，即使在代码执行移出该块之后，它仍然保留局部变量。支持闭包的语言(如 JavaScript、Swift 和 Ruby)将允许您保留对作用域(包括其父作用域)的引用，即使在声明这些变量的块已经执行完之后，只要您在某个地方保留对该块或函数的引用。- [堆栈溢出](https://stackoverflow.com/questions/36636/what-is-a-closure)

一个更长的定义，但仍然没有信息量。

我第一次遇到使用中的闭包时，我坐在那里，不知道刚刚到底发生了什么。就像魔法一样。我不知道它是如何工作的，只知道它是。

这似乎是一种普遍的情绪。

幸运的是，它们其实很简单。它们是解决问题的方法。一旦你发现了问题，你就会认识到解决方案——闭包，因为它们是什么。

但是首先我们需要讨论使闭包成为必要的三个部分。

## 词法范围

在编程语言中，作用域是一组控制变量绑定在哪里被访问的规则。有两种形式，词法和动态。

对于动态作用域，变量绑定与调用函数的位置有关，而对于词法作用域，编写绑定的位置*是关键。* 

```
const x = 5;
const printX = ( ) => console.log('The value of X is: ', x);

const dynamicScope = ( ) => {
  const x = 100;
  printX( ); // uses the x where it was called from
}
dynamicScope( );  //-> The value of X is 100

const lexicalScope = ( ) => {
  const x = 100;
  printX( ); // uses the x where it was written
} 
lexicalScope( );  //-> The value of X is 5 
```

词法范围规则是最常见的范围系统，因为它们易于阅读和调试。您编写的代码将根据您如何定义它来保持一致的行为，而不是根据它在哪里使用。

词法作用域产生一系列嵌套的块，防止块内定义的变量被块外访问。

```
// global scope

const a = 'outer';
const b = 'outer';
const c = 'outer';

{  // block scope
  const b = 'inner';
  const c = 'inner';

  {  // nested block scope
    const c = 'innermost';
    console.log('InnerMost Scope: ', 'a: ', a, 'b: ', b, 'c: ', c);
    //-> InnerMost Scope: a: outer, b: inner, c: innermost
  }
  console.log('Inner Scope: ', 'a: ', a, 'b: ', b, 'c: ', c);
  //-> Inner Scope: a: outer, b: inner, c: inner
}
console.log('Outer Scope', 'a: ', a, 'b: ', b, 'c: ', c);
//-> Outer Scope: a: outer, b: outer, c: outer 
```

当最里面的`console.log`请求`a`、`b`和`c`的值时，它首先在定义它的块中查找。如果它没有找到变量绑定，那么它会在定义它的块周围的块中查找，依此类推，直到它到达全局范围，不能再继续下去。

这意味着每个`console.log`在它被定义的范围内访问变量值，或者更高的*。内部和外部作用域*无法*看到最内部作用域的值。*

 *当我们定义一个函数时，它有自己的块作用域，在函数内部定义的变量不能从函数外部访问。

```
function hasItsOwnScope() {
  const innerScope = 'cannot access outside of function';
}

console.log(innerScope); 
//-> Uncaught ReferenceError: innerScope is not defined 
```

## 执行上下文

下一个难题是执行上下文。每次调用(即执行或调用)一个函数时，该函数都会被添加到调用堆栈中。如果那个函数调用另一个函数，那么*那个*函数被添加到调用堆栈中，在前一个函数的*顶部*处。当一个函数完成时，它被从调用堆栈中移除。

```
function first ( ) {
  function second ( ) {
    function third ( ) {
    }
    third( );
  }
  second( );
}
first( );

// Call stack: [ ]
// Call stack: [first]
// Call stack: [first, second]
// Call stack: [first, second, third]
// Call stack: [first, second]
// Call stack: [first]
// Call stack: [ ] 
```

为了节省内存，当函数从调用堆栈中移除时，函数内部定义的变量将被丢弃。每次你调用一个函数，它是一个干净的石板。其中定义的每个变量，包括参数，都要重新定义。

这些绑定以及仅在函数内部可用的特殊绑定(如`arguments`、`name`和`caller`)存储在执行上下文中，该上下文包含函数访问其内部定义的变量值以及词法范围链上的变量所需的所有信息。

## 第一类&高阶函数

如今，许多语言都允许一级函数，这意味着您可以像对待任何其他值一样对待一个函数。它可以绑定到一个变量定义:

```
const firstClass = function myFirstClassFn( ) { /* ... */ } 
```

它可以作为参数传递给函数，也可以由其他函数返回。当一个函数接受一个函数作为参数，或者返回一个参数时，这个函数被称为高阶函数:

```
function higherOrderFn(firstClassFnParameter) {
  firstClassFnParameter( );

  return function anotherFirstClassFn( ) { /* ... */ }
}

higherOrderFn(firstClass); //-> function anotherFirstClassFn... 
```

# 问题

*   我们不能从函数外部访问函数内部的值
*   函数内部的变量只在函数被调用时存在
*   但是，我们*可以*在另一个函数中定义一个函数，然后*返回*它。

那么，当返回的一级函数试图访问在返回的高阶函数内部定义的值时，会发生什么呢？

```
function higherOrder( ) {
  const insideScope = "cannot be accessed outside";
  return function firstClass( ) {
   console.log(insideScope);
  }
}

const returnedFn = higherOrder( );
returnedFn( );  //-> ??????? 
```

并且*那个*是一个闭包！当另一个函数返回时，闭包保留了该函数的执行上下文。该语言知道您以后可能需要执行上下文，所以它没有丢弃它，而是将它附加到返回的函数中。

稍后当你准备好使用返回的函数时，它能够访问它需要的所有值，就像当它还在返回它的函数内部时你调用它一样。

这是一个非常强大的想法！你现在可以定义私有变量:

```
function higherOrder( ) {
  let privateVariable = 'private';

  return {
    get: () => privateVariable,
    set: (val) => privateVariable = val
  }
}
console.log(privateVariable);
//-> Uncaught ReferenceError: privateVariable is not defined

const getterSetter = higherOrder( );
getterSetter.get( );  //-> 'private';
getterSetter.set('new value');
getterSetter.get( );  //-> 'new value' 
```

还可以组合函数！

```
const log = function (message) {
  return function (val) {
    console.log(message, val);
  }
}

const logWarning = log('Warning! We encountered an issue at: ');
const logError = log('Error: ');

logWarning('ChatBot message delivery');
logWarning('PostIt note stickyness');

logError('Connection lost'); 
```

虽然这是一个简单的例子，但扩展它的能力是不可思议的。函数现在是有状态的。由另一个函数返回的函数保留了它的高阶函数的记忆，你可以用它来组合函数，就像乐高积木一样。*