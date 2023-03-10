# 带 Flexbox 的粘性页脚

> 原文：<https://dev.to/yas46/sticky-footer-with-flexbox-2128>

Flexbox 是一种在水平或垂直堆栈中定位元素的方法，根据我的经验，这是在页面底部放置页脚的最佳方法。

```
<body>
  <nav></nav>
  <main></main>
  <footer></footer>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

要使用 Flexbox 将页脚固定在页面或可视窗口的底部，您需要:

1.  将页脚的父元素的 CSS `display`值设置为`flex`，将`flex-direction`值设置为`column`。

    ```
    body {
      display: flex;
      flex-direction: column;
    } 
    ```

    `display: flex;`使`<body>`元素成为灵活的框布局模块，并为其所有直接子元素启用 flex 上下文。

    `flex-direction: column;`将所有子元素从上到下垂直堆叠。

2.  将页脚的同级可扩展元素的 CSS `flex`值设置为:

    *   `1` -将页脚贴在可视屏幕的底部
    *   `1 0 auto` -将页脚贴在页面底部

    ```
    main {
      flex: 1 0 auto;
    } 
    ```

    `flex: 1;`定义元素在必要时增长的能力。如果 flex 容器中有可用空间，项目应该会占用它。这将推动`<footer>`向下并卡在底部。

常见问题:

*   主要内容元素不会扩展到占据所有可用空间。
    *   将`min-height: auto;`应用到`<main>`元素将解决这个问题。
    *   另外，确保 body 元素的`height`被设置为`100%`。

```
body {
  display: flex;
  flex-direction: column;
  height: 100%;
}

main {
  flex: 1 0 auto;
  min-height: auto;
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你是 flexbox 的新手，想了解更多并理解它是如何工作的，这本全面的 CSS flexbox 布局指南解释了关于这种方法的一切。