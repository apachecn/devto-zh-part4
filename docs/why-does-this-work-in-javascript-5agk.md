# 为什么这在 javascript 中行得通？

> 原文：<https://dev.to/sobiodarlington/why-does-this-work-in-javascript-5agk>

我刚刚碰到了这个。有人知道这为什么有效吗？

```
const a = {b: 2}; 

console.log( a[['b']] );

// Result: 2 
```

Enter fullscreen mode Exit fullscreen mode