# Bourbon Saas 在 Vue CLI 项目中使用 Bourbon (Sass)

> 原文：<https://dev.to/tbeseda/use-bourbon-sass-in-a-vue-cli-project-1i2d>

首先，确保将 Sass 安装为一个开发依赖项(如果在使用 CLI 创建项目时没有选择 Sass):

`npm install -D sass-loader sass`

安装波旁和波旁-齐:

T0】

配置`css.loaderOptions.sass.includePaths`以使用正确的路径:

```
//vue.config.js

const bourbon = require('bourbon');
const neat = require('bourbon-neat');

const bourbonPaths = [...bourbon.includePaths, ...neat.includePaths];

module.exports = {
  css: {
    loaderOptions: {
      sass: {
        includePaths: bourbonPaths,
      },
    },
  },
}; 
```

Enter fullscreen mode Exit fullscreen mode

进口波旁威士忌和纯威士忌(我在`src/scss/site.scss`做过这个):

```
@import '_bourbon'; // leading underscore required*
@import 'neat'; 
```

Enter fullscreen mode Exit fullscreen mode

将`site.css`导入您的应用:

```
<template>
  ...
</template>

<style lang="scss">
@import '~@/scss/site';

body {
  background-color: tint(black, 80%); // use a bourbon function
}
.my-container {
  @include grid-container; // use a neat grid
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

*我不会用 Sass 来理解为什么需要前导下划线。如果没有它，就会出现编译错误，因为它试图加载 bourbon 包的主 Javascript 文件，而不是 CSS。任何见解将不胜感激。