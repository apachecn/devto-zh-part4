# 用纯 CSS 制作“细节”标签动画

> 原文：<https://dev.to/pixmy/animate-details-tag-with-pure-css-52j7>

你好！

看到一个帖子，标题是“HTML 能做到那样？”在这里，dev 用户添加了

<details>标签。下面是 W3 学校的一个小描述:</details>

*<细节>标签指定了用户可以按需查看或隐藏的附加细节。
<details>标签可用于创建用户可以打开和关闭的交互式小部件。任何种类的内容都可以放在标签中。
除非设置了 open 属性，否则< details >元素的内容不应该可见。*

这里的问题是，

<details>标签本身并不用户友好，我建议给它添加一个非常小的动画，这样用户就能确切地知道在点击细节摘要后会出现什么内容。</details>

无动画的详细信息标签:

[https://codepen.io/Pixmy/embed/xoXaXm?height=600&default-tab=result&embed-version=2](https://codepen.io/Pixmy/embed/xoXaXm?height=600&default-tab=result&embed-version=2)

为了给它添加动画，我们首先需要创建 CSS 关键帧。在我们的动画中，你可以使用很多 css 规则，但是为了简单起见，我们只使用不透明度和左边空白。

```
@keyframes open {
  0% {opacity: 0; margin-left: -20px}
  100% {opacity: 1; margin-left: 0px}
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦动画准备好了，让我们用[open]选择器将它分配给我们的

<details>标签，就像这样:</details>

```
details[open] summary ~ * {
  animation: open .5s ease-in-out;
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以在这里看到结果:

[https://codepen.io/Pixmy/embed/RzLYer?height=600&default-tab=result&embed-version=2](https://codepen.io/Pixmy/embed/RzLYer?height=600&default-tab=result&embed-version=2)

这是一个超级简单的动画，你可以添加到你的项目中，效果非常好！