# TypeScript 上 Vue.js 组件的样板文件

> 原文：<https://dev.to/room_js/boilerplate-for-vue-js-component-on-typescript-351f>

嗨，德夫斯！这个帖子可能会引起前端人员的兴趣，特别是那些处理 Vue.js 的人。这个标题很容易理解，我想分享一下我最近创建的 Vue.js 组件项目的启动程序。

它包含各种工具，如:

*   `Sass`用于造型
*   `Jest`用于单元测试
*   `ESLint`对林挺来说
*   `Rollup`用于建筑
*   `Storybook`用于呈现可视组件

所以，如果 TypeScript 和 Vue.js 是你的东西请随意克隆/分叉/贡献！

GitHub repo 的链接:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[room-js](https://github.com/room-js)/[typescript-vue-component-boilerplate](https://github.com/room-js/typescript-vue-component-boilerplate)

### 使用 TypeScript 创建自定义 Vue.js 组件的样板文件

<article class="markdown-body entry-content container-lg" itemprop="text">

# Vue.js 组件样板文件(类型脚本)

这个样板文件使用了 [vue-class-component](https://www.npmjs.com/package/vue-class-component) 和 [vue-property-decorator](https://www.npmjs.com/package/vue-property-decorator) 包。它们允许使用类和装饰器来构建 Vue 组件:

```
import { Vue, Component, Prop } from 'vue-property-decorator';

@Component
class MyVueComponent extends Vue {
  // component props, methods and lifecycle methods
}
```

### 特征

*   `Storybook`用于预览和构建组件- `npm run storybook`
*   `Jest`用于测试- `npm test`
*   `ESLint`对于林挺- `npm run lint`

### 构建包

```
npm run dist 
```

构建将被放置到`./lib`文件夹中，并准备在 npm 注册表上发布

</article>

[View on GitHub](https://github.com/room-js/typescript-vue-component-boilerplate)