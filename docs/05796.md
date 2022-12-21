# otp 不起作用

> 原文：<https://dev.to/rsanzedah/otp-not-working-513b>

你好，我曾经为我的自动化代码使用过 pt.now。登录时工作正常，但是当我把 chrome 驱动程序更新到 76 时，opt 部分就停止工作了。现在认证是错误的。任何反馈。对于 totp 来说和 chromedriver 版本有关系吗

公共静态 String get OTP(String secret key){
//String secret key = " 6 bslnllrh 7 hljfmm "；// < -这个 2FA 秘钥。

```
Totp totp = new Totp(secretkey);
return totp.now(); 
```