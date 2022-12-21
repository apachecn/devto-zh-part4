# 使用字段集在运行中禁用 HTML 表单

> 原文：<https://dev.to/chromiumdev/disable-a-html-form-while-in-flight-using-fieldset-61b>

我非常喜欢那些能够很好地控制用户与页面交互能力的 ui:比如在表单提交过程中更改一些值。我们都见过*“在完成之前不要改变这个表单！”*消息。🙄

在`<fieldset disabled>`中有一个标准的 HTML 特性可以帮助你做到这一点。首先，这里有一个演示:

[https://codepen.io/samthor/embed/LKYRGy?height=600&default-tab=result&embed-version=2](https://codepen.io/samthor/embed/LKYRGy?height=600&default-tab=result&embed-version=2)

尝试提交表单，点击其中一个蓝色链接(它们不会去任何地方，只是为了演示)，然后按 Tab 键。输入无法聚焦！🤯

## 特性

当您的浏览器看到一个设置了`disabled`属性的`<fieldset>`时:例如`<fieldset disabled>`，它将完全禁用其中的每个`<input>`、`<textarea>`和`<button>`元素。🚫📝

这是非常强大的。您的代码可能看起来像:

```
yourForm.addEventListener('submit', (event) => {
  event.preventDefault();  // disable normal submit

  const body = new FormData(yourForm);
  const p = fetch('/foo', {method: 'POST', body}).then((response) => {
    // ...
  });

  // important bit here
  yourFieldset.disabled = true;
  p.finally(() => {
    // .finally runs even if the fetch fails
    yourFieldset.disabled = false;
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

## 其他用途为`fieldset`

`fieldset`元素还允许您对 HTML 表单元素进行分组，包括添加一个`legend` (-ary)。这对于可访问性很有用，也是一种视觉化地设计组的方式。对于一个简单的演示，请查看 MDN 上的[这一页。](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/fieldset)

## 替代品

真正简单的经典替代品，*强大的*功能如`<fieldset disabled>`往往包括:

*   添加一个覆盖表单的巨大元素，这样用户就不能点击它
*   遍历每个`<input>`并标记为`disabled`
*   只是把表格藏起来。

与`<fieldset disabled>`相比，所有这些都非常痛苦。🤮

## 谢谢！

如果你对控制用户交互很感兴趣，一定要仔细阅读“惰性”属性，它更进了一步，但是还没有完全的浏览器支持。

Eleven