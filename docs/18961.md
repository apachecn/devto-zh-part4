# 带有 CSS 变量的黑色主题

> 原文：<https://dev.to/phuchieu/dark-theme-with-css-variable-1fjo>

如今，越来越多的应用程序和操作系统将适应新的趋势:黑暗主题和 web 应用程序也不例外。深色主题并不是 web 应用程序的必要特性，但是拥有它还是不错的。根据用户的偏好给他们更多的选择总是有助于你的应用程序更有吸引力。今天我将向你展示一些在你的应用程序中应用黑暗主题的方法。

## 解决方案 1:使用 css 嵌套

假设您当前的 web 应用程序只支持 light 主题，现在您想添加一个允许用户在运行时更改应用程序主题的新特性。所以最简单的方法是我们为黑暗主题定义了一个新的类，将那个类应用到根元素(`<html>`或`<body>`)。因为应用程序的主题对于整个应用程序必须是一致的，所以为了确保应用程序的每个元素都遵循所选的主题，我们必须将该类设置为根元素。

在我们有一个黑暗主题的课程后，我们将为里面的每个孩子定制外观和感觉。2 我们需要记住的最重要的 CSS 属性是`color`和`background-color`。除此之外，我们还需要根据应用程序的实现来处理其他 CSS 属性。

类似这样的东西

```
/* Default css for light theme */
body {
  background-color: white;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  transition: all linear 1s;
}
.panel {
  background-color: lightblue;
}
label{
  color:#0d47a1;
}
/* Custom css for dark theme */
[data-theme=dark-theme] {
  background-color:gray ;
}
[data-theme=dark-theme] .panel {
  background-color: darkgray;
}
[data-theme=dark-theme] label {
  color: rgb(245,245,245);
}
```

Live demo [https://codepen.io/phuchieuct93abc/embed/QXwYzK/?height=600&default-tab=css,result&embed-version=2](https://codepen.io/phuchieuct93abc/embed/QXwYzK/?height=600&default-tab=css,result&embed-version=2)

这种方式工作完美，但是，它仍然有一个很大的缺点是，我们需要为每个组件复制 CSS 代码，我们想自定义之间的光明主题和黑暗主题。在上面的例子中，你可以看到我们需要为标签和面板重新定义 CSS 两次，更糟糕的是我们还需要多次重复`[data-theme=dark-theme]`。为了摆脱这个缺点，我想向你介绍另一种方法来应用黑暗主题是使用 css 变量。

## 解决方案 2: CSS 变量

这个解决方案背后的想法是，我们将定义一些 CSS 值作为一个变量，我们定义每个元素将重用 CSS 变量，而不是定义新的变量。这意味着该变量的值在运行时是灵活的。通过这种方式，我们只需要定义自定义 CSS 一次(这些 CSS 属性应该依赖于 CSS 变量)，除此之外，我们还需要定义 2 组变量:一组用于暗主题，一组用于亮主题。这种方式比使用嵌套 CSS 的解决方案 1 简单得多。

让我们看看上面例子的 CSS 变量版本:

```
/* Default css for light theme */
body {
  --backgroundColor: white;
  --panelBackgroundColor: lightblue;
  --labelColor: #0d47a1;
}
body[data-theme="dark-theme"] {
  --backgroundColor: gray;
  --panelBackgroundColor: darkgray;
  --labelColor: rgb(245, 245, 245);
}
body {
  background-color: var(--backgroundColor);
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  transition: all linear 1s;
}
.panel {
  background-color: var(--panelBackgroundColor);
}
label {
  color: var(--labelColor);
}

```

现场演示:

现场演示
[https://codepen.io/phuchieuct93abc/embed/ZdYZpd/?height=600&default-tab=css,result&embed-version=2](https://codepen.io/phuchieuct93abc/embed/ZdYZpd/?height=600&default-tab=css,result&embed-version=2)

## 最后的话

希望你对 CSS 变量有所了解，并愿意在你的下一个项目中应用它，不仅是主题改变，也是灵活的网络应用。让我们在下面的评论区知道你的感受，下次见。

也可以访问我的博客，阅读更多有趣的话题:[https://frontend . cloud access . host/dark-theme-with-CSS-variable/](https://frontend.cloudaccess.host/dark-theme-with-css-variable/)