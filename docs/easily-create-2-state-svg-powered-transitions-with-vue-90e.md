# 利用 vue 轻松创建双态 SVG 供电转换

> 原文：<https://dev.to/kaos/easily-create-2-state-svg-powered-transitions-with-vue-90e>

有了库 [`vue-svg-transition`](https://github.com/kai-oswald/vue-svg-transition) 你可以很容易地将微型动画添加到你的 vue 应用中。

那么这个库是如何工作的呢？基本上，一个 SVG 缩小，而另一个 SVG 同时放大。利用正确的时机，这在这两者之间创造了一个非常好的过渡。

[![Animations with vue-svg-transition](img/776d01a1614ddbfc690945d0bbda8b21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YvwYH2Ge--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://raw.githubusercontent.com/kai-oswald/vue-svg-transition/master/assets/demo.gif)

## 安装

```
npm i vue-svg-transition 
```

```
import Vue from 'vue';
import SvgTransition from 'vue-svg-transition';

Vue.use(SvgTransition); 
```

## 用法

使用 [`vue-svg-loader`](https://www.npmjs.com/package/vue-svg-loader) ，我们可以导入 SVG 并像使用 vue 组件一样使用它们。
这导致了非常简洁的语法。

```
<template>
    
        <MyIcon slot="initial" />
        <MyOtherIcon />
    
</template>

<script>
import MyIcon from "./assets/MyIcon.svg";
import MyOtherIcon from "./assets/MyOtherIcon.svg";

export default {
    components: {
        MyIcon,
        MyOtherIcon
    }
}
</script> 
```

这已经是所有的魔法了。默认情况下，过渡在`click`触发，但你也可以用`trigger`道具通过`hover`或`none`。

```

<!-- your icons -->
 
```

您也可以使用`ref`以编程方式触发转换。

```

<!-- your icons -->


<script>
export default {
    mounted() {
        this.$refs.transition.performTransition();
    }
}
</script> 
```

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[【kai-Oswald】](https://github.com/kai-oswald)/[视图 svg 过渡】](https://github.com/kai-oswald/vue-svg-transition)

### 创建双态 SVG 供电的转换

<article class="markdown-body entry-content container-lg" itemprop="text">

# 视图 svg 过渡

> 创建双态、SVG 支持的动画图标

[![Demo](img/776d01a1614ddbfc690945d0bbda8b21.png)](https://raw.githubusercontent.com/kai-oswald/vue-svg-transition/master//assets/demo.gif)

[Codesandbox 演示](https://codesandbox.io/s/6v20q76xwr)

受[图标过渡生成器](https://blog.nucleoapp.com/create-2-state-svg-powered-animated-icons-76ed19160a7e)的启发

[![npm version badge](img/177d8fb4a18d2e006c2db8011f22981a.png)](https://camo.githubusercontent.com/3143e668c42a175ee67e24d23e0ceae1f05a7e7e/68747470733a2f2f696d672e736869656c64732e696f2f6e706d2f762f7675652d7376672d7472616e736974696f6e2e737667)

## 快速启动

```
npm install --save vue-svg-transition 
```

```
import Vue from 'vue';
import SvgTransition from 'vue-svg-transition';
Vue.use(SvgTransition);
```

## 模板示例

建议使用 [`vue-svg-loader`](https://www.npmjs.com/package/vue-svg-loader) ，这样我们可以从外部文件导入我们的 SVG，但是也可以使用内嵌 SVG。

```
<template&gt
    
        <MyIcon slot="initial" />
        <MyOtherIcon />
    
</template>

<script>
import MyIcon from "./assets/MyIcon.svg";
import MyOtherIcon from "./assets/MyOtherIcon.svg";

export default {
 components: {
 MyIcon,
 MyOtherIcon
 }
 data() {
 return {
 size: {
 width: 48,
 height: 48
 }
 }
 }
}
</script>
```

通过`ref`编程触发

```


[View on GitHub](https://github.com/kai-oswald/vue-svg-transition)