# 您可能不知道的两个特殊的 spread 操作符用例

> 原文：<https://dev.to/duranenmanuel/two-exceptional-use-cases-for-the-spread-operator-you-may-not-know-of-5ap8>

*原贴于[Enmascript.com](https://enmascript.com/articles/2019/09/20/two-exceptional-use-cases-for-the-spread-operator-you-may-not-know-of)T3】*

<mark>**#你知道吗**是一个新的英语脚本系列，我们对我们认为可能相关或被低估的主题进行简短的解释，如果你正在寻找更完整的深入文章，请查看我们的 **[其他系列](https://dev.to/series)** 。</mark>

继 Ecmascript 2015 之后，javascript 中最受欢迎的功能之一无疑是 spread 运算符。在这篇简短的“您知道吗”文章中，我们将探索几个我们认为非常有用但不太为人所知的异常用例。

## 可选/有条件扩散

基于条件添加对象属性和值:

```
const isDog = true;

const obj = {
    key: 'value',
    ...(isDog && { woof: true })
}; 
```

这样，我们可以有条件地添加`woof`属性，而不必使用任何 if/else 逻辑或类似的方法...**如果条件返回 true，它将添加属性，否则，对象将保持不变**。

**结果`console.log(obj)`** 结果

```
{ key: 'value', woof: true }; 
```

## 复制对象并排除选中的属性

假设我们喜欢某个 CPU 的部件，我们希望保留除 ssd 之外的大部分部件，因为我们当然希望有更多的空间:

```
// Original CPU
const CPU = {
    ram: '32gb',
    ssd: '64gb',
    micro: 'i7'
};

// new CPU copy without the 64GB ssd
const { ssd, ...newCPU } = CPU; 
```

现在，如果您控制台日志`newCPU`，您会看到它不再包含`ssd`属性，这是因为我们利用 *rest 操作符*将该属性从包含在`newCPU`中的 rest 中排除了。

**结果`console.log(newCPU)`** 结果

```
{ ram: '16gb', micro: 'i7' }; 
```

对于 spread operator，您还有什么相关的用例要评论吗？去红迪网或推特上我们的社交网站上试试吧。