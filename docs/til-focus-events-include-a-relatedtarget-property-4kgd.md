# TIL:焦点事件包含一个 relatedTarget 属性

> 原文：<https://dev.to/stefanjudis/til-focus-events-include-a-relatedtarget-property-4kgd>

今天我偶然发现了一个描述焦点事件属性的 MDN 页面。`blur`、`focus`、`focusin`、`focusout`算作焦点事件。

事实证明，如果您附加一个`focus`事件监听器，被触发的事件将不仅包括一个`target`元素，还包括一个`relatedTarget`元素。

```
document.querySelector('button')
  .addEventlistener('focus', (event) => {
    console.log(event.target);        
    // 👆 the element that received focus
    console.log(event.relatedTarget); 
    // 👆 the element that lost focus (if any)
  }); 
```

Enter fullscreen mode Exit fullscreen mode

焦点事件中`target`和`relatedTarget`的规则如下:

| 事件名称 | 目标 | 相关目标 |
| --- | --- | --- |
| `focus` | 接收焦点的元素 | 元素失去焦点 |
| `blur` | 元素失去焦点 | 接收焦点的元素 |
| `focusin` | 接收焦点的元素 | 元素失去焦点 |
| `focusout` | 元素失去焦点 | 接收焦点的元素 |

使用`relatedTarget`，当用户在你的界面中“跳转”时，你可以计算出先前聚焦的元素是什么。

`relatedTarget`也可以是`null`当没有上一个/下一个目标时。例如，当一个按钮有焦点，而用户单击了不可聚焦的内容时，就会发生这种情况。

那对我来说完全是新闻！😲

如果你想体验一下，看看这个特性的运行，我在 CodePen 上构建了一个快速原型[。](https://codepen.io/stefanjudis/pen/YzKYbPO)