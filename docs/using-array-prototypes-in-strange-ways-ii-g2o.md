# 以奇怪的方式使用数组原型#2

> 原文：<https://dev.to/bugmagnet/using-array-prototypes-in-strange-ways-ii-g2o>

通常使用`.fill()`来填充一个简单的值。但是，您可以用其他东西填充，比如记录甚至函数引用。

这里我们用随机数预先填充一个 100 个元素的数组:

```
const LIM = 1000;
const arrayOfRandomIntegers = new Array(LIM)
  .fill(function () {
    return Math.floor(Math.random() * LIM)
  })
  .map(function (item) {
    return item();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

当然，这并不像值填充那么简单——我们仍然需要评估每个单元格中的每个函数引用——然而，这是创建数组的另一种方式，并且不需要明显的`for(...;...;...)`代码。

函数引用方法允许一些有趣的事情。下面我们预加载一个字符数组，首先用对象的方法引用填充，然后用函数本身填充。

首先是物体本身:

```
function Generator(init) {
  var stuff,
  index;
  var that = this;
  if (init) {
    stuff = init;
    index = 0;
  }
  that.next = function () {
    return stuff.charAt(index++);
  }
  that.eod = function () {
    return index >= stuff.length;
  }
  return that;
} 
```

Enter fullscreen mode Exit fullscreen mode

示例对象公开了两个方法:`next()`和`eod()`(数据结束)。当我们实例化对象时，我们为它提供一些数据。每次调用`next()`都会返回下一个字符。`eod()`用于检查我们是否用完了数据。在这个实现中，如果用完了，对象将返回一个空字符串。

所以，第一次实例化和使用:

```
const string = "lewd did i live - evil i did dwel";

const H = new Generator(string);
const arrayFromH = new Array(string.length)
  .fill(H.next)
  .map(function (item) {
    return item();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

这里我们填充了一个对`.next()`的引用，并且相信如果我们请求的数据多于它所能提供的，那么`Generator`对象将会有礼貌地失败。事实上，因为我们已经告诉`Array`只为`string.length`分配空间，我们永远不会知道`Generator`是否礼貌。

第二次去:

```
const G = new Generator(string);
const arrayFromG = new Array(string.length)
  .fill(G)
  .map(function (item) {
    return item.eod() ? "*" : item.next();
  }); 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们存储对函数的引用，然后在`.map()`的主体中用`.eod()`进行检查，如果我们做得太过分，就存储一个星号。

对了，“好色的历史...dwel”的东西可以在这里找到

附言:ES6 里有发电机，这个不是。