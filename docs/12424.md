# 你知道 JavaScript 的这些奇怪的部分吗？

> 原文：<https://dev.to/lqj/do-you-know-these-weird-parts-of-javascript-48pg>

什么？！你说 JavaScript 很奇怪？拜托老兄，我已经用了好几年了，这是我见过的最好的语言。

[![What?!](img/1199aab32ea1323732210119232a0146.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BfsEWONn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media3.giphy.com/media/VeB9ieebylsaN5Jw8p/giphy.gif%3Fcid%3D790b76115d32e3a27961654a73194541%26rid%3Dgiphy.gif)

大家喝杯咖啡，看看下次面试能不能问一些这样的问题，呵呵。

开始了:

```
> 0.1 + 0.2
0.30000000000000004 
```

Enter fullscreen mode Exit fullscreen mode

```
> 1 + “23” - 10
“113” 
```

Enter fullscreen mode Exit fullscreen mode

```
> null == undefined
true

> NaN == NaN
false 
```

Enter fullscreen mode Exit fullscreen mode

```
> typeof null
"object"

> typeof NaN
"number" 
```

Enter fullscreen mode Exit fullscreen mode

```
> ['1', '7', '11'].map(parseInt)
(3) [1, NaN, 3] 
```

Enter fullscreen mode Exit fullscreen mode

```
> function foo() {
    return 
    {
      foo: 'bar'
    }
  }
foo()
< undefined

> function bar() {
  return {
    foo: 'bar'
  }
}
bar()
> {foo: "bar"} 
```

Enter fullscreen mode Exit fullscreen mode

```
> [] + []
""

> {} + {}
"[object Object][object Object]"

> [] + {}
"[object Object]"

> {} + []
0

> +!+[]
1 
```

Enter fullscreen mode Exit fullscreen mode

```
3 > 2
true

3 > 2 > 1
false 
```

Enter fullscreen mode Exit fullscreen mode

```
> function Test() {
}()
Uncaught SyntaxError: Unexpected token )

> var test = function() {}
undefined 
```

Enter fullscreen mode Exit fullscreen mode

[![OMG JS](img/62a671429381846aa98e62acd768dd53.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BrEgiRLz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media3.giphy.com/media/V0IdVIIW1y5d6/giphy.gif%3Fcid%3D790b76115d32e09a59426e437366085e%26rid%3Dgiphy.gif)

## 参考文献:

[https://blog . mge chev . com/2013/02/22/JavaScript-the-verged-parts](https://blog.mgechev.com/2013/02/22/javascript-the-weird-parts)

[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Operators/Operator _ Precedence](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)

[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Guide/Expressions _ and _ Operators](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators)

[https://wtfjs.com/](https://wtfjs.com/)