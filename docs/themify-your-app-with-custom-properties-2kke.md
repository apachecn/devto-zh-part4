# 使用自定义属性对您的应用进行主题化

> 原文：<https://dev.to/bpetetot/themify-your-app-with-custom-properties-2kke>

我目前的副业项目是一个开放的 SaaS 平台，管理称为[会议厅](https://github.com/bpetetot/conference-hall)的论文征集。当我开始的时候，我想根据用户的角色有不同的主题。如果你是一个演讲者，一个蓝色的主题，一个红色的主题给组织者。

该应用程序目前是用 React 构建的，有很多方法可以使用提供者、钩子、样式组件、css-in-js 等来构建主题化系统。但我想保持简单，贴近它应该有的样子:“风格”。于是我决定使用 CSS 的新力量之一:[“自定义属性”](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)(或者 CSS 变量)及其继承体系。

这里有一个[现场演示](https://codepen.io/bpetetot/pen/bPzeJZ)和下面的解释。

### 用自定义属性进行主题化

首先声明一个默认的主题，该主题需要所有的自定义属性。

```
.default-theme {
  --background-color: #FFF;
  --font-color: #000;
} 
```

Enter fullscreen mode Exit fullscreen mode

将默认主题类设置为`body`(或其他根元素)。该元素及其子元素将可以访问这些变量。

```
<body class="default-theme">
...
</body> 
```

Enter fullscreen mode Exit fullscreen mode

这些变量可以通过`var()`函数在你的 CSS 中使用。

```
body {
  background: var(--background-color);
  color: var(--font-color);
} 
```

Enter fullscreen mode Exit fullscreen mode

> **那么，我们应该怎么改变 app 的主题呢？**

使用自定义属性声明一个新的 CSS 类，以在新主题中重写，然后将其应用于元素。由于自定义属性的继承，你可以在你的应用程序中使用黑暗模式。

```
.dark-theme {
  --background-color: #000;
  --font-color: #FFF;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
<body class="default-theme dark-theme">
...
</body> 
```

Enter fullscreen mode Exit fullscreen mode

### 将主题应用到子组件

如果您在所有子组件中使用自定义属性(`var()`)，主题将自动应用。

但是你可以独立于你的应用程序构建你的组件(用 Storybook，DocZ，等等)。)并且主题化系统暗示你的一些组件应该使用这些自定义属性。因此，如果您想拥有一个无需声明 CSS 变量就能正确呈现的组件，您可以:

*   通过颜色道具将 CSS 变量传递给组件；
*   将带有 CSS 变量的类或样式传递给组件；
*   或者在其中使用带有回退值的 CSS 变量(`var`函数的第二个参数):

```
.my-component {
  background: var(--background-color, #DDD), 
} 
```

Enter fullscreen mode Exit fullscreen mode

### 浏览器兼容性

除了 Internet Explorer，大多数浏览器已经支持自定义属性。但是如果你想为 IE 保留一个后备，你可以在 CSS 类中添加一个额外的属性。当然，主题切换在带有自定义属性的 IE 上是不起作用的。

```
.my-component {
  background: #DDD, 
  background: var(--background-color, #DDD), 
} 
```

Enter fullscreen mode Exit fullscreen mode

我的应用程序[会议厅](https://github.com/bpetetot/conference-hall)的所有主题系统都完全基于自定义属性，并且工作得非常好。希望对你有帮助。

**参见 codepen 中的现场演示:**
[https://codepen.io/bpetetot/embed/bPzeJZ?height=600&default-tab=result&embed-version=2](https://codepen.io/bpetetot/embed/bPzeJZ?height=600&default-tab=result&embed-version=2)