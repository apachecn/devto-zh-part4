# 为什么两个小 for 循环比一个大 for 循环跑得快？

> 原文：<https://dev.to/nijeesh4all/why-two-small-for-loops-run-faster-than-a-big-one-49pn>

*像我五岁一样解释*为什么两个小 for 循环比一个大 for 循环跑得快？

```
s = Date.now()
for(let i=0;i<1e10;i++) { i + 100 }
for(let i=0;i<1e10;i++) { i + 100 }
console.log(Date.now() - s)
// => 37s

s = Date.now()
for(let i=0;i<2e10;i++) { i + 100 }
console.log(Date.now() - s)
// => 38s 
```

尤其是对于像 JS 这样不是多线程的语言