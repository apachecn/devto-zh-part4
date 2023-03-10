# 想象的语言特征

> 原文：<https://dev.to/awwsmm/imaginary-language-features-4k3>

梅根在一份`switch`声明中对`any`案例提出了这个非常有趣的建议(与`default`略有不同)...

[![nektro](img/cb4b59d5a2101f651752b1c1936faa81.png)](/nektro) [## 语言建议:“任意”转换情况

### Meghan(她/她)Jul 18 ' 191min read

#discuss](/nektro/language-proposal-the-any-switch-case-5a5h)

...让我思考:用什么想象的语言特性来编程会很酷？作为对梅根提议的回应，我想到了一个`template case`:

[![awwsmm profile image](img/db6dae06e86ae6f78793b649b6380576.png) ](/awwsmm) [ Andrew (he/him) ](/awwsmm) • [<time datetime="2019-07-18T07:44:46Z"> Jul 18 '19 </time> • Edited on <time datetime="2019-07-18T07:49:15Z">Jul 18</time>](https://dev.to/awwsmm/comment/d5o7) 

一个`template`案例呢？

```
switch (number) {

  template:
      before: // do something before
       after: // do something after

  case 1: // ...

  template case 2: // ...

  template case 3: // ...

  case 4: // ...

  default: // ...

} 
```

Enter fullscreen mode Exit fullscreen mode

`template`只适用于特定的案例子集，而`template`的定义可以扩展到异常处理，等等。

你对语言特性有什么好的想法？