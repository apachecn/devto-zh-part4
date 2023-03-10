# 使用 RegEx 验证电子邮件

> 原文：<https://dev.to/emersonbroga/validando-email-com-regex-i17>

[![Validando email com RegEx](img/0a3129ca0e7be03e1f785439d782342e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HdzOcePY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hdp6fefzpww7f9m7ol7f.jpg)

随着当今顶级域数量的增加，很难验证电子邮件。
使用 RegEx(正则表达式或正则表达式)，我们只能检查电子邮件中是否包含@以及@前后的任何文本。更重要的是发送确认电子邮件。

写这个函数的另一个较短的方法是:

```
const isEmail = text => /^.+@.+$/.test(text); 
```

请务必访问 github 存储库，并查看 emersonbrogadev/social media snippets 中的所有提示。

[参加我们的开发者名单](https://emersonbroga.com/e/participe/)
[【学习 es 6】](https://amzn.to/2J4XnLg)[【查看 Instagram 上的提示](https://www.instagram.com/emersonbrogadev/)

=)