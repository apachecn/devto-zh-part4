# TIL: RegExp 全局标志没有返回您期望的结果

> 原文：<https://dev.to/martinwheeler/til-regexp-global-flag-doesn-t-return-what-you-d-expect-3cc5>

```
const myRegExp = /[0-9]+ [a-z]+ [a-z]+/gi
const myString = '10 Banana Road'

const doesItMatch = myRegExp.test(myString)
// true
const itShouldMatch = myRegExp.test(myString)
// false 
```

等等，什么？！？为什么是`true`然后是`false`？这是怎么回事？

答案并不像我最初想的那样直观。这是我在 stackoverflow 上找到的:[https://stackoverflow.com/a/1520853](https://stackoverflow.com/a/1520853)

本质上，全球国旗是导致这一问题的原因。它似乎有一些我不知道的内部状态。移除全局标志后，测试都将返回`true`。