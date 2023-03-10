# 验证 HTML 表单输入的提示

> 原文：<https://dev.to/nedyudombat/tips-for-validating-html-form-inputs-nb9>

表单验证是我们每天都要做的事情，因为我们想改善用户体验。

我将解释一些使用 html 验证数据的简单方法。

### **输入属性**

HTML 提供了一些有助于验证数据的属性。让我们来看看其中的几个。

**1。Pattern:** 这指定了一个正则表达式，当提交表单时，应该根据这个表达式来验证输入的值。这可用于多种输入类型，包括`text`、`date`、`tel`、`email`、`password`等。

**2。Title:** 这不用于验证数据，而是提供更多关于数据应该以何种格式提供的信息。

**3。Required:** 这确保提交表单时输入字段不为空。

**4。Minlength:** 这只是设置输入字段中提供的数据的最小长度。参见下面的示例。

```
 <input type="text" minlength="4" required> 
```

Enter fullscreen mode Exit fullscreen mode

这些属性可以与许多输入类型相结合，以确保提供正确的数据。

### **输入类型**

使用正确的输入类型有助于验证不正确的数据类型。以下是验证表单时可能需要的输入类型。

**1。电子邮件:**使用`email`输入类型收集用户电子邮件将验证数据，以确保表单提交时格式正确。

**2。密码:**这不仅验证了密码字段，还屏蔽了输入的字符。可以通过向输入字段的`pattern`属性传递一个正则表达式来指定密码的强度。

```
 <input type="password" pattern="(?=.*[a-z]).{6}" title="Must be 6 characters or more and 
contain at least 1 lower case letter"> 
```

Enter fullscreen mode Exit fullscreen mode

上面是一个简单的密码输入字段的例子，它根据 pattern 属性中提供的正则表达式进行验证。title 属性包含当提供的密码未通过 regex 验证时要显示的文本。

**3。Tel:** 这基本上定义了一个用于输入电话号码的字段。它主要用于优化移动设备上的键盘。将它与 pattern 属性结合起来可以帮助验证 tel 输入字段中的数据。看看下面的例子

```
 <input type="tel" pattern="[\+]\d{2}[\(]\d{2}[\)]\d{4}[\-]\d{4}" title="Phone Number format is +99(99)9999-9999"> 
```

Enter fullscreen mode Exit fullscreen mode

**4。Number:** 定义了一个只接受数值的输入字段。它允许对可接受的数字范围进行限制。

```
 <input type="number" min="2" max="8"> 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子显示了一个数字输入字段，它只接受 2 到 8 之间的数字。

下面是上面提到的一些东西被很好地应用的片段。

```
 <form id="form" autocomplete>
    <input type="text" minlength="4" required>
    <input type="email" required>
    <input type="tel" pattern="[\+]\d{2}[\(]\d{2}[\)]\d{4}[\-]\d{4}" 
     title="Phone Number format is +99(99)9999-9999" required>
    <input type="password" pattern="(?=.*[a-z]).{6}" 
     title="Must be 6 characters or more and 
     contain at least 1 lower case letter" required>
  </form> 
```

Enter fullscreen mode Exit fullscreen mode

*P.S* :数据验证必须在用户无权访问的服务器端完成，这确保了用户不会篡改服务器上正在处理的数据或数据类型。同样，在您想要进行一些客户端测试的情况下，您可以向您的`<form>`元素添加一个`novalidate`属性。

近年来，HTML 变得越来越强大，它能做的事情也很有趣。我会写很多关于它的文章。如果你喜欢这个，那么请❤️在这里和 Twitter 上关注我。