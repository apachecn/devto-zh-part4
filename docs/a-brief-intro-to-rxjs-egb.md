# RxJs 简介

> 原文：<https://dev.to/osamaqarem/a-brief-intro-to-rxjs-egb>

这篇文章最初发表在我的博客上。

* * *

[RxJs](https://rxjs.dev/) 是针对 Javascript 的[反应式扩展](http://reactivex.io/)的实现。这些扩展是工具的集合，允许我们编写声明性的反应式代码，而不是命令式代码(例如，`do this when x`而不是`if x is this right now, then do the following...`)。基本上，它为我们提供了可以增强基于事件的逻辑处理的操作符(函数)，尤其是在处理多个相关事件时。

Rx 肯定是有学习曲线的。新的概念和术语在开始时会让人不知所措。但是一旦你尝试了足够多的次数，最终你会很快学会所有的 wizzabanga 单词和概念。我会尽量让这篇文章简单，但是你肯定需要熟悉 promises API。

## 承诺 vs 可观

想象一下，我们有一个描述我们思想的字符串数组。

```
const thoughts = ["food", "sleep", "code"]; 
```

Enter fullscreen mode Exit fullscreen mode

### 许诺

当我们在 JavaScript 中处理事件时，我们通常会使用 Promise API。对于一个承诺，有两种情况会发生:

1-它使用单个值进行解析。

2-它用一个值拒绝，通常是一个错误消息。

1 和 2 都表示一个承诺的完成。

```
const thoughtsPromise = () => {
  return new Promise((resolve, reject) => {
    resolve(thoughts);
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 可观察的

一个可观察对象是一个数据流，从中我们可以得到带有值的通知。我们可以在三种不同的场景中获得这些通知:

1-当流中有新值时。

2-当错误发生时给出错误值。

3-当流完成。

一个区别是，可观察值 ***可以用新值*** 解析多次。例如，假设你想在你的应用上向用户展示一个视频。让用户一次下载整个视频好，还是一点一点地播放好？可观测量可以帮助你对数据进行流式处理。

让我们创造一个可观察的。

`from`操作符可以将数组或承诺之类的数据转换成数据流。

```
import { from } from "rxjs";

// The trailing $ sign is a naming convention for observables in JS
const thoughts$ = from(thoughts); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

### 获取数据

回到我们的承诺。你认为当`thoughtsPromise`解决时会发生什么？

```
const getValue = async () => {
  try {
    const thoughtsArray = await thoughtsPromise();
    console.log(thoughtsArray);
    // Output: ["food", "sleep", "code"]
  } catch (err) {
    // handle error
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们一下子得到了数组。你认为当我们开始倾听价值时会发生什么，换句话说就是`subscribe`到我们的`thoughts$`可观察值？

```
// We subscribe to an observable to get values from it
thoughts$.subscribe(
  value => console.log(value),
  err => null, //handle error,
  () => null // handle completion,
);
// Output:
//    food
//    sleep
//    code 
```

Enter fullscreen mode Exit fullscreen mode

我们从数组中一个接一个地获取值。数据流。酷毙了。

[玩这个例子(React)](https://codesandbox.io/embed/sharp-wing-vfuqn?fontsize=14&expanddevtools=1)

## 我一定要知道 Rx 吗？

没有。但是这里有一些用例需要考虑:

1-如果你因为某种原因不能使用`async await`，在 JS 中保持代码扁平。

2-处理复杂的基于事件的逻辑，无论是网络相关的还是 UI 相关的(例如 websockets /拖放)。

3-如果你的队友来自不同的语言，但你们都知道 Rx，对他们来说，使用 RxJs 可能比使用`promises`和`async await`更容易。

4-在其他语言中，Rx 对于处理多线程非常有用。

如果你喜欢反应式编程，并且想把它应用到任何地方，那就去吧🙂。

[去抖动用户输入(反应)](https://codesandbox.io/embed/pensive-resonance-z5qci?fontsize=14)

## 有用链接:

*   Rx 操作员列表

[http://reactivex.io/documentation/operators.html](http://reactivex.io/documentation/operators.html)

*   Fireship 关于 RxJs 运营商的精彩视频

[https://www.youtube.com/watch?v=ewcoEYS85Co](https://www.youtube.com/watch?v=ewcoEYS85Co)

*   反应式编程

[https://en.wikipedia.org/wiki/Reactive_programming](https://en.wikipedia.org/wiki/Reactive_programming)

*   命令式编程

[https://en.wikipedia.org/wiki/Imperative_programming](https://en.wikipedia.org/wiki/Imperative_programming)

*   声明式编程

[https://en.wikipedia.org/wiki/Declarative_programming](https://en.wikipedia.org/wiki/Declarative_programming)

*   观察者模式

[https://en.wikipedia.org/wiki/Observer_pattern](https://en.wikipedia.org/wiki/Observer_pattern)