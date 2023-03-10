# 如何重置 CSS 属性？

> 原文：<https://dev.to/dzhavat/how-do-you-reset-css-properties-2g4f>

我想知道你通常如何将 CSS 属性重置为默认值？你使用给定属性的初始值还是 [`initial`](https://developer.mozilla.org/en-US/docs/Web/CSS/initial_value) 关键字？

* * *

示例:

```
main {
  padding-bottom: 1rem;
}

main:hover {
  padding-bottom: 0;

  /* vs */

  padding-bottom: initial;
} 
```

Enter fullscreen mode Exit fullscreen mode

我这么问是因为我大多使用初始值(上面例子中的`0`)。正因为如此，我有时会忘记初始值是什么，需要检查它。这通常没什么大不了的，但我刚刚有了一个 VS 代码扩展的想法，它显示了悬停的价值。对你会有帮助吗？

[![](img/f7510220e5a6e88e3ba77c77f9e3de7a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--r_80cr06--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/p2hurliljvcpp19mmumc.jpg)