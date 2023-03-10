# 使用 bcrypt 通过 nodejs 设置密码

> 原文：<https://dev.to/arbaoui_mehdi/use-bcrypt-to-campare-passwords-using-nodejs-36be>

将您的密码存储为纯文本很快，但它并不安全，这就是为什么您必须让黑客通过使用*哈希*来获取重要信息变得复杂，有几个哈希函数，如 *md5* ，我们不会使用 md5，因为它不是为密码设计的，加上它被*密码破解*，当攻击者可以生成一个常用密码及其相应哈希的列表，然后将哈希与网站存储的哈希进行比较。

您将使用`bcrypt`，它更适合用于密码，bcrypt 使用 *salt* 来使散列输出唯一，即使您的用户使用相同的密码，这是 bcrypt 的一个简单用例，供想要更新密码的用户使用。

```
const bcrypt = require("bcryptjs");

// Generate Salt
const salt = bcrypt.genSaltSync(10);

// Plain Text Passwords
const currentpPassword = "abc123";
const oldPassword = "abc123";
const newPassword = "nWd6yCyj";

// Generate the Current User Password Hash
// by combining the salt and the password
const currentPasswordHash = bcrypt.hashSync(currentpPassword, salt);

// Compare the Old Password set by the user
// to the Current Password Hash
if (!bcrypt.compareSync(oldPassword, currentPasswordHash)) {
  console.log("The Current Password is Wrong");
}

// The new password should not be similar
// to the old password
if (bcrypt.compareSync(newPassword, currentPasswordHash)) {
  console.log(
    "The new password is similar to the new password, please choose a different one",
  );
} 
```

Enter fullscreen mode Exit fullscreen mode