# 不是不(！！)

> 原文：<https://dev.to/tyronasaurus_dev/not-not-gbm>

### 图片本

您需要检查页面上是否存在某个元素，让我们来看看这个例子:

您的网站上有一个很酷的昆虫列表，但是您想检查瓢虫元素是否出现在特定的实例中(可能用户最初从列表中选择了一群昆虫)

```
function doesLadybugExist() {
    return (document.getElementById("lady-bug"));
    // This will either return the element, or null
} 
```

这看起来不错...但是，这要么将返回元素，要么将返回 null，这不是错误的，但在这个例子中这不是我们想要的，我们想看看瓢虫 T2 是否存在

### 瓢虫们！！凉爽的

```
function doesLadybugExist() {
    return (!!document.getElementById("lady-bug"));
    // This will either true if it exists, or false
} 
```

仔细看一看**！！**

这很棒，因为它为我们返回了一个布尔值，告诉我们用户是否喜欢瓢虫。**成功！**

但没那么快。这是如何工作的？

快速的谷歌搜索会告诉你！！不存在于 java 上下文中。简直就是了！运算符使用了两次。

它的作用是将一个非布尔值转换成一个反布尔值，这意味着任何真值被评估为真，任何假值被评估为假(在布尔上下文中)

所有的值都是真的，除非它们被定义为假的

下面是根据 [MDN](https://developer.mozilla.org/en-US/docs/Glossary/Falsy) 在 Javascript 中为假的值

| 号码 | 价值 |
| --- | --- |
| 1. | 错误的 |
| 2. | 空 |
| 3. | 不明确的 |
| 4. | 0(注意:字符串“0”的计算结果为真) |
| 5. | 圆盘烤饼 |
| 6. | '' |
| 7. | 文档.全部 |

使用**！！**是一种干净、简单的方法，可以将 DOM 上的元素状态与布尔值进行比较，而不必担心类型转换和其他转换方法。

最后，不要不留下一个喜欢和独角兽；)