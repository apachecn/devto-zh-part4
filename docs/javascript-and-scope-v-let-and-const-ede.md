# JavaScript 和范围 V - let 和 const

> 原文：<https://dev.to/tchaffee/javascript-and-scope-v-let-and-const-ede>

在 [JavaScript 和 Scope IV - Blocks](https://blog.toddbiz.com/blog/2019-06-07-javascript-and-scope-iv/) 中，我们展示了当使用`var`关键字时，块本身不会创建新的作用域。如果有一种方法可以在一个块中创建`toto`变量，使它有自己的作用域，不像下面这样，那不是很好吗？

```
var toto = 'wants';

{
  var toto = 'a puppy';
}

console.log(toto); // logs 'a puppy' 
```

ES6 允许在声明变量时使用关键字`let`的块级作用域。

```
var toto = 'wants';

{
  let toto = 'a puppy';
}

console.log(toto); // logs 'wants' 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/db423zut/)

在上面的代码中，块内的`toto`变量不再污染全局变量的值，因为它们现在是两个不同的变量，即使它们有相同的名称。

我们还可以修复上一篇文章中的`for`循环示例。

```
var buzz = 3;

for (let buzz = 1; buzz < 10; buzz++) {
  if (buzz === 9) { 
    console.log('for loop buzz:', buzz); // logs 9
  }
}

console.log('global scope buzz:', buzz); // now logs 3 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/qr7x2fuk/)

而且我们还可以修复`if`语句代码。这一次我们使用了关键字`const`,因此该值不能更改。

```
var cute = 'not';

if (true) {
  const cute = 'cat';
}

console.log(cute); // logs 'not' 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/yjwt75v4/)

从上面的例子中可以看出，当你需要的时候，`let`和`const`都会给你块级的作用域。事实上，这也是更喜欢用`let`和`const`而不是`var`的原因之一。变量应该有尽可能有限的范围，以避免我们在前一篇文章中看到的意外副作用。