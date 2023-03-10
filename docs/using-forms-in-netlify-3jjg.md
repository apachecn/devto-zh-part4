# 在网络生活中使用表单

> 原文：<https://dev.to/khansubhan95/using-forms-in-netlify-3jjg>

Netlify 提供的功能总是让我感到惊讶。Netlify 提供的一个很酷的特性是支持自定义表单。您只需在页面中包含 Netlify 提供的表单标记，就大功告成了。每当有人在您的网站上使用此表单提交信息时，Netlify 将自动提供他们在 Netlify 仪表板上提交的信息。此外，Netlify 还提供了一些高级的表单功能，如 reCAPTCHA、文件上传、将表单通知导出到 csv、与 Slack 和 Zapier 集成等。

我已经使用这个特性为我的[联系人](https://dev.to/contact)页面创建了一个联系人表单。这是做到这一点的食谱。

### 在页面中包含表单标记。

将 Netlify 提供的表单标记包含在您想要的页面中。我用过这个代码

```
<form name="contact" method="POST" data-netlify="true">
  <p>
    <label style="font-family: monospace;">Name<br><input style="width: 100%;" type="text" name="name" /></label>   
  </p>
  <p>
    <label style="font-family: monospace;">Email<br><input style="width: 100%;" type="email" name="email" /></label>
  </p>
  <p>
    <label style="font-family: monospace;">Message<br><textarea style="width: 100%;" rows="15" name="message"></textarea></label>
  </p>
  <div data-netlify-recaptcha="true"></div>
  <br>
  <p>
    <button class="send-button" type="submit">Send</button>
  </p>
</form> 
```

请注意在表单标记中使用了 data-netlify="true"。这告诉 Netlify 在 Netlify 仪表板上显示表单提交。此外，所有字段都应该提供名称属性。

最后，我使用`<div data-netlify-recaptcha="true"></div>`加入了 reCAPTCHA 对垃圾邮件保护的支持。

### 连接扎皮尔

每当用户提交表单时，您可能希望通过电子邮件得到通知。为此，Netlify 提供了与 Zapier 的集成。您将需要配置此集成以使用所需的“收件人”地址。您还可以配置此集成，使其使用用户提交的表单名称、电子邮件和消息。

### 杂项

您还可以使用 AJAX 表单提交。Netlify 还允许表单在 React 和 Vue 等库中使用

Netlify 表单的文档可以在[这里](https://www.netlify.com/docs/form-handling/)找到。