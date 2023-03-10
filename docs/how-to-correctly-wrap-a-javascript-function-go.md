# 如何正确包装 JavaScript 函数

> 原文：<https://dev.to/trackjs/how-to-correctly-wrap-a-javascript-function-go>

包装 JavaScript 函数使您可以向不受您控制的函数添加通用逻辑，如本机和外部函数。许多 JavaScript 库，如 TrackJS 代理，需要包装外部函数来完成它们的工作。添加包装器允许我们监听代码中的遥测、错误和日志，而无需您显式调用我们的 API。

您可能希望包装一个函数来添加检测或临时调试逻辑。您还可以更改外部库的行为，而无需修改源代码。

## 基本功能包装

因为 JavaScript 非常动态，我们可以通过简单地用新的东西重新定义函数来包装函数。例如，考虑一下`myFunction` :
的包装

```
var originalFunction = myFunction;
window.myFunction = function() { 
  console.log("myFunction is being called!"); 
  originalFunction(); 
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个简单的例子中，我们包装了原始的`myFunction`并添加了一条日志消息。但是有很多事情我们没有处理好:

*   我们如何传递函数参数？
*   我们如何维护范围(`this`的值)？
*   我们如何获得返回值？
*   万一出错了怎么办？

为了处理这些事情，我们需要在包装方面变得更聪明一点。

```
var originalFunction = myFunction;
window.myFunction = function(a, b, c) { 
  /* work before the function is called */ 
  try { 
    var returnValue = originalFunction.call(this, a, b, c);
    /* work after the function is called */ 
    return returnValue; 
  } catch (e) { 
    /* work in case there is an error */ 
    throw e; 
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

注意，在这个例子中，我们不只是调用函数，而是用`this`的值和参数`a`、`b`和`c`来调用函数。`this`的值将从您附加包装函数的地方传递，在本例中为`Window`。

我们还将整个函数放在一个`try/catch`块中，这样我们就可以在出错时调用定制逻辑，重新抛出它，或者返回一个默认值。

## 高级功能包装

基本的包装示例在 90%的情况下都能工作，但是如果您正在构建共享库，比如 TrackJS 代理，这还不够好！为了像专业人士一样包装我们的功能，我们应该处理一些边缘情况:

*   未声明或未知的参数怎么办？
*   我们如何匹配函数签名？
*   我们如何镜像函数属性？

```
var originalFunction = myFunction;
window.myFunction = function myFunction(a, b, c) { /* #1 */
  /* work before the function is called */
  try {
    var returnValue = originalFunction.apply(this, arguments); /* #2 */
    /* work after the function is called */
    return returnValue;
  }
  catch (e) {
    /* work in case there is an error */
    throw e;
  }
}
for(var prop in originalFunction) { /* #3 */
  if (originalFunction.hasOwnProperty(prop)) {
    window.myFunction[prop] = originalFunction[prop];
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有 3 个微妙但重要的变化。首先(#1)，我们命名了函数。看起来是多余的，但是用户代码可以检查`function.name`的值，所以在包装时保持名称很重要。

第二个变化(#2)是我们调用包装函数的方式，使用了`apply`而不是`call`。这允许我们传递一个`arguments`对象，它是运行时传递给函数的所有参数的一个类似于[数组的对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments)。这允许我们支持可能具有未定义的或可变数量的参数的函数。

有了`apply`，我们不需要函数签名中定义的参数`a`、`b`和`c`。但是通过继续声明与原始函数相同的参数，我们保持了函数的 [arity](https://en.wikipedia.org/wiki/Arity) 。也就是说，`Function.length`返回签名中定义的参数个数，这将反映原始函数。

最后一个变化(#3)将用户指定的属性从原始函数复制到我们的包装中。

### 局限性

这种包装是彻底的，但是 JavaScript 总是有限制。具体来说，很难用非标准原型(如对象构造函数)正确包装函数。这是一个通过继承更好解决的用例。

一般来说，改变函数*的原型是可能的*，但这不是一个好主意。操纵原型会产生严重的性能影响和意想不到的副作用。

## 尊重环境

函数包装给了您很大的能力来检测和操作 JavaScript 环境。你有责任明智地运用这种力量。如果您正在构建函数包装器，请确保尊重用户和您的操作环境。可能还有其他的包装器，为事件附加的其他监听器，以及对函数 API 的期望。脚步轻点，不要破坏外部代码。

JavaScript 经常出错，而且是以不可预知的方式。TrackJS 捕获客户端 JavaScript 错误,因此您可以看到并响应错误。免费试用一下，看看我们的函数包装有多棒。