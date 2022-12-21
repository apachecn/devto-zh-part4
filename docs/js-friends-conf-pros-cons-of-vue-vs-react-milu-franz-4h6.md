# JS & Friends Conf:Vue vs React 的利弊-米卢弗朗兹

> 原文：<https://dev.to/aromig/js-friends-conf-pros-cons-of-vue-vs-react-milu-franz-4h6>

## 优点&缺点 Vue vs 反应- [米卢弗朗兹](https://twitter.com/milu_franz)

[![Milu Franz](img/78606e2c6ecfe8f2f0bfacab24a1fdde.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w0x35HZO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tb2et1bu8yzgd79gjtj4.jpg)

Mile Two 的 Milu Franz 做了一个很棒的演讲，比较了 Vue 和 React 的优缺点。我对使用 Vue 进行构建并不熟悉，但我对它与更流行的 React 库相比如何很感兴趣。

她展示了 Vue 是一个进步的框架，它包括用于状态管理和路由的官方包，而 React 是一个专注于 UI 开发的库，但有用于状态管理和路由的外部第三方包。她还解释了 Vue 的模板语法和 React 的格式之间的差异，前者将标记、逻辑和样式(作为单独的部分)组合在一个*单个文件组件*中，后者将标记作为 JSX 包含在 JavaScript 代码中，样式从外部导入。css 文件(除非使用 CSS-in-JS)。注意，如果需要更复杂的定制，Vue 组件也可以用与 React 组件相同的方式建模，但是模板语法是设计大多数类型组件的首选方法。

Milu 在 Vue 和 React 中构建了相同的待办事项项目，回顾了 Vue 在标记中使用插值(插入 JavaScript)的概念，以及用于状态的双向数据绑定的指令，如 *v-model* (这真的可以被描述为*魔法*)。这与需要显式定义状态(通过 useState 钩子)的 React 相比较。关键的区别是 Vue 抽象处理状态，所以你只需要直接*更新一个值(而不是使用函数来更新它), Vue 只会重新呈现改变的内容，而 React 可能会重新呈现组件的子树结构中的所有内容。*

 *解释了生命周期方法的差异以及 React 的 useEffect 挂钩。运行时性能也与两者的性能进行了比较，具体取决于用例。米卢甚至谈到了每一种方法的缺点:Vue 可能过度灵活，缺乏资源；并对其陡峭的学习曲线、缺乏原生库以及与设计者合作的困难做出反应(当需要 JSX 时，更难转化为标记)。

我从演讲中得到的结论是，React 仍然更受欢迎——它问世的时间更长，有些东西吸引着开发人员用 JavaScript 编写它，但 Vue 在成熟度、性能和用例方面正在迎头赶上。两者都完全能够帮助开发人员创建引人入胜的应用程序。很明显，我更喜欢 Vue，但是我能理解为什么开发人员的项目会被它吸引。不过，对我来说，我真的很喜欢模板标记，因为我可以更容易地可视化布局，而不需要围绕它的所有 JavaScript。我也不介意 Vue 为我如此神奇地处理状态。把它抽象化，让过程更简单。

我确信有很多其他的技术差异和观点使一个*比另一个*更好，但是我像对待其他技术一样看待它:使用你喜欢的和有效的。并保持解决方案简单。

[←回主 JS &好友文章](https://dev.to/aromig/my-first-dev-conference-javascript-friends-fh7)*