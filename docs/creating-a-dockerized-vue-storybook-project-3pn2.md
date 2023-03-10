# 创建一个 dockered vue-story book 项目

> 原文：<https://dev.to/dasdaniel/creating-a-dockerized-vue-storybook-project-3pn2>

# 让我们创建一个在 docker 中运行的基于 vue 的故事书项目

### 概述

*   设置

    *   1.创建新项目
    *   2.安装 vue 和依赖项
    *   3.安装故事书
*   建设

    *   4.添加故事书脚本`package.json`
    *   5.创建一个组件`/components/MyButton.vue`
    *   6.创建配置`/.storybook/config.js`
    *   7.创造一个故事`/stories/button.js`
*   船

    *   8.创建`dockerfile`
    *   9.创建`.dockerignore`
    *   10.创建`docker-compose.yml`

### 1。创建新项目

假设您还没有创建项目，首先创建一个新目录，并通过运行`init`命令在其中启动一个新项目。

```
npm init 
```

Enter fullscreen mode Exit fullscreen mode

这将询问一些关于项目设置的问题，如名称、版本等。你喜欢怎么填就怎么填。

### 2。安装视图

接下来，安装 vue 依赖项。通常情况下,`vue`不会作为开发依赖项安装，而其他依赖项会。我让它保持原样，尽管在这个例子中它们可能都是相同的。

```
npm install vue --save
npm install vue-loader vue-template-compiler @babel/core babel-loader babel-preset-vue --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

### 3。安装故事书

这只是又一个依赖项，但是与其他的相比，它需要一段时间来安装。

```
npm install @storybook/vue --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

### 4。添加故事书脚本

打开`package.json`文件，用
替换`"test":...`脚本

```
 "storybook":  "start-storybook -s 8086"` 
```

Enter fullscreen mode Exit fullscreen mode

这将允许我们使用`npm run storybook`来启动故事书应用程序。

它还在每次启动时使用相同的端口号，这样我们可以更容易地通过 docker 使用该端口。

### 5。创建组件

为了说明 storybook 中的一个基本组件，让我们在`/components/MyButton.vue`中创建一个按钮组件

该组件将允许改变颜色为`red`、`blue`或`green`，并有能力设置`rounded`为真或假。它使用一个槽来定义按钮文本。

```
<template>
  <button :class="className"><slot></slot></button>
</template>

<script>
export default {
  props: {
    color: {
      type: String,
      default: ''
    },
    rounded: {
      type: Boolean,
      default: false,
    }
  },
  computed: {
    className() {
      let c = ['btn']
      if (this.color.toLowerCase() === 'red') c.push('btn-red');
      if (this.color.toLowerCase() === 'blue') c.push('btn-blue');
      if (this.color.toLowerCase() === 'green') c.push('btn-green');
      if (this.rounded) c.push('btn-rounded');
      return c.join('  ')
    }
  },
};
</script>

<style scoped>
.btn {
  text-decoration: none;
  font-size: 25px;
  color: #ffffff;
  font-family: arial;
  background: linear-gradient(to bottom, hsl(224, 10%, 68%), hsl(225, 3%, 51%));
  box-shadow: 0px 1px 5px hsl(215, 8%, 16%);
  border: solid hsl(217, 10%, 74%) 1px;
  border-radius: 2px;
  padding: 15px;
  text-shadow: 0px 1px 2px #000000;
  -webkit-transition: all 0.15s ease;
  -moz-transition: all 0.15s ease;
  -o-transition: all 0.15s ease;
  transition: all 0.15s ease;
}
.btn:hover {
  opacity: 0.9;
  background: linear-gradient(to bottom, hsl(224, 10%, 68%), hsl(225, 3%, 51%));
  box-shadow: 0px 1px 2px #000000;
}
.btn.btn-rounded{
  border-radius: 8px;
}
.btn.btn-red{
  background: linear-gradient(to bottom, hsl(0, 100%, 68%), hsl(0, 63%, 51%));
  box-shadow: 0px 1px 5px hsl(0, 68%, 16%);
  border: solid hsl(0, 100%, 74%) 1px;
}
.btn.btn-red:hover{
  background: linear-gradient(to bottom, hsl(0, 100%, 62%), hsl(0, 63%, 48%));
  box-shadow: 0px 1px 2px #000000;
}
.btn.btn-blue{
  background: linear-gradient(to bottom, hsl(255, 100%, 68%), hsl(255, 63%, 51%));
  box-shadow: 0px 1px 5px hsl(255, 68%, 16%);
  border: solid hsl(255, 100%, 74%) 1px;
}
.btn.btn-blue:hover{
  background: linear-gradient(to bottom, hsl(255, 100%, 62%), hsl(255, 63%, 48%));
  box-shadow: 0px 1px 2px #000000;
}
.btn.btn-green{
  background: linear-gradient(to bottom, hsl(108, 100%, 68%), hsl(108, 63%, 51%));
  box-shadow: 0px 1px 5px hsl(108, 68%, 16%);
  border: solid hsl(108, 100%, 74%) 1px;
}
.btn.btn-green:hover{
  background: linear-gradient(to bottom, hsl(108, 100%, 62%), hsl(108, 63%, 48%));
  box-shadow: 0px 1px 2px #000000;
}
</style> 
```

Enter fullscreen mode Exit fullscreen mode

### 6。创建故事书配置

使用
创建一个新文件:`.storybook/config.js`

```
import { configure } from '@storybook/vue';

function loadStories() {
  const req = require.context('../stories', true, /\.js$/);
  req.keys().forEach(filename => req(filename));
}

configure(loadStories, module); 
```

Enter fullscreen mode Exit fullscreen mode

这将在`stories`文件夹中扫描任何`.js`文件，以便加载故事。

### 7。创造一个故事

创建一个文件`stories/button.js`

```
import { storiesOf } from '@storybook/vue';
import MyButton from '../components/MyButton';

storiesOf('Button', module)
  .add('default', () => ({
    components: { MyButton },
    template: '<my-button>Default</my-button>'
  }))
  .add('red', () => ({
    components: { MyButton },
    template: '<my-button color="red">Red</my-button>'
  }))
  .add('blue', () => ({
    components: { MyButton },
    template: '<my-button color="blue">Blue</my-button>'
  }))
  .add('green', () => ({
    components: { MyButton },
    template: '<my-button color="green">Green</my-button>'
  }))
  .add('rounded', () => ({
    components: { MyButton },
    template: '<my-button :rounded="true">Rounded</my-button>'
  })) 
```

Enter fullscreen mode Exit fullscreen mode

## 测试一下

此时，您应该能够使用
运行故事书了

```
npm run storybook 
```

Enter fullscreen mode Exit fullscreen mode

### 8。创建 docker 文件(用于 docker 图像)

这个文件定义了什么是图像指令。
该映像基于 node 版本 10，使用 alpine Linux。我用 Alpine 是因为它很小，而且有这个用途所需的所有东西。

创建`Dockerfile`并输入以下指令。

```
# Select reference image
FROM node:10-alpine

# This is optional. Sets the level of logging that you see
ENV NPM_CONFIG_LOGLEVEL warn

# Create app directory
WORKDIR /usr/src/app

# Copy project files into the docker image
COPY . .

# Install app dependencies
RUN npm set progress=false && npm install

# Make port 8086 available
EXPOSE 8086

# run storybook app
CMD ["npm", "run", "storybook"] 
```

Enter fullscreen mode Exit fullscreen mode

### 9。用跳过 node_modules。dockerignore

创建一个`.dockerignore`文件并放入下面的

```
node_modules/ 
```

Enter fullscreen mode Exit fullscreen mode

这将防止您的本地节点模块文件被复制到 docker 映像中。由于不同的环境可能需要不同的依赖版本(二进制文件)，防止 docker 复制`node_modules`将会避免令人头疼的问题，您应该经常这样做。

### 10。创建 docker 容器

创建一个`docker-compose.yml`文件并粘贴以下内容:

```
version: '3'
services:
  storybook:
    ports:
      - "8086:8086"
    build: . 
```

Enter fullscreen mode Exit fullscreen mode

这个文件使得运行容器变得更加容易，所以您不需要为`Dockerfile`运行构建和运行命令。

然后用
运行它

```
docker-compose up

# or to force building after making changes you can use --build
docker-compose up --build 
```

Enter fullscreen mode Exit fullscreen mode

第一次运行时，它会花一点时间，因为它需要下载所需的图像，但随后的运行应该会更快。

storybook 准备好之后，您可以使用 localhost:8086 来测试它

git repo 的链接:[https://github . com/das Daniel/dockerized-vue-storybook-project](https://github.com/dasDaniel/dockerized-vue-storybook-project)