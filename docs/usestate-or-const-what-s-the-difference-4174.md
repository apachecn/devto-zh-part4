# useState 或 const:有什么区别？

> 原文：<https://dev.to/wolfhoundjesse/usestate-or-const-what-s-the-difference-4174>

这是给你的快餐。这两个声明有什么区别？

```
const Tuesday = () => {
  const [needsTacos] = React.useState(true); // 1
  const needsTacos = true; // 2
  return <marquee>Of course we need tacos!</marquee>
} 
```

Enter fullscreen mode Exit fullscreen mode