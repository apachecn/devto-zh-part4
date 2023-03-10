# JavaScript 冒泡排序最少的行

> 原文：<https://dev.to/letmypeoplecode/javascript-bubble-sort-in-the-fewest-lines-5e5n>

我没有拿到计算机科学的学位，所以偶尔我会用计算机科学的概念来教育自己。在面试中有人问我关于冒泡排序的问题，所以我决定用 JavaScript 编写一个。

## **冒泡排序是如何工作的？**

基本上，冒泡排序函数遍历一个数组，将每个值与其右边的邻居进行比较，如果邻居更小，就交换它们。它不断遍历数组，一遍又一遍，直到没有可交换的内容。

它看起来像这样。

```
Start: [9,6,3,2,4]
After 1: [6,3,2,4,9]
After 2: [3,2,4,6,9]
After 3: [2,3,4,6,9]
After 4: [2,3,4,6,9] 
```

Enter fullscreen mode Exit fullscreen mode

为什么它在第三次之后有了正确的顺序却经历了四次？

因为它直到第四次运行时才知道顺序是正确的，并且不需要交换任何东西。

所以我写了一个简单的函数。

```
function bubble(arr){
  do { 
    var swaps = false; 
    arr.forEach((val, ind) => {
      if (val > arr[ind + 1]) {
        swaps = true; 
        arr[ind] = arr[ind + 1];
        arr[ind + 1] = val;
      } 
    });
  } while (swaps == true);
  return arr;
} 
```

Enter fullscreen mode Exit fullscreen mode

## **这是最短的气泡排序吗？**

成功了。但是我想确保我用尽可能少的代码编写了它。当我谷歌“ [javascript 最有效的冒泡排序](https://www.google.com/search?q=javascript+most+efficient+bubble+sort)”时，前两个结果多花了 2-3 行。

归结起来有三件事:

**1。**他们在用 [`for`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for) 循环迭代数组，而我在用 [`Array.prototype.forEach()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach) 。

`forEach`方法向操作每个元素的回调函数提供数组中该元素的值、其索引，甚至数组本身。所以我保存了一行代码。

当他们需要交换值时，他们必须声明一个临时值来保存其中一个值，但是我已经提供了一个临时值作为函数的参数。

我的代码获取值和索引号(如`val`和`ind` ):

```
if (val > arr[ind + 1]) { 
  swaps = true; 
  arr[ind] = arr[ind + 1]; 
  arr[ind + 1] = val;
} 
```

Enter fullscreen mode Exit fullscreen mode

他们的代码，只得到一个索引号(如`i` ):

```
if (arr[i] > arr[i + 1]) { 
  swaps = true; 
  let temp = arr[i + 1]; 
  arr[i + 1] = arr[i]; 
  arr[i] = temp;
} 
```

Enter fullscreen mode Exit fullscreen mode

**2。**在创建他们的`for`循环之前，他们也用数组的长度声明了一个变量，而`forEach`只是知道一点。

**3。**其中一个用`let`关键字在`do... while`循环外声明了他们的`swaps`布尔。我和另一个用`var`在循环内部声明。`let`关键字是块范围的，所以如果他们在循环中声明了变量，循环控制就看不到它。`var`关键字是函数作用域的，所以在被声明和赋值后，在循环*中的任何地方都可以看到它。*

使用`let`并没有显著提高可读性，反而增加了不必要的复杂性(在这种情况下)。

## **好吧，短一点，但是我的*更好吗*？**

我对自己很满意，但是我已经知道使用`forEach`的一个代价。

一旦开始，`forEach`必须通过数组完成整个循环。一个`for`循环可以使用`return`或`break`关键字提前退出，它可以使用`continue`结束处理并跳到下一个迭代。`forEach`方法没有这些。

`for`循环的一个优势是冒泡排序*有*来迭代所有的值…差不多。

它不必将最后一个值与最后一个索引加 1 的未定义值进行比较。

一个`for`循环可以被设置为遍历除最后一个值之外的所有值，这意味着循环中少运行一次代码。

**胜者:`for`循环。**

另一个问题是确定数组长度的两种方法的比较效率。

在内部，`forEach`基本上是运行一个`for`循环，并在每次迭代中查找数组长度。

在`for`循环变体中，数组长度在变量中设置一次，然后循环使用该变量。

如果数组长度可能在循环中改变，比如用`Array.prototype.splice`方法删除重复项，那么在循环的每次迭代中查找它是非常有用的。

但是在数组大小保持不变的情况下，获取一次数组长度并将其放入一个变量供`for`循环使用……*似乎取决于引擎*。在 V8 (Chrome，node.js)中，查找实际上似乎稍微快一点(根据这个[堆栈溢出讨论](https://stackoverflow.com/questions/5752906/is-reading-the-length-property-of-an-array-really-that-expensive-an-operation))，但是用其他引擎 YMMV。

胜利者:让我们打成平局吧。

最后，关于`let`与`var`的使用，这确实是一个声明一次+重复赋值是否比在一个语句中重复声明和赋值更快的问题。

所以我对这两种方法进行了计时，在多次测试中分别运行了 100 亿次。`var`声明和赋值方法在每次测试中都以 0.2%左右的优势胜出。这还不足以称之为赢家。

胜利者:我们也打成平局吧。

## **那么，*被*挖矿*变得更好*？**

我的更短，可读性更好，但是能够在每次循环中跳过整个比较代码的执行是`for`循环的决定性优势。因此“更好”…嗯，这取决于你的优先事项。 [![🙂](img/c1766ba8b46615e3a821a99042b0d85a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2EAkP1Kt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/12.0.0-1/72x72/1f642.png)