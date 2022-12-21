# CFScript 中的表单处理第三部分:发送电子邮件通知

> 原文：<https://dev.to/mikeborn/form-processing-in-cfscript-sending-form-notifications-via-email-lil>

到目前为止，在本系列中，我们已经了解了如何验证表单条目以及如何将表单条目保存到数据库中。今天我们将看到如何通过简单地发送一封电子邮件来通知一个人(通常是站点管理员或编辑)一个新的表单条目已经被提交和处理。

电子邮件适用于表单通知，因为整个条目通常可以放在一封电子邮件中，回复电子邮件可以向填写表单的原始用户发送消息。相比之下，短信的内容长度有限，不支持丰富的格式或回复功能。

## 通过配置的 SMTP 服务器发送邮件

如果你的 web 服务器也安装了 SMTP 服务器(Postfix，有人吗？)并在 ColdFusion 或 Lucee 管理员中进行配置，发送电子邮件就像指定收件人地址、发件人地址和主题一样简单。

```
cfmail(
    to="admin@mysite.com",
    from="noreply@mysite.com",
    subject="New form submission from #form.name#"
){
    // email contents go here
} 
```

## 通过邮件枪 SMTP 发送邮件

例如，如果您没有安装或配置 SMTP 服务器，您可以使用`server`、`port`、`username`和`password`参数通过 Mailgun SMTP 服务器发送。

```
cfmail(
    to="admin@mysite.com",
    from="noreply@mysite.com",
    subject="New form submission from #form.name#",
    server=server.system.environment.SMTP_HOST,
    port=server.system.environment.SMTP_PORT,
    username=server.system.environment.SMTP_USERNAME,
    password=server.system.environment.SMTP_PASSWORD
){
    // email contents go here
} 
```

这里我使用存储在 Lucee 的`server.system.environment`结构中的[环境变量通过 Mailgun 的 SMTP 服务器发送。这些环境变量可以在`/env/environment`文件或您的 bash 配置文件中设置成如下所示:](https://blog.simplicityweb.co.uk/109/lucee-5-simpler-access-to-environment-variables) 

```
SMTP_HOST=smtp.mailgun.org
SMTP_PORT=587
SMTP_USERNAME=postmaster@mail.mysite.com
SMTP_PASSWORD=123A923BCB6BGA7B*3 
```

确切的功能取决于您的系统，但通常这些变量会在系统启动或概要文件启动时加载到您的环境中。

## 将邮件回复设置为

设置`replyto`电子邮件地址将让我们回复填写表单的用户:

```
cfmail(
    to="admin@mysite.com",
    replyto="#form.email#"
    from="noreply@mysite.com",
    subject="New form submission from #form.name#"
){
    // email contents go here
} 
```

这太棒了，因为它允许管理员快速响应原始用户。当通过 SMS 或 Slack 等其他媒介交付时，您根本无法做到这一点。

## CFMail 正文输出

这里是我们在邮件正文中输出(或*呈现*)表单提交的地方。这非常简单——我们使用`writeOutput()`函数在邮件正文中输出 HTML 和表单值:

```
cfmail(
    to="admin@mysite.com",
    replyto="#form.email#"
    from="noreply@mysite.com",
    subject="New form submission from #form.name#"
){
    writeOutput("New form submission by #form.name#");
    writeOutput("#form.message#");
} 
```

注意，默认情况下，电子邮件的 MIME 类型是`text/plain`。为了在我们的电子邮件中呈现 HTML，我们需要通过`type="text/html"`或者仅仅是`type="html"` :
来设置 mime 类型

```
cfmail(
    to="admin@mysite.com",
    replyto="#form.email#"
    from="noreply@mysite.com",
    subject="New form submission from #form.name#",
    type="html"
){
    writeOutput("New form submission by #form.name#");
    writeOutput("#form.message#");
} 
```

## 避开邮件中的 XSS

请不要在没有编码的情况下输出用户提交的数据(表单变量)！您最不想做的事情就是将垃圾邮件发送者的脚本标签(想想`<script src="http://badwebsite.com/js/payload.js"></script>`标签)发送到您客户的电子邮件地址。这就是你所说的[跨站点脚本](https://www.acunetix.com/websitesecurity/cross-site-scripting/)，在 CFML 这很容易避免——只要用`encodeForHTML()` :
把每个`#form.field#`包起来就行了

```
cfmail(
    to="admin@mysite.com",
    replyto="#form.email#"
    from="noreply@mysite.com",
    subject="New form submission from #form.name#"
){
    writeOutput("New form submission by #encodeForHTML( form.name )#");
    writeOutput("#encodeForHTML( form.message )#");
} 
```

有一堆为特定上下文设计的`encodeFor*()`函数，比如 [`encodeForURL()`](https://cfdocs.org/encodeforurl) 和 [`encodeForHTMLAttribute()`](https://cfdocs.org/encodeforhtmlattribute) 。我不能演示所有这些，但如果你愿意，我可以写一篇更详细的 XSS 博客！

请注意，我们的电子邮件模板看起来更大、更复杂。真实世界的代码可能会很大、复杂和混乱，所以让我们通过**不使用 CFScript** 来清理它。

## 最佳 Dang 模板语法

从脚本语言输出 HTML 是- *老实说* - **尴尬的**。它在 Javascript 中很笨拙(尽管最近模板文字有了很大改进)，在 PHP 中很笨拙，在 CFScript 中当然也很笨拙。Twig、Jade 和 Mustache 等模板引擎为其他语言解决了这一问题，但在 ColdFusion 中，我们拥有世界上**最好的 dang 模板语法**。

那么为什么不用呢？

CFML 允许我们根据需要在脚本语法和标签语法之间切换——不需要库，不需要额外的编译，也不需要学习时髦的模板语法。

我建议为电子邮件模板创建一个`views/emails/`目录。那么每封新邮件都可以是一个以联系方式命名的独立文件，比如`views/emails/donate-thankyou.cfm`或`views/forms/forgotpassword.cfm`。

这里有一个简单的例子:

```
cfmail(
    to="admin@mysite.com",
    from="noreply@mysite.com",
    subject="New form submission from #form.name#"
){
    include "/views/forms/contact/email.cfm";
} 
```

有了这个设置，我们的电子邮件发送代码看起来更干净，电子邮件模板看起来也更好。

我们甚至可以在`_head.cfm`和`_foot.cfm`中创建一个标准化的电子邮件页眉和页脚，并包含主电子邮件模板
中的内容

```
<cfinclude template="./_head.cfm" />
<!---

        EMAIL BODY GOES HERE

--->
<cfinclude template="./_foot_.cfm" /> 
```

## 格式化电子邮件中的表单条目

对于快速完成通知邮件，您可以循环遍历`form.fieldnames`来动态输出每个表单字段:

```
<p>
<cfloop list="#form.fieldnames#" index="field">
    <strong>#encodeForHTML( field )#:</strong>
    #encodeForHTML( form[ field ] )#<br>
</cfloop>
</p> 
```

如果你想要高质量的电子邮件模板，你可能想要这样的东西:

```
<h1>New form submission by #encodeForHTML( form.email )#</h1>
<p>Message: #encodeForHTML( form.message )#</p>
<p>
    Name: #encodeForHTML( form.name )#<br>
    Email: #encodeForHTML( form.email )#
</p> 
```

## 邮件附件

假设我们有一个工作申请表，其中有一个上传简历文件的地方:

```
<form enctype="multipart/form-data" action="processApplication.cfm">
    <label for="resumeFile">Please add your resume in PDF format</label>
    <input type="file" id="resumeFile" name="resume">
    <!--- more form here --->
</form> 
```

通过 POST 请求发送的文件存储在服务器的临时目录中。要将文件附加到电子邮件中，我们首先需要将文件保存到一个半永久的位置，这样它就不会在假脱机的电子邮件发出之前被清除(即删除)。 [`fileUpload()`](https://cfdocs.org/fileupload) 函数很好地解决了这个问题:

```
var uploadedFile = fileUpload( "/files/resume/", "resume", "application/pdf", "makeunique" ); 
```

保存文件后，我们可以使用 [`cfmailparam()`](https://cfdocs.org/cfmailparam) :
将文件附加到我们的电子邮件中

```
cfmail(
    to="admin@mysite.com",
    from="noreply@mysite.com",
    subject="New form submission from #form.name#"
){
    // Email body here
    cfmailparam( file="#uploadedFile.serverfile#" );
} 
```

## 结论

电子邮件是表单通知的绝佳选择。CFML 是电子邮件的天堂。这篇指南的结尾比我预期的要长得多——希望你喜欢它，并且可以把它作为你的第一个 CF 应用的方便参考！