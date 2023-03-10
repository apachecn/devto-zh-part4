# 计算可能的魔方组合

> 原文：<https://dev.to/revelmind/calculating-the-possible-rubik-s-cube-combos-3eio>

## 大家好！

今天我将告诉你如何计算魔方有多少种组合。

(这在 web 和 Node 上都有效！)

> 正确的输出在帖子的最后:)

我们将从在 Javascript 文件中创建一个函数开始，姑且称之为`calc`。

```
function calc() {
} 
```

确保给它两个参数，`x`和`y`，就像这样:

```
function calc(x, y) {
} 
```

现在，在这个函数中，定义一个名为`j`的变量。

```
function calc(x, y) {
  var j = 0;
} 
```

我们将使用这个变量作为函数的输出，所以`calc(1, 1)`将输出`y`，它将等于`1`。

进行您的`for`循环。它将帮助我们计算价值。

```
function calc(x, y) {
  var j = 0;
  for(var i = 0; i < y; i++) {
  }
} 
```

在我们的`for`循环中，它做了 3 件事(按顺序):

*   定义初始`i`变量。
*   确保`i`小于`y`。
*   给`i`加一。

太好了，现在添加一个`if-else`语句来检查`j`是否等于`0`。

```
function calc(x, y) {
  var j = 0;
  for(var i = 0; i < y; i++) {
    if(j == 0) {
    } else {
    }
  }
} 
```

### 我们为什么要这样做？

公式是`j`设为`j * x`进行`y`次。如果我们不设置初始值，它将永远是`0`。

让我们结束我们的功能！

```
function calc(x, y) {
  var j = 0;
  for(var i = 0; i < y; i++) {
    if(j == 0) {
      j = x;
    } else {
      j = j * x;
    }
  }
} 
```

绝对不要忘记在那个`for`循环之后返回`j`的值！

```
function calc(x, y) {
  var j = 0;
  for(var i = 0; i < y; i++) {
    if(j == 0) {
      j = x;
    } else {
      j = j * x;
    }
  }
  return j;
} 
```

### 好了，现在怎么办！？

那么我如何用这个函数来总结魔方的可能组合数呢？

魔方有六个面；每个都有不同的颜色。每一面有 9 块瓷砖，有 6 种可能的颜色。

我们可以这样算出来:

```
calc(calc(6, 9), 6); 
```

用`console.log`把它打印到控制台上，你的代码应该是这样的:

```
function calc(x, y) {
  var j = 0;
  for(var i = 0; i < y; i++) {
    if(j == 0) {
      j = x;
    } else {
      j = j * x;
    }
  }
  return j;
}

console.log(calc(calc(6, 9), 6)); 
```

它将输出这个:

```
> 1.0475325355943345e+42 
```

这就是我们的答案，1.0475325355943345e+42。

感谢大家的阅读！<3