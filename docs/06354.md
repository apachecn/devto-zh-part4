# 揭开闭包的神秘面纱

> 原文：<https://dev.to/stereobooster/demystify-closures-5g42>

在上一篇文章中，我们实现了函数，但没有实现闭包。让我们解决这个问题。

## 问题

如果没有闭包，下面的代码片段不会像预期的那样工作:

```
> (define getFun
    (function (x y)
      (function (i j)
        (- (+ x y) (+ i j))
      )
    )
  )
> (define fun (getFun 5 4))
> (fun 3 2) 
```

这将导致一个错误(`Can't find "y" variable...`)，但是我们希望它返回`4`。

闭包可以解决这个问题，因为闭包是一个附加了环境的函数，例如`(function (i j) ...`可以访问父函数`(function (x y)`的局部变量。

这种变量解析(嵌套作用域)被称为词法作用域。

## 解

解决方案是将环境(创建函数时的环境)与函数一起存储。环境带来的功能叫做封闭。

> 闭包是具有代码和数据组件的数据结构。
> 
> - [闭包转换:如何编译 lambda](http://matt.might.net/articles/closure-conversion/)

现在，我们将一个函数存储为一个包含 3 项的列表:符号“函数”，参数列表，函数体。让我们将环境存储为第四项:

```
const evaluate = (ast, environment = { ...defaultEnvironment }) => {
  // ...
  const [name, first, second] = ast;
  const numberOfArguments = ast.length - 1;
  if (name === "define") {
    // ...
  } else if (name === "function") {
    return [name, first, second, environment];
  } else {
    // ...
  }
}; 
```

当我们调用函数时，我们需要使用闭包的环境和“全局”环境:

```
const evaluate = (ast, environment = { ...defaultEnvironment }) => {
  // ...
  if (name === "define") {
    // ...
  } else {
    // ...
    if (isFunction(environment[name])) {
      const [_, argumentNames, functionBody, closureEnvironment] = environment[
        name
      ];
      // create new environment from "global" and closure's environment
      const functionEnvironment = { ...environment, ...closureEnvironment };
      // add arguments to environment
      // ...
      return evaluate(functionBody, functionEnvironment);
    }
    throw new RuntimeError(`"${name}" is not a function`);
  }
}; 
```

这就是闭包背后的全部秘密。注意:建议的环境存储实施不是最有效的，因为我们将有大量的环境拷贝。

## 局部范围

在函数中使用`define`会带来什么？函数有它的局部作用域，在这个作用域中定义的所有变量都将留在这个作用域中(这里插入一个关于拉斯维加斯的笑话)。

```
> (define testLocal
    (function (x)
      (define local x)
    )
  )
> (testLocal 10)
> (+ local 1) 
```

这段代码将产生错误`Can't find "local" variable...`。

## 封装

封装(一个在面向对象编程中经常使用的术语)——是提供隔离的方法，例如，防止不希望的数据改变，或者隐藏实现细节。

闭包也提供封装。闭包携带了环境的一部分，它可以包含其他人无法读取的变量(如果闭包是在另一个函数内部创建的)。从这个意义上说，闭包提供了一种隔离数据的方法。

## PS

这个帖子的代码是[这里是](https://github.com/stereobooster/write-a-language/tree/master/6.closure)。在下一篇文章中可能会谈到评估策略。