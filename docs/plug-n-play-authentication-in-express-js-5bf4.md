# JavaScript 中的即插即用认证

> 原文：<https://dev.to/kauresss/plug-n-play-authentication-in-express-js-5bf4>

我的副业项目是一个即插即用的认证模块，主要是为开发人员从头开始构建的。它的构建考虑到了冗长性，因此初级开发人员可以参与其中。

使用 MongoDB、Express.js、Passport.js(社交登录)、EJS、SendGrid(邮件)和节点模块(bcrypt、jwt 等)

到目前为止，1.0 版的功能有:

1.  用户寄存器
2.  向 JWT 用户发送验证电子邮件
3.  经过验证的用户可以登录
4.  第三方登录(脸书、Google+和 Twitter)
5.  重定向到用户机密问题的密码重置电子邮件
6.  回答机密问题并重置密码
7.  谷歌垃圾邮件保护 recaptcha

我可能会放一个蜜罐来进一步保护垃圾邮件

[![](img/8cfe211f85dc7222defaa4a90a41e549.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5YpPOLBS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mijsjk950sdaup0quj7h.png)

[![](img/5eb09ad77a20c4537deacc5ed4bb2d90.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QqyhQE4R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dx961h0en6qqsjwozbkr.png)

[![](img/37083cccbb7708dcf852e8a5336fa1c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JOTjZKB6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/678v25nxn0xlidzxijkv.png)