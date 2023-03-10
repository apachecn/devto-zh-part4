# 将 ShareJS 附加到选择 HTML 元素

> 原文：<https://dev.to/brightdevs/attaching-sharejs-to-select-html-element-220d>

我发现在 [ShareJS](http://sharejs.org/) 库中缺少的一件事是将实时并发编辑附加到 HTML `<select>`元素的可能性。开箱即用，它只适用于文本字段- `<input>`和使用`doc.attachTextarea(elem)`功能的`<textarea>`。

解决这一缺陷并不是一件小事。ShareJS 与[操作转换](http://en.wikipedia.org/wiki/Operational_transformation)一起工作，提取文本的每个逻辑变化(添加或删除)并通过网络只发送变化信息。对于文本元素来说这很好，但是对于值总是在一个镜头中被替换的`<select>`，这就有点意义了。

不幸的是，对于`<select>`值的变化，我们没有“替换”操作——我们不得不忍受的*操作方式*仅限于插入和删除。这意味着我们必须用移除和插入来模仿“替换”操作。这种方法的问题是，当操作颠倒时——客户机首先接收新值插入，然后删除以前的值——中间的间歇值不是有效的`<option>`。它是新旧价值的结合。DOM API 不喜欢这样，拒绝这种改变，将`<select>`值设置为空 1。接下来的移除操作无法修复这个值，因为它试图从 DOM 中已经空了的字符串中移除一些东西。

我已经解决了这个问题，用一个很小的包装器来包装我的 DOM 元素，这个包装器保存原始值并为 ShareJS 转换公开它，同时仍然尝试更新原始元素的 DOM:

```
var rawValue = innerElem.value;
var elem = {
    get value () {
        return rawValue;
    },
    set value (v) {
        rawValue = v;
        innerElem.value = v;
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

ShareJS 也不会将自己附加到`change`事件，这是`<select>`元素的典型特征——它专门处理键盘事件。所以我必须自己附加并依赖于底层 ShareJS 实现的事件，伪造由库处理的事件类型——我选择了神秘的`textInput`事件。

以下是作为主旨的完整代码: [ShareJS attachSelect](https://gist.github.com/NOtherDev/9e713cfd68d6da9a174a) 。它向`Doc`原型添加了一个新函数，允许以我们调用 ShareJS native `attachTextarea` :
的相同方式调用它

```
if (elem.tagName.toLowerCase() === 'select') {
    doc.attachSelect(elem);
} else {
    doc.attachTextarea(elem);
} 
```

Enter fullscreen mode Exit fullscreen mode

请随意使用代码，我希望有人觉得有用。