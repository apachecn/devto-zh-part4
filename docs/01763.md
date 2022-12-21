# Javascript 中的 noop

> 原文：<https://dev.to/praneshpsg239/noop-in-javascript-478h>

先说#javascript 中的“noop”。

noop =无操作

正如它的缩写所说，它不会执行任何操作。迷惑？我们来看一个例子:

```
function noop(){}
const calculateSum = addFunction || noop; 
```

如这个例子所述，noop 就像一个默认函数，如果“addFunction”不存在，“calculateSum”就取 noop。意思是，它不会执行任何操作。如果没有定义“addFunction”并阻止“calculateSum()”作为函数运行 undefined，那么它将运行 noop 并返回 undefined。

如果我们用这样的东西会怎么样:

```
const calculateSum = addFunction || () => undefined; 
```

上面的语句非常好，但是通过使用命名函数，比如 noop，增强了用户的可读性。

它主要用作备份回调函数。我发现它被用在一个 React 下拉库中([down](https://github.com/downshift-js/downshift))还有 lodash 有 [_。noop](https://lodash.com/docs/4.17.15#noop) 等等。

TL；DR - It 是一个函数，它不执行任何操作，一旦被调用就返回未定义的结果。它有助于提高代码的可读性，并防止应用程序中断。
例如:

```
function noop(){}
// without noop:
const calculateSum = addFunction || () => undefined;
// with noop:
const calculateSum = addFunction || noop; 
```