# 发布您的第一个 NPM 包(Vue) -第 1 部分

> 原文：<https://dev.to/amroessam/publish-your-first-npm-package-vue-263h>

大家好，

又是我😊

从我的第一篇[帖子](https://dev.to/amroessam/vgauge---created-my-first-npm-package-first-post-446o)到现在已经过去了 4 个月

我意识到我的文章不是最有帮助的🤦🏽‍♂️，因为我没有详细说明我是如何创建第一个 NPM 包的。

在这篇文章中，我打算这样做😁，我还将尝试介绍如何让智能感知为 vscode 中的 vue 组件工作。

为了简单起见，我们正在制作一个非常小的可重用警报组件
，我们将使用 [rollup](https://rollupjs.org/guide/en/) 来构建

我们开始吧😎🙌

### 1。创建组件

我们将在本教程中使用 vue cli，因此如果您不熟悉它，可以从 vue 的 cli [文档](https://cli.vuejs.org/guide/)开始

打开您最喜欢的终端，将目录切换到您的项目目录

```
cd c:/projects 
```

现在使用 vue cli
创建一个新项目

```
vue create simplealertcomponent 
```

选择默认预设，等待 vue-cli 为您创建所有必要的样板文件。

将目录切换到新建的文件夹，启动编辑器，我用的是 vscode...

```
cd simplealertcomponent && code . 
```

这是你应该有的文件结构

```
.
├───public
│     favicon.ico
│     index.html
└───src
    │ App.vue
    │ main.js
    ├───assets
    │ logo.png
    └───components
      HelloWorld.vue 
```

删除 src 文件夹中的所有内容，使其为空，同时删除公共文件夹，因为我们不再需要它。

现在在 src 文件夹中创建 entry.js 文件，这将是我们的应用程序的入口点。

首先我们导入我们的组件，稍后我们将创建它

```
import component from './SimpleAlert.vue' 
```

然后，我们将它注册到 vue 实例中，如果它存在的话

```
function install(Vue) {
  if (install.installed) return
  install.installed = true
  Vue.component('SimpleAlert', component)
}

const plugin = {
  install
}

let GlobalVue = null
if (typeof window !== 'undefined') {
  GlobalVue = window.Vue
} else if (typeof global !== 'undefined') {
  GlobalVue = global.vue
}

if (GlobalVue) {
  GlobalVue.use(plugin)
}

component.install = install 
```

然后如下所示导出组件

```
export default component 
```

所以文件 entry.js 在林挺和格式化
后最终看起来像这样

```
// ./src/entry.js

import component from "./SimpleAlert.vue";

function install(Vue) {
  if (install.installed) return;
  install.installed = true;
  Vue.component("SimpleAlert", component);
}

const plugin = {
  install
};

let GlobalVue = null;
if (typeof window !== "undefined") {
  GlobalVue = window.Vue;
} else if (typeof global !== "undefined") {
  GlobalVue = global.vue;
}

if (GlobalVue) {
  GlobalVue.use(plugin);
}

component.install = install;

export default component; 
```

现在，让我们创建 vue 组件。创建一个名为 SimpleAlert.vue 的新文件

并在其中添加以下代码。

```
// ./src/SimpleAlert.vue

<script>
import { setTimeout } from "timers";
export default {
  name: "SimpleAlert",
  props: {
    message: {
      type: String,
      default: "Hello Dev.to"
    },
    timeout: {
      type: Number,
      default: 0
    }
  },
  mounted() {
    setTimeout(() => {
      alert(this.message);
    }, this.timeout);
  }
};
</script> 
```

请注意，我没有使用模板或脚本标签，因为这个组件不显示任何 html，它只是警告。

现在创建一个名为 build 的新文件夹，它应该在根目录下的 src 文件夹旁边，并在其中添加 rollup.config.js

因此项目结构如下

```
.
│
├───build
│     rollup.config.js
└───src
      entry.js
      SimpleAlert.vue 
```

在 rollup.config.js 中粘贴以下代码

```
// rollup.config.js

import vue from "rollup-plugin-vue";
import buble from "rollup-plugin-buble";
import commonjs from "rollup-plugin-commonjs";
import replace from "rollup-plugin-replace";
import uglify from "rollup-plugin-uglify-es";
import minimist from "minimist";

const argv = minimist(process.argv.slice(2));

const config = {
  input: "src/entry.js",
  output: {
    name: "SimpleAlert",
    exports: "named"
  },
  plugins: [
    external,
    replace({
      "process.env.NODE_ENV": JSON.stringify("production")
    }),
    commonjs(),
    vue({
      css: true,
      compileTemplate: true,
      template: {
        isProduction: true
      }
    }),
    buble()
  ]
};

// Only minify browser (iife) version
if (argv.format === "iife") {
  config.plugins.push(uglify());
}

export default config; 
```

现在一切就绪，让我们试着构建它，我们需要编辑我们的 package.json 来包含构建命令。

打开 package.json，用下面的
替换里面的所有内容

```
{  "name":  "simplealertcomponent",  "version":  "0.1.0",  "main":  "dist/simplealertcomponent.umd.js",  "module":  "dist/simplealertcomponent.esm.js",  "unpkg":  "dist/simplealertcomponent.min.js",  "browser":  {  "./sfc":  "src/simplealertcomponent.vue"  },  "files":  [  "dist/*",  "src/*",  "attributes.json",  "tags.json"  ],  "vetur":  {  "tags":  "tags.json",  "attributes":  "attributes.json"  },  "scripts":  {  "build":  "npm run build:unpkg & npm run build:es & npm run build:umd",  "build:umd":  "cross-env NODE_ENV=production rollup --config build/rollup.config.js --format umd --file dist/simplealertcomponent.umd.js",  "build:es":  "cross-env NODE_ENV=production rollup --config build/rollup.config.js --format es --file dist/simplealertcomponent.esm.js",  "build:unpkg":  "cross-env NODE_ENV=production rollup --config build/rollup.config.js --format iife --file dist/simplealertcomponent.min.js"  },  "devDependencies":  {  "cross-env":  "^5.2.0",  "minimist":  "^1.2.0",  "rollup":  "^1.14.4",  "rollup-plugin-buble":  "^0.19.6",  "rollup-plugin-commonjs":  "^9.3.4",  "rollup-plugin-replace":  "^2.2.0",  "rollup-plugin-uglify-es":  "0.0.1",  "rollup-plugin-vue":  "^4.7.2",  "vue":  "^2.6.10",  "vue-template-compiler":  "^2.6.10"  }  } 
```

现在打开一个终端，键入`npm i`来安装所有的开发依赖项。
完成后，键入`npm run build`，这将创建一个名为`dist`的新文件夹，其中包含我们需要推送到 NPM 的所有文件，以便其他用户使用我们的组件。

这是第 1 部分的结尾🎉🎉，其余的很快就来了😊