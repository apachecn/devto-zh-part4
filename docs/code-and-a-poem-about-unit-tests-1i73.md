# 代码和一首关于软件的诗

> 原文：<https://dev.to/joelnet/code-and-a-poem-about-unit-tests-1i73>

最好的代码，
未经测试，
不可信。

```
function alwaysLeft(left) {
  return function(right) {
    return left
  }
} 
```

测试覆盖，
给出置信度，
仍然可以被终结。

```
alwaysLeft('L')('R') === 'L' 
```

代码被滥用，
无法想象的方式，
让你厌恶。

```
function I(x) {
  return x
}

alwaysLeft(I)('L')('R') === 'R' 
```

[@joelnet](https://twitter.com/joelnet)

[![Cheers!](img/661c03d25961a6e3b1ecbe00d148b35a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--PqqJmrxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6jsy3a866frzp3u5oda0.jpg)