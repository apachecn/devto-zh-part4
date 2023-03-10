# 介绍哈默-零开销钩🎣

> 原文：<https://dev.to/lucagez/introducing-hamo-zero-overhead-hooks-7ne>

你好👋

今天我想向大家介绍一个我刚刚完成的微型图书馆。

它被称为`Hamo`(拉丁语意为`hook`)，可以让你钩住**字面上的**一切，外加**零**开销。

# 问题

钩子很有用，但是它们经常碍事。您可以用事件发射器自己实现它们。但是，任何实现都需要大量的工作，并且会增加函数的复杂性。

更重要的是，根据需要挂钩的函数的重要程度，不可忽略的开销增加会导致速度变慢。

如果你能从应用程序的任何地方挂钩你的函数，而不降低一盎司的性能，会怎么样？

# 解

使用`Hamo`你可以做如下事情。

```
const hamo = require('hamo');

const [sum, onSum] = hamo((a, b) => a + b);

onSum('after', (result, a, b) => console.log(`The sum of ${a} plus ${b} is ${result}`);

sum(1, 3);

// 3
// The sum of 1 plus 2 is 3 
```

很方便吧？

你实际上可以挂接函数`before / after / oncebefore / onceafter`被挂接的函数。

好吧，那不是一个改变生活的例子，让我们从现实世界中举一些例子:

## 节点

在下面的代码片段中，我们挂接了一个写函数。

```
const hamo = require('hamo');
const { writeFile } = require('hamo');
const { promisify } = require('promisify');

// The `true` argument will notify hamo that the passed function is async
const [write, onWrite, offWrite] = hamo(promisify(writeFile), true);

module.exports = {
     write,
     onWrite,
     offWrite,
}; 
```

然后，从你应用程序的任何地方，你都可以**连接**或**分离**钩子，当有东西写到磁盘时，你会得到通知。

就像函数的通知系统。

```
onWrite('before', path => console.log(`Writing file to ${path}`);

// Writing file to {path} 
// ... somewhere `write` is busy writing something ...

offWrite('before');

// no more notifies 🌺 
```

## 浏览器

也许你想在一个`React`(功能性)组件渲染时得到通知。

```
const HelloComp = () => <h1>Hello!</h1>;

const [Hello, onHello] => hamo(HelloComp);

onHello('before', () => console.log('Rendering Hello...'));

onHello('after', () => console.log('Rendered Hello 🎉'));

// then, when mounting..

const App = () => {
     console.log('Mounting App...');

     return <Hello />;
};

// Mounting App...
// Rendering Hello...
// Rendered Hello 🎉 
```

# 它是如何工作的？

## 零开销

检测附加到函数的挂钩。然后，结果处理程序的主体在运行时动态生成。里面没有`if`语句，因为只有当前活动钩子严格需要的部分被添加到函数体中。

所以，零挂钩，零开销。

## 返回报表后运行函数

怎么可能从已经返回的函数中运行函数呢？
这是通过以下方式在事件循环中调度一个`micro-task`来实现的。

```
const func = () => {
     Promise.resolve().then(() => console.log(42));
     return 'The answer is';
};

func();

// The answer is
// 42 
```

通过在一个已经解决的承诺中运行代码，您可以确保事件循环将获得任务，并将它们安排在稍后执行。

嗯，差不多就是这样。

可以在这里查看回购:
[https://github.com/lucagez/hamo](https://github.com/lucagez/hamo)

快乐钩住所有人！🎣