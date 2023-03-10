# 用指针事件替换鼠标事件

> 原文：<https://dev.to/stephencweiss/replacing-mouseevents-with-pointerevents-5485>

如何通过复制事件处理程序逻辑来处理不同类型的输入(例如，鼠标、触摸和笔)？指针事件令人信服地证明了它是一个可靠的解决方案。

[![pointer](img/3f4fff4816a71ddcb89c7afb1fde1ae1.png)](///static/7aac3619127b537520b5240c5349b4eb/b100d/pointer.png) 1<sup>1</sup>

> 指针事件提供了鼠标事件中所有常见的属性(客户坐标、目标元素、按钮状态等)。)除了用于其他输入形式的新属性:压力、接触几何形状、倾斜等。 <sup>2</sup>

尽管并非所有浏览器都支持指针事件，但主流现代浏览器已经广泛采用了指针事件(Safari 是一个主要的例外，尽管 v13 中会提供支持)。 <sup>3</sup>

[![can i use pointer events](img/b928c68fe79efcccca42619ec2e48530.png)](///static/d5a8fc72a4b6eb9dda8ce0e7f269111d/1589a/can-i-use-pointer-events.png)

这是一个好处，因为指针事件将使处理不同的输入事件变得更加简单。

# 资源:

*   <sup>1</sup> 指针事件| W3
*   <sup>2</sup>[T3】同上 T5】](https://www.w3.org/TR/pointerevents/)
*   <sup>3</sup> [指针事件|我能用……](https://caniuse.com/#search=pointerdown)