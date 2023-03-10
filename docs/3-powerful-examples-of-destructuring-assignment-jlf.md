# 析构赋值的 3 个有力例子

> 原文：<https://dev.to/laurieontech/3-powerful-examples-of-destructuring-assignment-jlf>

ECMAScript 总是添加新的特性，使我们的代码更加强大。我甚至开始讨论人们的最爱。

[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## ES2015+你最喜欢的新增功能是什么

### 劳丽 6 月 11 日 191 分钟阅读

#discuss #javascript #webdev](/laurieontech/what-s-your-favorite-addition-to-es2015-5468)

在那里，我列出了我的破坏作业。你可以在 mozilla 文档中了解更多信息。

所以，事不宜迟，这里有一些这种语法的很好的例子！

# 物体毁坏

假设我们有一个想要操作的对象，看起来像这样:

```
{
  data: {
    item: "this thing"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们把它传递给一个函数并访问`item`,那就有点混乱了。

```
(result) => {
   result.data.item
} 
```

Enter fullscreen mode Exit fullscreen mode

用析构赋值，我们可以改成这样！

```
({data}) => {
   data.item
} 
```

Enter fullscreen mode Exit fullscreen mode

导入和导出模块经常使用这个概念。

[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 出口、进口和违约，天啊！

### 劳丽 Apr 29 ' 19 分钟阅读

#javascript #react #webdev #beginners](/laurieontech/exports-and-imports-and-defaults-oh-my-fa3)

# 数组解构

如果我们有一个数组。

```
[
  {
    item: "this thing"
  },
  {
    num: 200
  }
] 
```

Enter fullscreen mode Exit fullscreen mode

在不析构赋值的情况下访问它并不理想。

```
(result) => {
   result[0].item
} 
```

Enter fullscreen mode Exit fullscreen mode

但是看看 ES2015+有多强大！

```
([data, status]) => {
   data.item
} 
```

Enter fullscreen mode Exit fullscreen mode

# 连在一起并带有别名！

前几天在使用`Promise.all`时，我遇到了一个惊人的析构赋值用例。如果您不熟悉，`Promise.all`解析多个承诺并将结果放在一个数组中。类似于

```
[result1, result2] 
```

Enter fullscreen mode Exit fullscreen mode

现在，考虑一个典型的 promise 响应(特别是当它是一个 http 请求时),两个结果看起来都像这样。

```
{
  data: {
    item: "this thing"
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以将前面的两个例子结合起来，使访问每个响应对象内部的内容更加清晰。

```
Promise.all([
            promise1,
            promise2,
        ]).then(([{ data: result1 }, { data: result2 }]) => {
            result1.item
        }); 
```

Enter fullscreen mode Exit fullscreen mode

现在，这里发生了很多事情。

*   我们使用数组析构来单独访问数组中的每一项。
*   然后，我们在那些数组条目上使用对象析构。

但是有一个转折。这是什么？

```
{data: result1} 
```

Enter fullscreen mode Exit fullscreen mode

这是给数据对象分配一个变量名。我们不想使用`data`，因为我们也在访问`result2`中一个叫做`data`的东西。请注意，我们访问的对象在前面，名称在后面。下面就不行了。

```
Promise.all([
            promise1,
            promise2,
        ]).then(([{ result1: data }, { result2: data }]) => {
            result1.item
        }); 
```

Enter fullscreen mode Exit fullscreen mode

你必须有唯一的变量名(这是可以理解的)。在本例中,`data`被使用了两次。

# 权力

但是现在你有了。看看有了析构赋值，我们的代码有多干净！