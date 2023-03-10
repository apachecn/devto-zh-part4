# 使用正则表达式验证简单表单

> 原文：<https://dev.to/vicradon/validate-a-simple-form-using-regex-ci2>

在本教程中，我们将创建一个表单并使用正则表达式验证其值

## 创建标记

标记会非常简单。只有普通的 HTML 和一些输入标签

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        RegEx Form
        <link rel="stylesheet" href="./styles.css">
    </head>
    <body>
        <h1>New User Signup</h1>
        <form>

            <input type="text" name="username" placeholder="username">
            <p>Username must be lowercase including numbers and contain 5 - 12 characters</p>

            <input type="text" name="email" placeholder="email">
            <p>Email must be a valid address, e.g. me@mydomain.com</p>

            <input type="password" name="password" placeholder="password">
            <p>Password must alphanumeric (@, _ and - are also allowed) and be 8 - 20 characters</p>

            <input type="text" name="telephone" placeholder="telephone">
            <p>Telephone must be a valid 11 digits telephone number</p>

        </form>
        <script type="text/javascript" src="./validation.js"></script>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

根据标记，有四个输入字段需要验证

1.  用户名:
    1.  必须只包含小写字母
    2.  可以包含数字
    3.  允许 5 到 12 个字符
    4.  不区分大小写
2.  电子邮件:
    1.  必须包含名称
    2.  必须包含@和。
    3.  必须包含域
    4.  必须包含扩展名，扩展名可以有子扩展名(例如. co.uk)
3.  密码
    1.  可以包含@、_ 和-
    2.  长度必须为 8 到 20 个字符
4.  电话
    1.  必须仅包含 11 位数字

## 一点 css 会有帮助

```
body{
    font-family: arial;
    color: #333;
}
h1{
    font-weight: normal;
    margin: 20px auto;
    text-align: center;
}
form{
    width: 90%;
    margin: 20px auto;
}
input{
    display: block;
    padding: 8px 16px;
    font-size: 2em;
    margin: 10px auto;
    width: 100%;
    box-sizing: border-box;
    border-radius: 10px;
    border: 3px solid #ccc;
    outline: none !important;
}
.valid {
    border-color: #36cc36;
}
.invalid {border-color:orange}

input + p {
    font-size: 0.9em;
    font-weight: bold;
    margin: 0 10px;
    text-align: center;
    color: orange;
    opacity: 0;
    height: 0;
}
input.invalid + p {
    opacity: 1;
    height: auto;
    margin-bottom: 20px;
} 
```

Enter fullscreen mode Exit fullscreen mode

这增加了一点趣味，并根据情况为每个字段提供了有效/无效的类样式。

## 归主家什

验证将使用 JavaScript 实现(很明显)。我们从为每个输入字段定义正则表达式开始。但是这将使用一个对象来完成，以包含所有内容。

```
const input_fields = {
  username: /^[a-z\d]{5,12}$/i,
  email: /^([a-z\d\.-]+)@([a-z\d-]+)\.([a-z]{2,8})(\.[a-z]{2,8})?$/,
  password: /^[#\w@_-]{8,20}$/,
  telephone:/^\d{11}$/,
} 
```

Enter fullscreen mode Exit fullscreen mode

## 到底怎么回事？

1.  用户名正则表达式必须以字符类`[a-z\d]{5,12}`定义的值开始和结束。这意味着，5 到 12 个字符是小写字母或数字。^和$表示表达式以这些字符开始和结束。没有什么能在他们之后到来。正则表达式后面的 I 表示它应该不区分大小写。
2.  电子邮件正则表达式有六个部分。
    1.  名称:小写字母、连字符、点或数字，带有一个或多个由`+`表示的字符。它们用括号括起来以便分组。
    2.  @符号跟在后面
    3.  域名应包含一个或多个小写字符、数字或连字符
    4.  圆点跟随其后。它的特殊含义被反斜杠覆盖
    5.  第一个扩展名包含 2 到 8 个字符
    6.  第二个是可选的，用`?`表示。规则和第一个一样
3.  密码包含任意单词字符`\w`(字母和数字)。也支持`# _ - @`。
4.  电话号码是 11 位数字

我们现在可以定义一个验证函数来处理我们的验证

```
const validate = (field, regex) => {
  regex.test(field.value) ? field.className = 'valid' : field.className = 'invalid';
} 
```

Enter fullscreen mode Exit fullscreen mode

我们定义了一个名为 validate 的新函数，它有两个参数，field 和 regex。对字段的值调用 regex.test()方法，并使用 tenary 运算符进行检查。

```
let keys = document.querySelectorAll('input');
keys.forEach(item => item.addEventListener(
  'keyup', e => {
    validate(e.target, input_fields[e.target.attributes.name.value])
  }
)); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们选择所有的输入元素，并给它们一个 keyup 事件。我们正在输入的特定输入字段的验证函数。
validate 函数充当回调函数，将特定字段作为第一个参数，将 input_fields 对象中相应的键作为第二个参数。

```
e.target.attributes.name.value
//this gets the target field and goes to its attributes 
//for the name attribute and checks the value of that. 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

我们成功地使用正则表达式来验证一个表单。如果你完全迷失了，我建议你谷歌一下正则表达式。可以从我的 github repo 下载示例代码

[https://github . com/vicradon/regex 验证](https://github.com/Vicradon/regex-validation)