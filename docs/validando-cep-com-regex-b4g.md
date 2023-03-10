# 使用 RegEx 验证 CEP

> 原文：<https://dev.to/emersonbroga/validando-cep-com-regex-b4g>

[![Validando CEP com RegEx](img/98a94236e8e0b1ac5a39aae49d2ca244.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--enMwdPOL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n32ra846qfcbqnzlelqj.jpg)

您可以使用正则表达式快速轻松地验证邮政编码。

使用较短语法创建同一函数的另一种方法:

```
const isValidBRZip = zip => /^[0-9]{5}-[0-9]{3}$/.test(zip); 
```

希望你们喜欢！

请务必访问 github 存储库，并查看 emersonbrogadev/social media snippets 中的所有提示。

[参加我们的开发者名单](https://emersonbroga.com/e/participe/)
[【学习 es 6】](https://amzn.to/2J4XnLg)[【查看 Instagram 上的提示](https://www.instagram.com/emersonbrogadev/)

=)