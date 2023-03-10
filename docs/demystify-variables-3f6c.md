# 揭开变量的神秘面纱

> 原文：<https://dev.to/stereobooster/demystify-variables-3f6c>

目前我们的简单编程语言(PL)有两种运算(`+`、`-`)、两种类型(`number`、`symbol`——在这一点上一切都不是数字，例如`a`、`b`、`+`、`-`等。).我们要添加的下一个特性是“可变”。

## 什么是变量？

这个问题有很多方面。所以让我们从变量可以是什么这个最简单的角度开始。

变量是临时存储。想象一下，如果你需要计算一些复杂的公式，你会分小部分做，你会写下中间的结果或记住它(如果你的记忆力足够好的话)。变量允许我们存储一些计算的中间结果。一般来说，它允许存储任何信息，可以是用户输入或一些预定义的常量值等。

变量有一个名字(或标识符)，我们用它来区分它们。在我们的语言中，我们将使用符号来表示变量。我们的 PL 中已经有了符号，现在，我们用它们来表示操作。我们也将扩展符号的用法来表示变量。

## 我们省略了什么？

我说过这是变量实现的简化版本，所以我们省略了一些主题，例如，

*   局部作用域与全局作用域(我们现在使用全局作用域)
*   按值调用与按名称调用
*   重新分配，引用透明(我们现在不允许重新分配)
*   引用，指针

## 它将如何工作？

我们需要实现两个操作:

*   写-保存该值
*   read -获取值

write、read 单词的用法将来可能会让你感到困惑，因为我们将讨论输入输出(IO)和向 IO 写入，从 IO 读取是一件有点不同的事情。我们可以称之为“写”——在一个动作中进行内存分配和赋值。

Write 将被实现为一个接受两个值的操作——变量名和值。对于这个操作，我们将使用单词`define`。在我们的例子中，它看起来像这样:

```
(define x 1) 
```

在其他语言中，它可能是这样的，例如:

```
x = 1
x := 1
const x = 1 
```

读取器只接受一个值——名称。为了简单起见，我们将不使用任何显式操作符来读取，而是每当我们遇到符号时，除了一些特殊情况，我们将用一个值来代替它(先前存储的)。这样它将类似于数学符号:

```
(+ x 1) 
```

## 实现

我们说过，我们把变量存储在一些存储器中，也就是内存中。如果你读过一些关于如何实现编译器的书，你可能还会知道它的其他名字:stack(保存在堆栈中)，heap(保存在堆中)。

在 PL 的上下文中，我们将其称为**环境**。每一个操作(或程序)的执行都是在一定的环境中进行的。

我们需要一些能够存储数据对的数据结构。最简单的解决方案可能是存储一个配对列表:

```
[["x", 1], ["y", 2] ...] 
```

但是使用起来并不舒服——我们每次都需要遍历列表来比较我们想要找到的名字和对中的名字。

如果我们能使用他们在字典中使用的相同原则就好了——你把这个词用作“地址”，并基于它获得关于这个词的解释。

有这样一个数据结构。它被称为字典或哈希表。这是一个关于如何实现它的单独帖子的主题。现在，让我们看看它在 JS:
中是如何工作的

```
const environment = {};
// define
environment["x"] = 1;
// read
environment["x"]; 
```

让我们把`define`加到`evaluate`上:

```
const environment = {};
const evaluate = ast => {
  let [name, first, second] = ast;
  const numberOfArguments = ast.length - 1;
  if (name === "+") {
    // ...
  } else if (name === "-") {
    // ...
  } else if (name === "define") {
    return (environment[first] = second);
  } else {
    throw new RuntimeError(`"${name}" is not a function`);
  }
}; 
```

让我们把“operartion”加读作:

```
const environment = {};
const evaluate = ast => {
  if (typeof ast === "string") {
    return environment[ast];
  } else if (typeof ast === "number") {
    return ast;
  }
  let [name, first, second] = ast;
  const numberOfArguments = ast.length - 1;
  if (name === "+") {
    return evaluate(first) + evaluate(second);
  } else if (name === "-") {
    // ...
  } else if (name === "define") {
    // ...
  } else {
    throw new RuntimeError(`"${name}" is not a function`);
  }
}; 
```

此时我们应该能做到:

```
> (define x 1)
1
> (+ x 1)
2 
```

基本的实现已经就绪，但是还有更多...

## 告诫

### 评估值

我们应该能做到:

```
(define x (+ 1 1)) 
```

为了支持这一点，我们需要将`evaluate`添加到第二个参数:

```
if (name === "define") {
  return (environment[first] = evaluate(second));
} 
```

*注*:想想为什么我们`evaluate`第二个论证，而不是第一个。

### 仅符号

如果我们这么做了会发生什么:

```
(define 1 2) 
```

这说不通。为了防止这种情况，我们需要检查第一个参数是否是一个符号，如果不是，就返回一个错误:

```
if (name === "define") {
  if (typeof first !== "string") {
    throw new TypeError(
      `"${name}" expects symbol as the first argument, instead got "${first}"`
    );
  }
  return (environment[first] = evaluate(second));
} 
```

### 重新分配

如果我们这么做了会发生什么:

```
> (define x 1)
> (define x 2) 
```

对此有两种可能的应对方式:

*   允许重新分配，所以最终的值是`2`
*   使用`define`生成的变量表现为常数，例如，我们不能改变第一次定义后的值。

我选择了第二个选项:

```
if (name === "define") {
  if (typeof first !== "string") {
    throw new TypeError(
      `"${name}" expects symbol as the first argument, instead got "${first}"`
    );
  }
  if (environment[first] !== undefined) {
    throw new RuntimeError(`Can't redefine "${first}" variable`);
  }
  return (environment[first] = evaluate(second));
} 
```

### 特殊符号

如果我们这么做了会发生什么:

```
(define + 1) 
```

这会变得非常混乱:

```
> (+ + +)
2 
```

我们不想那样，所以让我们禁止这种行为:

```
if (name === "define") {
  if (typeof first !== "string") {
    throw new TypeError(
      `"${name}" expects symbol as the first argument, instead got "${first}"`
    );
  }
  if (
    environment[first] !== undefined ||
    first === "+" ||
    first === "-" ||
    first === "define"
  ) {
    throw new RuntimeError(`Can't redefine "${first}" variable`);
  }
  return (environment[first] = evaluate(second));
} 
```

*注意*:思考一下为什么我们以与其他环境变量相同的方式处理特殊关键字。

### 未定义的值

如果我们这么做了会发生什么:

```
(+ x y) 
```

而我们从来没有定义过`y`？正如我们在[上一篇文章](https://stereobooster.com/posts/demystify-programming-languages-errors)中谈到的，有两种方法可以处理错误:

*   返回一些指示错误的值，例如`undefined`或`null`
*   中断当前执行

我选择了第二个选项:

```
if (typeof ast === "string") {
  if (environment[ast] === undefined) {
    throw new RuntimeError(
      `Can't find "${ast}" variable. Use \`(define ${ast} ...)\` to define it`
    );
  }
  return environment[ast];
} 
```

### 未定义的定义值

如果我们这么做了会发生什么:

```
(+ + +) 
```

此刻它将导致:

```
Can't find "+" variable. Use `(define + ...)` to define it 
```

但是我们什么时候会照它说的去做呢

```
(define + 1) 
```

这将导致

```
Can't redefine "+" variable 
```

我不认为会有一个开发者想要使用特殊符号作为变量，所以我们不要担心这个。

我们悄悄地引入了第三种类型(`function`作为值)，这种类型目前不可用，但在这种奇怪的行为中表现出来了。

## 测试

当前的实现不容易测试，因为它使用了一个全局变量(`environment`)。让我们通过给`evaluate`
添加第二个(可选)参数来解决这个问题

```
const evaluate = (ast, environment = {}) => {
  //...
  let [name, first, second] = ast;
  const numberOfArguments = ast.length - 1;
  if (name === "+") {
    return evaluate(first, environment) + evaluate(second, environment);
  } else {
    // ...
  }
}; 
```

现在我们可以编写测试:

```
const assert = require("assert");

let testEnv = {};
assert.equal(evaluate(parse("(define x 1)"), testEnv), 1);
assert.equal(testEnv["x"], 1);

assert.equal(evaluate(parse("(+ x x)"), { x: 1 }), 2);

try {
  evaluate(parse("(+ x x)"), {});
} catch (e) {
  assert.equal(
    e.message,
    'Can\'t find "x" variable. Use `(define x ...)` to define it'
  );
}

try {
  evaluate(parse("(define 1 1)"), {});
} catch (e) {
  assert.equal(
    e.message,
    `"define" expects symbol as the first argument, instead got "1"`
  );
}

try {
  evaluate(parse("(define x 1)"), { x: 1 });
} catch (e) {
  assert.equal(e.message, `Can't redefine "x" variable`);
}

testEnv = { y: 1 };
evaluate(parse("(define x y)"), testEnv);
assert.equal(testEnv["x"], 1);

try {
  evaluate(parse("(define x x)"));
} catch (e) {
  assert.equal(
    e.message,
    'Can\'t find "x" variable. Use `(define x ...)` to define it'
  );
}

try {
  testEnv = {};
  evaluate(parse("(define + 1)"), testEnv);
  // we don't want this to be valid program
  evaluate(parse("(+ + +)"), testEnv);
} catch (e) {
  assert.equal(e.message, `Can't redefine "+" variable`);
} 
```

## PS

这篇文章的源代码是[这里是](https://github.com/stereobooster/write-a-language/tree/master/3.variable)。

你猜怎么着。下一个主题是函数、闭包和作用域。

> 塞缪尔·泽勒在 Unsplash 上的照片