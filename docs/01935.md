# 使用 Vuenime 在 Vue 中轻松制作数据动画

> 原文：<https://dev.to/denisinvader/easy-data-animations-in-vue-with-vuenime-1b1g>

连续几次，我都在用不能进行 CSS 转换的动画来创建数据可视化。每次我都使用 [Animejs](https://animejs.com) 从头开始实现所有的动画逻辑。
除了最后一个，当时我意识到`watch`属性和`anime`调用的组合可以提供一种简单的方法来创建声明性动画的包装组件。

我写的。

然后我才发现，npm 里没有这样的包。好吧，有 [vue-anime](https://www.npmjs.com/package/vue-animejs) 但是它只是给 vue 原型增加了`$anime`并提供了`v-anime`指令(仅限于 DOM 节点；因为 JSX 的缘故，我根本不用指令。

我做了一个 npm 包。

坦率地说，这是一个寻找空闲时间、决定如何提供文档、哪些特性是发布所必需的等等的漫长过程。但是我关掉了完美主义，这就是了！

希望你觉得这个包有用，给我一些反馈！

[NPM 包](https://www.npmjs.com/package/vuenime)
[GitHub 回购](https://github.com/denisinvader/vuenime)
[故事书](//denisinvader.github.io/vuenime/)