# 破坏性分配的反向价值

> 原文：<https://dev.to/emersonbroga/invertendo-valores-com-o-destructuring-assignment-4aj3>

[![Invertendo valores com o Destructuring Assignment](img/2137508965ed4d3049b590840b2b0e4a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--U0xyW53k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1jshnolsigxcom49jsq8.jpg)

使用 destructuring 可以轻松地反转多个值。

假设你有`let a = 1`和`let b = 2`，你很容易就可以
反转数值，使得`a = 2`和`b = 1`只使用:

```
let a = 1;
let b = 2;
[a, b] = [b, a];
// a será 2
// b será 1 
```

=)

[参加我们的开发者名单](https://emersonbroga.com/e/participe/)
[【学习 es 6】](https://amzn.to/2J4XnLg)[【查看 instagram 上的提示](https://www.instagram.com/emersonbrogadev/)

#javascript #react #node #es6