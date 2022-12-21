# 创建 React 自定义元素包装生成器

> 原文：<https://dev.to/gilfink/creating-a-react-custom-element-wrapper-generator-2f8o>

[![](img/7d44c8c97fd31c74df37a1aca3ab7059.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cy2D8LVT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/192/1%2AN-eo9u-zmpYdKqJITKle1Q.jpeg) 

<figcaption>网页组件</figcaption>

几天前我发表了一篇名为“[在自定义元素](https://dev.to/gilfink/wrapping-react-components-inside-custom-elements-3fph)中包装 React 组件”的文章。这篇文章解释了如何将 React 组件包装在自定义元素中。在这篇文章的最后，我写道，我可能会想到一些方法来实现相同的功能，但这些方法是自动的，更通用的。在这篇文章中，我将解释其中的一个观点。

> 注意:如果你没有看过之前的[帖子](https://dev.to/gilfink/wrapping-react-components-inside-custom-elements-3fph)，我建议你去看看，了解一下这篇帖子的背景。

#### 观察道具

首先，我假设大多数 React 开发人员使用 prop-types 库在他们的 React 组件上声明 React prop 类型。使用这个假设，我们可以使用属性类型来理解组件正在接收的所有属性的名称和类型，并为定制元素生成观察到的属性数组。我们将创建一个 **getObserved** 辅助函数，它将负责从属性类型中提取观察到的属性的名称: