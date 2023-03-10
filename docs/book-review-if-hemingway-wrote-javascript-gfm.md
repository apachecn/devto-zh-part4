# 书评——如果海明威写了 Javascript

> 原文：<https://dev.to/camerenisonfire/book-review-if-hemingway-wrote-javascript-gfm>

如果计算机代码是由历史上最伟大的作家创作的，它会是什么样子？一些最有创造力的语言学家会如何编写 Javascript 呢？Angus Troll 试图在[“如果海明威写了 Javascript”](https://nostarch.com/hemingway)中回答这些问题。

> 这是我的博客 [Imago Dev](https://imago.dev) 的交叉帖子。

这本书采用了完全不同的编码和风格。Croll 解决了一些常见的编程挑战，如寻找斐波那契或质数，以适应各种各样的著名作家的风格。有些解决方案，像海明威的第一个例子，是非常简单的；然而，像伊塔洛·卡尔维诺这样的一些人完全颠覆了函数式编程。

通常，代码中的注释更多地是在讲述一个故事，而不是简单地描述代码行。一些作者完全抛弃了形式，将代码和注释结合成一串思想。例如，垮掉的一代作家杰克·凯鲁亚克对阶乘的解决方案，将整个程序写成一行。

```
/*...the only numbers for me are the mad ones, take forty-three like a steam engine with a talky caboose at the end*/ (n = 43), /*and that lanky fellow in a cocked fedora*/ (r = 1); /*then back to our number, our mad number, mad to become one*/ while (n > 1) /*mad to descend*/ n--, /*mad to multiply*/ (r = r * n); /*and at the end, you see the blue center-light pop, and everybody goes 1.4050061177528801e+51…*/
r; 
```

Enter fullscreen mode Exit fullscreen mode

它是如此的不可靠，以至于我不得不关闭我的漂亮的 VS 代码扩展来正确地保存代码片段。你还会注意到，他完全忽略了任何阶乘，除了数字 43。

Croll 做了一项出色的工作，为每位作者提供了一份简短的简历，以建立一个给定解决方案背后的推理。即使你几乎不懂 Javascript，你也会对 25 位杰出的作家有更好的理解。如果你和我一样，这本书可能会让你有一种想要深入了解一些作者真实文学作品的冲动。

当大多数编写代码的人都是技术工程类型的时候，让一个艺术家来处理“编程语言”中的“语言”部分确实提供了一种全新的编码风格。这并不是说，在开始下一个项目时，你应该效仿这些作者颠倒的本质，但它肯定会促使你跳出框框思考。

总的来说，这本书非常有趣，值得你花时间去读。

你读过这本书吗？你的想法是什么？