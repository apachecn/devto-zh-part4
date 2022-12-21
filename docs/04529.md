# 焦点与焦点内

> 原文：<https://dev.to/laurieontech/focus-vs-focus-within-3748>

今天我们要讨论几个不同的伪类。如果你不清楚什么是伪类，我建议从这篇文章开始。

[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## Hover 伪类简介

### 劳丽 Aug 27 ' 19 分钟阅读

#css #webdev #learning #beginners](/laurieontech/an-introduction-to-the-hover-pseudo-class-o2k)

### 可聚焦元素

我们将讨论`focus`和`focus-within`，但在此之前，有必要回顾一下在 CSS 环境中 focusable 的含义。

一个元素是可聚焦的，如果你可以点击它，点击它或 tab 键来选择。表单输入和链接就是很好的例子。

当用户聚焦于一个元素时，`focus`和`focus-within`伪类都会被触发，尽管方式不同。

### 聚焦

我们先来回顾一下`focus`。这个伪类应用于一个元素。当该元素成为焦点时，样式就会出现。

以输入列表为例。每个输入都有自己的 id 和相应的`focus`样式。

[https://codepen.io/laurieontech/embed/yLBVLZG?height=600&default-tab=result&embed-version=2](https://codepen.io/laurieontech/embed/yLBVLZG?height=600&default-tab=result&embed-version=2)

根据您选择的输入，它将具有相关的背景颜色。

请注意，这个伪类响应一个元素状态，并将样式应用于同一个元素。在我们的下一个例子中，情况就不是这样了。

### 焦点-内异是怎样的？

焦点在内被分配给父元素。当任何子元素获得焦点时，应用样式规则。选择输入、点击链接等。

事实证明，我们之前的列表示例是一个很好的例子。如果我们假设每个`input`都是孩子，那么`li`就是父母。因此，让我们将`focus-within`添加到我们的`li`元素中。*和前面的例子一样，我们实际上添加了一个 id 并将它与每个 li 元素相关联。*

[https://codepen.io/laurieontech/embed/oNvYgvz?height=600&default-tab=result&embed-version=2](https://codepen.io/laurieontech/embed/oNvYgvz?height=600&default-tab=result&embed-version=2)

如果我们点击一个输入，我们会看到周围的元素接受颜色。

那么实际上发生了什么呢？元素正在查看它内部的元素。当`input`获得焦点时，样式被应用。然而，由于规则在`li`元素上，所以样式被应用到`li`元素，即使状态改变发生在`input`中。

有道理吗？

### “祖父母”元素

让我们看另一个例子。元素还有另一个父元素`ul`。如果我们将我们的`focus-within`伪类应用于该元素会怎么样？

[https://codepen.io/laurieontech/embed/KKPzowG?height=600&default-tab=result&embed-version=2](https://codepen.io/laurieontech/embed/KKPzowG?height=600&default-tab=result&embed-version=2)

如果我们点击`ul`里面的一个`input`，这个样式就会被应用到`ul`元素上。

### 结论

这些都是非常好的 CSS 特性，理解它们之间的区别将有助于您更直接地应用样式。迫不及待地想看看你做了什么！