# CSS 网格:自动调整和自动填充

> 原文：<https://dev.to/nunocpnp/css-grid-auto-fit-auto-fill-4hkh>

在 CSS 网格中使用 repeat 时，不用设定次数，我们可以使用 auto-fit 或 auto-fill。

通过使用这些属性之一，我们可以创建一个响应式网格容器，而不需要额外的媒体查询。

假设我们想要构建一个 5 列的网格容器，我们可以通过添加几行 css 代码到我们的 div 容器中来快速完成

```
.grid-container {
    display: grid;
    grid-template-columns: repeat(5, 1fr);
} 
```

要使用**自动适应**或**自动填充**属性，首先我们需要在我们的网格中使用 minmax 属性，minmax 将让您定义我们的容器中每一列的最小和最大宽度。

让我们重构我们的代码来使用 minmax 属性，在这个特殊的例子中，我将定义 150px 为最小值，1fr 为最大值，如果我们在容器上有空间，1fr 将允许每一列均匀伸展:

```
.grid-container {
    display: grid;
    grid-template-columns: repeat(5, minmax(150px, 1fr));
} 
```

我添加了几行额外的 css 代码，只是为了让事情看起来更容易。让我们在这里检查一下运行中的代码[！](https://codepen.io/nuno-pereira/pen/ZEzBQLz)

很好，我们有一个 5 列的网格布局，工作正常，但是当我们试图在较小的设备上查看网格时，我们发现它没有响应。

让我们实现**自动适应**属性，看看不同之处:

```
.grid-container {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
} 
```

通过这个简单的改变，我们突然有了一个响应式的 5 列网格布局，让我们看看它的实际效果[这里](https://codepen.io/nuno-pereira/pen/JjPbGNR)！

现在我们有了一个完全响应的 5 grid 容器，而不需要使用任何媒体查询。

使用**自动填充**和**自动调整**来调整列的大小，这两者之间的区别只有在行宽足以容纳列时才明显。

如果你使用的是**自动调整**，内容将延伸以填充整个行宽，而使用**自动填充**，浏览器将允许空列占据行中的空间。

让我们看一下我们一直在使用的同一个例子，但是现在自动填充属性在起作用[这里](https://codepen.io/nuno-pereira/pen/zYOozPe)！

就这样，希望你喜欢这个关于 CSS 网格属性的小技巧。