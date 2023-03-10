# 解开 Array.prototype.flat 之谜

> 原文：<https://dev.to/laurieontech/demystifying-array-prototype-flat-gi9>

ES2019 正式向我们所有人开放。请注意，如果您使用这些功能，请确保您的浏览器和/或 transpiler 支持它们。

事不宜迟，让我们开始研究我们的第一个新特性。我给你 Array.prototype.flat！

# 古老的方式

嵌入式数组出于各种原因存在于我们的代码中，老实说，它们有点让人头疼。

```
let arr = [1, 2, [3, 4, [5, 6]]] 
```

Enter fullscreen mode Exit fullscreen mode

处理这样的事情过去需要像下面的代码这样不直观的技巧。

```
var merged = [].concat.apply([], arr); 
```

Enter fullscreen mode Exit fullscreen mode

这只会导致一层深度变平！

```
[1, 2, 3, 4, [5, 6]] 
```

Enter fullscreen mode Exit fullscreen mode

Boooooooo

# 新的方式！

然后出现了`flat()`。这是一个游戏改变者。

做我们上面做的同样的事情是轻而易举的。

```
var merged = arr.flat(1) 
```

Enter fullscreen mode Exit fullscreen mode

那个论点只是我们想要展平的深度。所以一级深给我们这个，就像以前一样。

```
[1, 2, 3, 4, [5, 6]] 
```

Enter fullscreen mode Exit fullscreen mode

注意，如果你没有传递一个参数，它默认为`1`。这意味着这些语句是等价的。

```
arr.flat(1)
//is the same as
arr.flat() 
```

Enter fullscreen mode Exit fullscreen mode

# 魔法

但是令人难以置信的强大之处在于它并不止于此。我们可以在一行中展平整个数组。

```
var merged = arr.flat(2) 
```

Enter fullscreen mode Exit fullscreen mode

变成了

```
[1, 2, 3, 4, 5, 6] 
```

Enter fullscreen mode Exit fullscreen mode

# 等待吧

我们甚至被赋予了一个更棒的功能。假设我们不知道数组的深度，但是我们想把它展平。

```
var merged = arr.flat(Infinity) 
```

Enter fullscreen mode Exit fullscreen mode

[![Ron Swanson saying what the hell just happened](img/b1f9de4fcfa2ee0b1be83d6579da6044.png)](https://i.giphy.com/media/xhN4C2vEuapCo/giphy.gif)

# 总结

真是奇迹！！！往前走，享受 ES2019 赋予我们的精彩。