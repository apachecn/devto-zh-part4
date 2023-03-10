# CFScript 中的表单处理第一部分:表单验证

> 原文：<https://dev.to/mikeborn/form-processing-in-cfscript-part-one-form-validation-5d33>

本系列将教您如何使用 ColdFusion 的 CFScript 语法处理表单提交。在第一部分中，我将向您展示在后端验证表单提交的基础知识！

**注意:**本文(以及整个系列)假设您没有使用像 Vue 或 ReactJS 这样的前端框架来生成和验证表单。如果是的话，很好，但是**不要依赖客户端验证，因为它太容易被绕过。参见后端与前端验证部分。**

## 目录

*   [表单验证简介](#intro-to-form-validation)
*   [表单验证:后端对前端](#form-validation-backend-vs-frontend)
*   [验证字段长度(必填字段)](#validating-field-length-required-fields)
*   [验证字段类型](#validating-field-type)
    *   [静态类型检查](#static-type-checking)
    *   [迭代类型检查](#iterative-type-checking)
*   [处理验证错误](#dealing-with-validation-errors)
*   [结论](#conclusion)

## 表单验证简介

在验证表单提交时，有两个主要的事情要记住，它们应该指导您的整体验证策略。

首先，表单验证的主要目的是 1)当用户输入错误的值时通知用户，2)防止后端错误。(例如，`cfmail()`会对一些格式严重错误的电子邮件地址抛出错误)。除此之外，验证应该尽量少用*。这是因为我们很容易过于严格，无意中排除了邮政编码、电子邮件地址和电话号码与我们预期不同的用户。*

 *其次，如果用户不愿意，你不能强迫他们输入个人信息！例如，要求一个`address`字段将会产生大量伪造的信息，比如“1600 Pennsylvania Ave NW”。

现在，让我们来看看我们将要验证的一个基本 HTML 表单。这里没有什么花哨的，只是一个小的联系方式。捐赠表格要大得多，需要更严格的验证(如果你正在通过你的服务器处理支付，我为你祈祷！)

```
<form action="contact.cfm">
    <div class="field">
        <label for="name">Your Name</label>
        <input type="text" name="name" id="name" value="" />
    </div>
    <div class="field">
        <label for="email">Your Email</label>
        <input type="text" name="email" id="email" value="" />
    </div>
    <div class="field">
        <label for="message">Message</label>
        <textarea name="message" id="message"></textarea>
    </div>
    <div class="submit">
        <input type="submit" class="button button-primary" value="Send">
    </div>
</form> 
```

稍后，我们将调整这个表单以支持我更喜欢的后端验证的特定“流”。

## 表单验证:后端对前端

前端级别的表单验证比后端级别的表单验证优越得多。想想用户体验——有了前端验证，您可以立即得到反馈，避免一开始就发送坏数据。后端验证只有在前端被破坏、禁用(Noscript 模式)或通过一些 bot 或黑客向后端提交 HTTP 请求而故意绕开时才是必要的。

出于这个原因，在通过后端进行验证时，您可能会省去一些客套话，忽略对体面的 UX 的需求。我说“可能”——我仍然喜欢为那些禁用 javascript 的合法用户提供一个不错的 UX。

## 验证字段长度(必填字段)

验证表单域并不完全是核科学。对于很多字段(不是全部，而是很多)，保证字段值不为空就足够了。在验证必填字段时，我使用一个变量来存储一个简单的被认为是“必填”的字段名列表，如果这些字段中有任何一个确实是空的，我们就停止处理并向用户显示一个错误。

**注意:**我在这篇博文中使用了成员函数。成员函数可以很棒，只要它们相对较短，重要的是，*只实现一个目的*。在我看来，更大的代码块可能属于一个`for`循环——而不是一个成员函数。

下面是我使用一些聪明的*成员函数来验证必填字段的方法。

**聪明有用但危险；请小心使用。(参见上面关于成员函数的注释。)*

```
var requiredFields = "email,phone,message";
var invalidFields = "";
form.fieldnames.each((fieldname) => {
    if ( requiredFields.find(fieldname) && isEmpty(form[fieldname]) ) {
        writeOutput('<p class="alert alert-danger">Please fill out #fieldname#</p>');
        invalidFields.listAppend(fieldname);
    }
});
if ( invalidFields.len() ) {
    // halt processing and re-render the form.
    cfabort();
} 
```

这里的 CFML 相当简单——遍历所有表单字段，看看是否需要，如果没有提供，就抛出一个错误并停止处理。这相当简单，但是完全可行——跳到“处理验证错误”以获得处理错误消息的改进方法。

## 验证字段类型

验证数据的*类型*比验证数据的*存在*要困难得多。

### 静态类型检查

简单的方法是使用`isValid()`来检查电子邮件、电话、zip 或其他标准输入。

```
if ( !isValid("email", form["email"]) ) {
    writeOutput('<p class="alert alert-danger">Please enter a valid email</p>');
}
if ( !isValid("telephone", form["phone"]) ) {
    writeOutput('<p class="alert alert-danger">Please enter a valid phone number</p>');
} 
```

我说**简单的方法**是有原因的:*我个人不推荐这个选项*。为什么？因为 [IsValid 对于邮件验证](https://www.raymondcamden.com/2014/07/21/ColdFusion-isValid-Email-and-new-TLDs/)来说是不准确的，而保证一个邮件地址真正有效的唯一方法就是给用户发一封邮件。例如，这是注册表单中的常见做法。

我会避免对任何特定于本地化的格式进行严格的验证，比如邮政编码或电话号码——有太多的变体了。对于任何验证正则表达式，几乎肯定会有某些“有效”格式的问题。这是我的观点，你可以自由使用或拒绝它！

对于更简单的值，如数字、浮点数或字符串，`isValid()`就可以了。例如，我们可以验证捐赠表单的“amount”字段，以确保它是一个美元值。

```
if ( !isValid("float", form["amount"]) ) {
    writeOutput('<p class="alert alert-danger">Please enter a valid donation amount</p>');
} 
```

### 迭代式检查

现在，代替硬编码的字段类型检查，我们可以用类型信息:
遍历一个字段名数组

```
var formFields = [{
    name: "amount",
    type: "float"
},{
    name: "name",
    type: "string"
}];
formFields.each((field) => {
    if ( form[field.name] != "" && !isValid(field.type, form[field.name]) ) {
        writeOutput('<p class="alert alert-error">#field.name# must be a valid #field.type#</p>');
    }
}); 
```

如果我打算通过 CFML 动态生成表单，我更喜欢这种方法，因为我可能已经有了每个字段的元数据。对于大多数较小的/非生成的 HTML 表单，谁会真正关心我的代码是否有点重复呢？

## 处理验证错误

上面显示的大多数例子会立即显示一个引导警告，并中止或退出表单处理，向用户显示消息。只要我们相信我们的前端验证，这就足够了*。前端验证将首先过滤掉大多数提交无效数据的真实用户。*

然而，如果我们有能力生成 HTML 表单，我们可以改进这个模型。暂停请求、显示错误并要求用户单击 back 按钮是不方便的，并且可能会清除表单，导致用户丢失所有的进度。为了获得更好的流程，我通常用错误消息填充一个`errors`数组，然后在顶部显示错误(如果提交无效):

```
var requiredFields = "email,phone,message";
var errors = [];
form.fieldnames.each((fieldname) => {
    if ( requiredFields.find(fieldname) && isEmpty(form[fieldname]) ) {
        errors.append("#fieldname# is required.");
    }
});
if ( errors.len() ) {
    // don't process the form; display it instead.
    include "form.cfm";
} 
```

一旦我们有了错误消息的数组，我们就可以很容易地在`form.cfm`的顶部为每个错误显示一个引导警告，如下所示:

```
<cfif errors.len() >
    <cfloop list="#error#" index="error">
       <p class="alert alert-error">#error#</p>
    </cfloop>
</cfif>
<!--- render form ---> 
```

最后，确保用提交的`form.field`值填充每个输入来调整表单。这允许用户在无效提交后无缝地选择，让他们轻松地填写必填字段或纠正电子邮件打字错误。

```
<form action="contact.cfm">
    <div class="field">
        <label for="name">Your Name</label>
        <input type="text" name="name" id="name" value="#encodeForHTMLAttribute(form.name)#" />
    </div>
    <div class="field">
        <label for="email">Your Email</label>
        <input type="text" name="email" id="email" value="#encodeForHTMLAttribute(form.email)#" />
    </div>
    <div class="field">
        <label for="message">Message</label>
        <textarea name="message" id="message">#encodeForHTMLAttribute(form.message)#</textarea>
    </div>
    <div class="submit">
        <input type="submit" class="button button-primary" value="Send">
    </div>
</form> 
```

*PS。确保编码所有用户提交的数据，以防止跨网站脚本(XSS)！*

## 结论

表单验证并不难，只是繁琐。希望这能帮助你理解验证的基础。当有疑问时，比你需要的更宽容，但是*永远不要相信用户输入*！

这就完成了 CFScript 中表单处理的第一部分。如果你喜欢这个或者讨厌这个，请在下面评论。:)*