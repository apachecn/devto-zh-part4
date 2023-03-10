# 为什么应该停止在 for 循环中声明变量(尤其是在 JavaScript 中)

> 原文：<https://dev.to/nicolalc/why-you-should-stop-declaring-variables-inside-a-for-loop-3npo>

在我的职业生涯中，我接触过很多不同的编程语言，尤其是 C#和 javascript。

在我多年的开发过程中，我遇到了很多移动和桌面应用程序的性能问题。

通常，缺少表演的主要原因是**剧本**。
脚本是我们网站/应用程序的主要部分，它们很重，使用很多资源，比如 CPU 和 RAM。

在 Javascript 中，没有手动垃圾收集器系统(像 c#或 c++ **malloc** 或 **free** 方法)，所以你需要优化应用程序的资源管理部分，以防止内存泄漏、僵尸数据或其他性能问题。

但是，作为开发人员，我们能做些什么来减少 RAM 的使用呢？

# 我们需要写好代码，重用资源，巧妙处理数据。

比如，**不要在 For 循环**内部声明变量，直到真正需要的时候！

## 为什么

循环的**是一个循环的方法调用，所以在**循环的**中声明的变量将只存在于循环范围内，并且它将在每次循环迭代中被初始化
！这意味着，在每个循环周期中，将为变量分配一个内存空间！**

什么鬼东西！

这个事实，再加上无类型的 Js 语言，可能会造成大量的内存泄漏、性能问题和其他不良行为。

回收 RAM 的一个简单而有效的方法是声明一次变量，然后在需要时重用它们。for 循环是最简单的情况，但是您可以在任何地方复制这个解决方案。

## 举例

### 不好

在 for 循环中声明 cursor，并在其中声明 string。

```
for (let i = 0; i < 1000; i++) {
  let logString = 'Current is: ' + i;
  console.log(logString);
}
console.log('%o', window.performance.memory); 
```

```
{totalJSHeapSize:  68876822,  usedJSHeapSize:  46264582,  jsHeapSizeLimit:  2197815296} 
```

## 好

在 for 循环外声明**光标**和**日志字符串**:

```
let logString = '';
let i = 0;
for (i = 0; i < 1000; i++) {
  logString = 'Current is: ' + i;
  console.log(logString);
}
console.log('%o', window.performance.memory); 
```

```
{totalJSHeapSize:  57747133,  usedJSHeapSize:  45757109,  jsHeapSizeLimit:  2197815296} 
```

可以看到第二个实现中的 **totalJSHeapSize** 小于第一个 **( < 11129689‬byte ~ 11mb)** ， **usedJSHeapSize** 也是 **( < 507473byte‬ ~ 0.5mb)** 。

我知道，这不是一个很好的节省，但是对于大型项目，这可以节省您的 web 应用程序性能！

# 结论

我们可以使用很多优化来提升我们的应用程序，这个解决方案不仅仅是 JS 相关的，你可以在任何语言中使用它。但是我认为 JS 是最好的选择，因为它不是资源优化的。

我希望你觉得这篇文章有用或者只是有趣！