# 复古组件，有人吗？满足<blink>反应’99</blink>

> 原文：<https://dev.to/johnridesabike/retro-components-anyone-meet-blink-react-99-blink-492f>

你是否曾经向往过网络的美好时光，那时网页大多是动画头骨和“正在构建中”的 gif。那时，你不需要花哨的 CSS，只需要许多嵌套的`<TABLE>`和`<FONT>`标签。有一些特别重要的信息？没问题，用`<BLINK>`就行了。那是一段简单的时光。

自 90 年代以来，网站建设已经发生了变化。这些天，酷小孩在用 React，或 Vue，或任何酷小孩现在用的东西。实际上似乎没有人再写 HTML 了。但是，如果我们可以在不放弃现代框架的情况下让时光倒流，那会怎么样呢？如果我们能两全其美呢？

认识一下我的最新项目: **[React '99。使用 Reason 和 React 2077 调用 1999 年的 HTML。](https://johnridesa.bike/react-99/)T3】**

这是一个推理反应组件库，模拟了过去几千年的 HTML。来看看:

```
open React99;
module SuperCoolHeader = {
  [@react.component]
  let make = () =>
    <CENTER>
      <BLINK>
        <FONT color="red" size="7">
          {React.string("Get up and slam!")}
        </FONT>
      </BLINK>
    </CENTER>;
}; 
```

Enter fullscreen mode Exit fullscreen mode

(不包括动画头骨和建筑 gif。)