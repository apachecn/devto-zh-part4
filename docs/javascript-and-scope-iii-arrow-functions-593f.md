# JavaScript 和范围 III -箭头函数

> 原文：<https://dev.to/tchaffee/javascript-and-scope-iii-arrow-functions-593f>

正如你在 [JavaScript 和作用域 I - Intro](https://blog.toddbiz.com/blog/2019-06-01-javascript-and-scope/) 和 [JavaScript 和作用域 II - Functions](https://blog.toddbiz.com/blog/2019-06-03-javascript-and-scope-ii/) 中看到的，函数创建了一个新的作用域，它将隐藏或遮蔽更高作用域中变量的值，比如全局作用域。

箭头函数呢？让我们回到我们的第一个代码示例，将它改为一个箭头函数。

```
var bar = () => {  var foo = 2;
  console.log(foo); // logs 2
}

bar();

// Edit and run the code if you don't remember
// what the next line results in.
console.log(foo); 
```

[在 js dild 中编辑](https://jsfiddle.net/tchaffee/xqtwu138/)

如果您运行上面的代码，您会看到 scope 的行为与常规的函数声明相同:`foo`变量只存在于函数范围内，而不存在于全局范围内。

到目前为止给出的所有其他例子也是如此。如果您想自己看看，并且有几分钟时间，可以继续编辑前两篇文章中的每个代码示例，并将它们改为只使用箭头函数。使用上面的例子作为如何将常规函数声明更改为箭头函数的指南。在每种情况下，您都需要声明一个变量来为 arrow 函数命名。这里是上两篇文章中的所有例子。

[例 2，在 js dild 中编辑](https://jsfiddle.net/tchaffee/0oy9jc1n/2/)

[例 3，在 js dild 中编辑](https://jsfiddle.net/tchaffee/0jwyg6za/)

[例 4，在 js dild 中编辑](https://jsfiddle.net/tchaffee/bue5zf9w/)

[例 5，在 jsdild 中编辑](https://jsfiddle.net/tchaffee/x3f1Ln8e/)

范围在箭头函数中的工作方式有几个例外。箭头函数作用域不包括自己的`this`或自己的`argument`对象，这将在另一篇文章中介绍。现在我们关注的是声明变量的范围。

总之，你可以指望 arrow 函数对声明的变量有自己的作用域，就像常规的函数声明一样。