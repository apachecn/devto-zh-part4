# forEach 的乐趣

> 原文：<https://dev.to/gokatz/fun-with-foreach-1p1o>

## 那么，什么是 forEach？

`forEach`是一个小家伙，他迭代/循环遍历数组，并对每个元素执行给定的回调。它更像是传统的`for`环，但具有功能性。一个样本片段应该是这样的，

```
let colors = ['black', 'blue', 'red'];

colors.forEach((color) => {
    console.log(`${color} is a cool color`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

输出为:

```
black is a cool color
blue is a cool color
red is a cool color 
```

Enter fullscreen mode Exit fullscreen mode

因此，正如您所见，`forEach`将为被调用方数组的每个条目调用回调。是的，你可能已经知道这有什么好玩的了？

## 里面有什么好玩的？👯‍♂️

我们将看到一些您在实时产品/应用中可能不会遇到的片段，并尝试猜测这些片段的结果(不偷看输出)👀).这才是真正的乐趣所在😉

### 片段 1

```
let colors = ['black', 'blue', 'red'];

colors.forEach((color) => {
    colors.push('cyan');
    console.log(`${color} is a cool color`);
});

console.log(colors); 
```

Enter fullscreen mode Exit fullscreen mode

所以，当我仔细思考这个片段时，我预测这会导致一个无限循环。这完全可以理解，对吧？但是，我们的小家伙，`forEach`，**将会运行回调函数，运行的次数正好是初始数组长度**的次数。

这是一个小小的 [Twitter 投票](https://twitter.com/_gokatz/status/1129378217226203136)陈述了这样一个片段。看看这个。你并不孤单😛

液体错误:内部

这里，初始数组长度是 3，回调只执行 3 次。然而，**回调可以改变数组**。但是，回调将不会为后面超出界限(初始长度)的元素执行。

因此，输出将是:

```
black is a cool color
blue is a cool color
red is a cool color
[ 'black', 'blue', 'red', 'cyan', 'cyan', 'cyan' ] 
```

Enter fullscreen mode Exit fullscreen mode

### 片段 2

```
let colors = ['black', 'blue', 'red'];

colors.forEach((color, index) => {
    colors[index+1] = 'cyan';
    console.log(`${color} is a cool color`);
});

console.log(colors); 
```

Enter fullscreen mode Exit fullscreen mode

根据**回调可以改变数组**的规则，这个代码片段的输出会有些直接。从第一次运行开始，我们用这个`colors[index+1] = 'cyan'`语句将下一个索引的数组值改为`cyan`。因此，输出将是:

```
black is a cool color
cyan is a cool color
cyan is a cool color
[ 'black', 'cyan', 'cyan', 'cyan' ] 
```

Enter fullscreen mode Exit fullscreen mode

正如你可能注意到的，在结果数组中有一个额外的元素，这是因为，在最后一次运行(index = 2)时，我们将下一个 index (index = 3)元素的值赋值为`cyan`,如前所述，回调将不会为我们刚刚推送的最后一个元素运行，因为它驻留在初始数组长度之外。

### 片段 3

```
let colors = ['black', 'blue', 'red'];

colors.forEach((color, index) => {
    delete colors[index+1];
    console.log(`${color} is a cool color`);
});

console.log(colors); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们从数组中删除项目。你认为输出会是什么？已删除项目的占位符是什么？`undefined`？`NULL`？还是别的？

快速浏览时，循环的常见预测是，

```
black is a cool color
undefined is a cool color
undefined is a cool color 
```

Enter fullscreen mode Exit fullscreen mode

这是因为，我们知道回调将被调用数组的初始长度，在这个数组中，就是 **3** 。

但是，删除数组元素会使那个空间成为一个[洞](http://2ality.com/2015/09/holes-arrays-es6.html)，而这个`forEach`家伙**相当聪明，不会为数组**中的洞运行回调。

因此，当对第一个元素(index = 0)执行回调时，它将删除第二个元素，并且不会执行对第二个元素的回调，循环将跳到第三个元素。因此输出将是:

```
black is a cool color
red is a cool color
[ 'black', empty, 'red' ] // empty is just the representation of holes in V8 
```

Enter fullscreen mode Exit fullscreen mode

### 片段 4

那么，一个空数组会被如何处理呢？

```
let colors = new Array(3);

colors.forEach((color, index) => {
    colors[index] = 'cyan';
    console.log(`${colors[index]} is a cool color`);
});

console.log(colors); 
```

Enter fullscreen mode Exit fullscreen mode

什么都不会改变，空数组的所有元素都是孔。`console.log(colors)`将导致类似于
的结果

```
[empty × 3] 
```

Enter fullscreen mode Exit fullscreen mode

因此**回调不会对任何洞**执行，整个代码片段的实际输出也将是:

```
[empty × 3] 
```

Enter fullscreen mode Exit fullscreen mode

### 片段 5

`forEach`中另一个较少使用的特性是它可以接受第二个参数， [`thisArg`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach#Using_thisArg) ，如果这个参数被传递，回调将使用传递的上下文来执行。以下片段仅用于演示，您可以在 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach#Using_thisArg) 上找到更相关的示例。我在这里没有使用箭头功能，因为那样会使`this`变成`undefined`。

```
class colorHandler {
  isFavorite(color) {
    return color === 'cyan';
  }
}

let colors = ['black', 'blue', 'red', 'cyan'];

colors.forEach(function(color, index) {
    console.log(this.isFavorite(color))
}, new colorHandler()); 
```

Enter fullscreen mode Exit fullscreen mode

输出将是:

```
false
false
false
true 
```

Enter fullscreen mode Exit fullscreen mode

### 如此...

希望这很有趣。这就是我们的小家伙。关于`forEach`可能还有很多其他有趣的东西。请在评论中分享给我们惊喜。让我们看看 **Fun With Arrays** 系列中的另一个数组方法/属性。

还有一个有趣的事实:这个标题的灵感来自于牛逼(😉)节目由《生活大爆炸》系列**中的谢尔顿和艾米主持，名为**与旗帜同乐。****

[![](img/433db746a0c6d5f41b415678a5559f1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xllGqR0l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hy6b2komgorhj87i64vh.jpg)