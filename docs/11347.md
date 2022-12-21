# JavaScript 闭包简单解释

> 原文：<https://dev.to/shimphillip/javascript-closure-simply-explained-1f79>

闭包是由另一个函数返回的有状态函数。它充当一个容器来记忆来自其父作用域的变量和参数，即使父函数已经完成执行。考虑这个简单的例子。

```
function sayHello() {
  const greeting = "Hello World";

  return function() { // anonymous function/nameless function
    console.log(greeting)
  }
}

const hello = sayHello(); // hello holds the returned function
hello(); // -> Hello World 
```

看啊！我们有一个返回函数的函数！返回的函数被保存到一个变量中，并调用下面的代码行。

## 相同代码的多种写法！

现在您已经基本了解了什么是闭包，这里有几种方法可以编写与上面相同的代码。

```
// original
function sayHello() {
  const greeting = "Hello World";

  return function() { // anonymous function
    console.log(greeting)
  }
}

// #1
function sayHello() {
  const greeting = "Hello World";

  return function hello() { // named function
    console.log(greeting)
  }
}

// #2
function sayHello() {
  const greeting = "Hello World";

  function hello() { // named function
    console.log(greeting)
  }

  return hello; // return inner function on a different line
}

// #3
function sayHello() {
  const greeting = "Hello World";
  const hello = () => { // arrow function
    console.log(greeting)
  }

  return hello;
} 
```

选择一种你最喜欢的风格并坚持下去，因为上面的每一种变化仍然会打印出相同的结果！

```
const hello = sayHello();
hello(); // -> Hello World 
```

#### 私有命名空间

内部函数记得它被创建的环境，这很酷，但是它有什么用呢？一对夫妇。首先，*它可以保持你的变量私有*。这是一个经典的反例。

```
function counter() {
  let count = 0;
  return function() {
    count += 1;
    return count;
  }
}

const increment = counter();
console.log(increment()); // 1
console.log(increment()); // 2
console.log(count) // Reference error: count is not defined 
```

试图访问 count 变量会给我们一个引用错误，因为它没有暴露在全局环境中。这有助于我们减少错误，因为我们的状态由特定的方法更严格地控制。

#### 可复用状态

因为“count”是私有范围的，所以我们可以创建计数器函数的不同实例，它们的“count”变量不会重叠！

```
function counter() {
  let count = 0;
  return function() {
    count += 1;
    return count;
  }
}

const incrementBananaCount = counter();
const incrementAppleCount = counter();
console.log(incrementBananaCount()); // 1
console.log(incrementBananaCount()); // 2
console.log(incrementAppleCount()); // 1 
```

#### 模块设计模式

模块设计模式是构建 JavaScript 应用程序的流行惯例。它利用 IIFE(立即调用的函数表达式)来返回对象，并且只公开您想要公开的变量和方法。

```
let Dog1 = (function() {
  let name = "Suzy";

  const getName = () => {
    return name;
  }

  const changeName = (newName) => {
    name = newName;
  }

  return {
    getName: getName,
    changeName: changeName
  }
}())

console.log(name); // undefined
Dog1.getName() // Suzy
Dog1.changeName("Pink")
Dog1.getName() // Pink 
```

这段代码一运行，函数就执行并返回一个保存到 Dog1 的对象。这种模式可以追溯到保持我们的名称空间私有，只通过对象的形式显示我们想要的公共方法和变量。状态被封装！

## 著名的面试问题

运行以下函数的结果是什么？

```
for(var i=0; i<5; i++) {
  setTimeout(function() {
    console.log(i)
  }, 1000)
} 
```

为什么这是一个如此受欢迎的面试问题？因为它考验你对函数作用域/块作用域、闭包、setTimeout 和匿名函数的知识！答案 1 秒后打印出 5 个 5。

```
5
5
5
5
5 
```

怎么会？嗯，setTimeout 在 1 秒后循环运行 5 次。时间延迟后，它们执行内部函数，简单地注销 I。1 秒过去后，循环已经结束，I 变成了 5。五个 5 被打印出来。不是你所期待的？你可能想反复看到数字 1 到 5。

## 解

有几个解决方案，但是让我们把重点放在使用闭包上！

```
for(var i=0; i<5; i++) {
  setTimeout((function(index) {
    return function() {
      console.log(index);
    }
  }(i)), 1000)
} 
```

我们有一个由匿名函数返回的闭包，它接收当前的“I”作为参数，并将它们作为“index”输出。这样做可以捕获每个函数的当前变量 I。结果变成了

```
0 (...1000ms have passed)
1 (...1000ms have passed)
2 (...1000ms have passed)
3 (...1000ms have passed)
4 (...1000ms have passed)
5 (loop exits) 
```

恭喜你！🎉🎉现在你为下一次面试做了更充分的准备！😉请记住，闭包是一个可以访问包含该函数的作用域的函数。