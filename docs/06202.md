# “var”和“let”关键词的区别？

> 原文：<https://dev.to/nunocpnp/differences-between-var-and-let-keywords-2b85>

这篇文章将是一系列小文章的第一篇，这些小文章将涵盖你在求职面试中可能遇到的一些问题。

我会更关注 JavaScript，但将来会有一些关于 HTML、CSS 和 React JS 的帖子！

好了，我们开始吧！

## ***差 nr 1！***

**【var】**从一开始就在 JavaScript 中引入，而**【let】**则在 ES2015/ES6 中引入。

## ***差 n ^ 2！***

**【让】**有一个<u>阻塞范围</u>而**【var】**为一个<u>功能范围</u>

让我们看看这个例子

```
let x = function() {

  if(true) {
    var v = 2;
    let l = 1;
  }

  console.log(v);
  --> 2
  console.log(l);
  --> Uncaught Reference: l is not defined
}

x(); 
```

变量 v 的 console.log 会因为**“var”**关键字的函数作用域而返回 2，而 l 变量会因为**“let”**关键字的块作用域而返回一个错误。

## ***差 n ^ 3！***

用 **"var"** <u>定义的变量在他的函数顶部被提升</u>，而用 **"let"** <u>定义的变量没有被提升</u>。

```
let x = function() {

  if(true) {
    console.log(v);
    --> undefined
    console.log(l);
    --> Uncaught Reference: l is not defined

    var v = 2;
    let l = 1;
  }
}

x(); 
```

在这种情况下，变量 v 将返回 undefined，而 l 变量将返回一个错误，发生这种情况是因为使用 **var** 的声明被<u>提升/提升</u>到它们的函数/局部作用域的顶部(如果在函数内声明)或它们的全局作用域的顶部(如果在函数外声明)，而不管实际声明是在哪里进行的。

*就是这样！没有什么太复杂的，但重要的是要保持这些概念在你的脑海中新鲜。*

*稍后见，了解更多提示！*