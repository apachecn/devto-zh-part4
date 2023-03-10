# 创建正则表达式(正则表达式或 RegEx)

> 原文：<https://dev.to/emersonbroga/criando-uma-expressao-regular-regular-expression-ou-regex-4c2>

[![Criando uma Expressão Regular (Regular Expression ou RegEx)](img/2d5a78cebc4dbee24b9825b96b1b24ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZTNjHiJp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3aftd73vgia197h1du40.jpg)

要在 JavaScript 中创建正则表达式，可以通过将表达式括在斜杠内并将其赋给变量
来按字面方式

`const urlSlug = /^[a-z0-9-]+$/;`

创建 regex 的另一种方法是使用 RegExp 对象的构造函数:

`const urlSlug = new RegExp(/^[a-z0-9-]+$/);`

请注意，使用构造函数创建 regex 时，不需要将其置于斜杠之间。

建议使用构造函数，例如，当您从外部源(如用户输入)获取 regex 时。

希望你们喜欢！

请务必访问 github 存储库，并查看 emersonbrogadev/social media snippets 中的所有提示。

[参加我们的开发者名单](https://emersonbroga.com/e/participe/)
[【学习 es 6】](https://amzn.to/2J4XnLg)[【查看 Instagram 上的提示](https://www.instagram.com/emersonbrogadev/)

=)