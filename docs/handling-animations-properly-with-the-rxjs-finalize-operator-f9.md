# 使用 RxJS finalize 运算符正确处理动画

> 原文：<https://dev.to/rxjs/handling-animations-properly-with-the-rxjs-finalize-operator-f9>

我最近在构建一个 Angular 应用程序，它必须从一个 API 请求数据。由于我使用的是 Angular HttpClient，默认情况下，数据请求的响应被包装在一个可观察对象中。

每当发起对数据的`GET`请求时，我希望显示一个动画省略号，表明数据检索过程正在进行。如果数据检索成功，或者在检索过程中出现错误，动画省略号将退出屏幕。

可观察对象是一种数据类型，它不断地向附属于它的订户发送数据。订户是一种数据类型，它会持续侦听从其订阅的可观察对象发出的数据。当订阅者订阅一个可观察对象时，订阅者有三个处理程序来响应可观察对象发出的数据。这三个处理程序是:

*   `next`处理程序——当可观测源从其数据序列中发出一个新值时执行，
*   `error`处理程序——当可观察数据序列中的值出现错误时执行，以及
*   `complete`处理程序——当可观察序列中没有更多值可发出时执行

假设下面的`getResults`方法返回一个可观察对象，那么`next`、`error`和`complete`处理程序在其 subscribe 方法中举例说明，如下面的代码片段
所示

```
getResults().subscribe(
  results => console.log('Next handler executed with results: ', results),
  error => console.error('Error handler executed with error: ', error),
  () => console.log(`Complete handler executed. All values have been emitted`),
); 
```

Enter fullscreen mode Exit fullscreen mode

作为 observables 的新手，我将隐藏动画省略号的方法放在了`complete`方法中，如下面的片段所示

```
getResults().subscribe(
  results => displayResults(results),
  error => notifyOnError(error.message),
  () => hideAnimatedEllipses(),
); 
```

Enter fullscreen mode Exit fullscreen mode

动画省略号是隐藏的(只要请求没有返回错误)。每当出现错误时，动画省略号仍然会在用户界面上与显示的错误信息一起跳动。

为了解决这个问题，我做的第一件事就是在`complete`和`error`处理程序中执行`hideAnimatedEllipses()`方法。没问题！它起作用了，但是后来我开始了解 finalize 操作符，这是完成工作的一个更好的方法。

学习 finalize 操作符不仅解决了我的问题，还暴露了我对三个订阅处理程序理解的错误。

我发现在执行了`error`处理程序之后，对`next`处理程序的进一步调用将不起作用，并且在执行了`complete`处理程序之后，对`next`处理程序的进一步调用也将不起作用。这就是为什么动画省略号一开始就在用户界面上自信地跳舞，甚至在显示错误信息之后。

> **完成**:当一个可观察的源在完成或出错时终止时，执行`finalize`操作符。

我意识到在执行`finalize`运算符函数时`hideAnimatedEllipses()`函数应该正确驻留，因此代码被更新为下面显示的代码片段

```
getResults()
  .pipe(finalize(() => hideAnimatedEllipses()))
  .subscribe(results => displayResults(results), error => notifyOnError(error.message)); 
```

Enter fullscreen mode Exit fullscreen mode

本质上

> 当可观测源从其数据序列中发出新值时，执行 **`next`** 处理程序，
> 
> **`error`** 处理程序在可观察对象的数据序列中出现错误时执行。在它被执行之后，对`next`的进一步调用将没有任何效果，并且
> 
> 当可观察序列中不再有可发出的值时，执行 **`complete`** 处理程序。在它被执行之后，对`next`的进一步调用将没有任何效果
> 
> 当一个可观察的源在完成或出错时终止时，执行 **`finalize`** 操作符。当订阅者取消订阅一个可观察对象时，`finalize`操作符中的代码也被执行，因此`finalize`的行为类似于承诺的`finally`方法或`try-catch-finally`阻塞。在 RxJS v5.5 之前，表示为`finally`。

你可以在 RxJS 文档[中找到关于`finalize`操作员的更多信息，请点击](http://reactivex.io/rxjs/file/es6/operators/finalize.js.html)

干杯！😃