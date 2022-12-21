# 用于网格布局的简单、轻量和灵活的 Vue.js 组件

> 原文：<https://dev.to/1000ch/simple-light-weight-and-flexible-vue-js-component-for-grid-layout-3i20>

刚刚发布的 [1000ch/vue-grd](https://github.com/1000ch/vue-grd) ，Vue.js 组件，提供灵活的网格布局系统，使用简单。

## 怎么用？

可以通过`npm`安装。

```
npm install --save vue-grd 
```

导入并注册为组件后，可以使用`<vue-grid>`和`<vue-cell>`。

```
<template>
  <vue-grid align="stretch" justify="start">
    <vue-cell width="fill">fill</vue-cell>
    <vue-cell width="3of12">3of12</vue-cell>
    <vue-cell width="3of12">3of12</vue-cell>
  </vue-grid>
</template>

<script>
import { VueGrid, VueCell } from 'vue-grd';

export default {
  components: {
    VueGrid,
    VueCell
  }
};
</script> 
```

您也可以将它们注册为全局组件。

```
import Vue from 'vue';
import VueGrd from 'vue-grd';

Vue.use(VueGrd);
// or
Vue.component('vue-grid', VueGrd.VueGrid);
Vue.component('vue-cell', VueGrd.VueCell); 
```

如果不使用`npm`，可以通过 jsDelivr 加载。

```
<script src="https://cdn.jsdelivr.net/npm/vue@2.6.10/dist/vue.js"></script>
<script src="https://cdn.jsdelivr.net/npm/vue-grd@1.1.0/dist/vue-grd.js"></script>
<script>Vue.use(VueGrd);</script> 
```

## 配置

它们有一些属性来对齐和调整集合宽度。

### `<vue-grid align>`

*   顶端:将项目拉到顶端
*   中间:将项目拉到中间
*   底部:将项目拉到底部
*   拉伸:拉伸项目
*   基线:将项目拉至基线

### `<vue-grid justify>`

*   开始:要开始的布局项目
*   居中:将项目布局居中
*   结束:要结束的布局项目
*   between:在项目之间添加空格
*   周围:在项目周围添加空间

### `<vue-cell width>`

*   填充:将项目宽度设置为左侧
*   1of12:将项目宽度设置为 8.3%
*   2of12:将项目宽度设置为 16.7%
*   3of12:将项目宽度设置为 25%
*   4of12:将项目宽度设置为 33%
*   5of12:将项目宽度设置为 41.7%
*   6of12:将项目宽度设置为 50%
*   7of12:将项目宽度设置为 58.3%
*   8of12:将项目宽度设置为 66.7%
*   9/12:将项目宽度设置为 75%
*   10/12:将项目宽度设置为 83.3%
*   11of12:将项目宽度设置为 91.7%
*   12of12:将项目宽度设置为 100%

## 现场演示

你可以从[这里](https://1000ch.github.io/vue-grd/)试试。