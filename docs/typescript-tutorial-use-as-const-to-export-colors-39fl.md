# TypeScript 教程-使用“as const”导出颜色！

> 原文：<https://dev.to/aexol/typescript-tutorial-use-as-const-to-export-colors-39fl>

您好，这是高级 typescript 教程系列的第四篇文章。今天我将讲述
的一些小用法

```
as const 
```

Enter fullscreen mode Exit fullscreen mode

假设您定义了一些颜色:

```
const Colors = {
  cherry: "#F9193F",
  mars: "#F19037",
  meteor: "#FFE3C8"
} 
```

Enter fullscreen mode Exit fullscreen mode

当你试图在其他地方使用它们时，TypeScript 会告诉你`Colors.cherry`的类型是`string`。如果我们有 3 种颜色这是可以的，但是如果我们有 50 种颜色有奇怪的名字呢？我们可以使用新的打字稿`as const`或`<const>` :

```
const Colors = {
  cherry: "#F9193F",
  mars: "#F19037",
  meteor: "#FFE3C8"
} as const 
```

Enter fullscreen mode Exit fullscreen mode

现在如果我们键入`Colors.cherry`，typescript 将显示樱桃色的真实值，即`#F9193F`！