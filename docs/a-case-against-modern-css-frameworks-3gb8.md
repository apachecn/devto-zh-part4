# 反对现代 CSS 框架(组件库)的案例

> 原文：<https://dev.to/michi/a-case-against-modern-css-frameworks-3gb8>

> 最初发布于[michaelzanggl.com](https://michaelzanggl.com/articles/a-case-against-component-libraries)。订阅[我的简讯](https://michaelzanggl.com/)永远不要错过新内容。

CSS 框架是快速启动项目的好方法。他们让你专注于你的应用而不是设计。如果你不是设计师，并且你的团队中没有设计师，这一点尤其重要。

但是，随着时间的推移，你需要定制，这就是对特异性的斗争，对框架的斗争的开始。

这当然不是什么新鲜事，人们已经为此争论了很长时间。

事实是，这在以前并不是一个大问题。当然，你可能需要一些`!important`和其他丑陋的东西，但至少知道在哪里添加这些覆盖并不困难。

如今，一些你想覆盖的小类存在于框架的一个组件中，被隔离开来。类名是自动生成的，曾经很容易修改的东西变得难以置信的乏味。

我发现自己在做这件事

```
.list-tile:hover >>> .v-list__tile {
    background: transparent;
} 
```

Enter fullscreen mode Exit fullscreen mode

`v-list__tile`成为 Vuetify 组件中的一个类。如果这个内部使用的类名被更改，升级指南中会提到吗？

另一件事是现代 CSS 框架的 API。Bootstrap 的 API 和 CSS 类非常容易记住。但是今天，每当我使用这样的框架进行开发时，我都必须随时打开 Vuetify、Material UI 或其他任何东西的文档，准备为一个简单的对话框复制粘贴 50 行代码。我不可能记住所有的道具、场景、事件等等。每种成分。老实说，这整个复制粘贴动作也带走了乐趣。

自从我开始使用低级的实用优先的 CSS 框架 [Tailwind](https://tailwindcss.com/) 以来，使用其他任何东西都感觉没有效率。我已经写了一篇博文[在这里](https://michaelzanggl.com/articles/utility-first-css/)。当然，首先你需要看文档，但是它的 API 是可预测的并且容易记忆，也有自动完成的工具。最棒的是，它完全是框架不可知的。不需要学习新的 API，因为你决定在下一个项目中使用 React over Vue。

你对现代 CSS 框架有什么想法？你喜欢使用它们吗？