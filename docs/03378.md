# Laravel Vue 路由器-教程

> 原文：<https://dev.to/lvtdeveloper/laravel-vue-router-tutorial-1965>

Vue Router 是 Vue.js 的官方路由器，允许您将 URL 链接到特定组件，从而构建单页应用程序。

功能包括:

*   嵌套路线/视图映射
*   基于组件的模块化路由器配置
*   路由参数，查询，通配符
*   查看由 Vue.js 的过渡系统支持的过渡效果
*   细粒度导航控件
*   带有自动活动 CSS 类的链接
*   HTML5 历史模式或哈希模式，IE9 中带有自动回退功能
*   可定制的滚动行为

我们将使用 vue-router 将每个菜单元素分配给我们想要显示的组件。我们开始吧！

## 设置

打开您的终端并运行以下命令:

*   安装 Laravel 应用程序(在这种情况下，项目的名称是“catgallery”，您可以选择您想要的名称):
![Alt Text](img/6985630a8947fb15a71b6d74179e2695.png)*   使用以下命令进入新目录:
![Alt Text](img/571fa96ed5d6a9f90b0fb7b507e1ebb0.png)*   安装视图:
![Alt Text](img/43a14f097f21f28646c1b56d318db730.png)*   安装 npm:
![Alt Text](img/180a2f65cb3702b4e36cb44b535db67a.png)*   安装 Vue 路由器:
![Alt Text](img/0c4fa86e619dcf3b2f6d132c4a6f1431.png)

## App.js

转到主 JavaScript 文件 resources/assets/js/app.js，并使用以下代码进行更新:

*   导入视图路由器:
![Alt Text](img/2c044d75411ccbc941f5bbc2e536d1c4.png)*   接下来，我们告诉 vue 使用 Vue 路由器插件:
![Alt Text](img/88aafe7151c78fb65e25eed256cbc09c.png)*   设置路线(我们稍后将创建组件):
![Alt Text](img/3f21cac8edae2d96a86cba44d3d92832.png)*   创建路由器实例并传递 routes 选项:
![Alt Text](img/fcb0395483e0fc9a44606333a6136688.png)*   为了去除散列，我们可以使用路由器的历史模式，该模式利用 history.pushState API 来实现 URL 导航，而无需页面重载:
![Alt Text](img/10aeff41c199e6b049eedd35d689b4a3.png)*   创建并装载根实例:
![Alt Text](img/593592123043364b045a4dbe8485f56d.png)*   资源/js/app.js
![Alt Text](img/a7077bbd08978081e9619bcec02541bb.png)

## 成分

创建 vue.js 组件

*   resources/js/components/welcome component . vue
![Alt Text](img/8a2028ea21eec51065cb177a768666db.png)*   resources/js/components/kitte none . vue
![Alt Text](img/e0c8c3c74fc2d087405d230f12383614.png)*   resources/js/components/kittentwo . vue
![Alt Text](img/12b1ed501498f2f60e87aa816fb0e4bd.png)*   resources/js/components/kitt three . vue
![Alt Text](img/cff191cb37c7b71d46cdd1a8981e625e.png)

## 视角

更新 welcome.blade.php

*   添加样式 css
![Alt Text](img/5caa5e717cdb6216886a59a2b062b6d9.png)*   添加 id
![Alt Text](img/432828eb745074fad810349d1175cc09.png)*   为导航添加路由器链接组件
![Alt Text](img/dc03b831b71ff2d8ff6485982e40ae14.png)*   添加路由器视图标记。路由器使用该标签作为容器，用于呈现与不同路由相关的不同组件。
![Alt Text](img/e60534a49c4c601462bb331153764270.png)*   添加 js
![Alt Text](img/70bd768566798cb87c8a9f31b633f57b.png)*   resources/views/welcome . blade . PHP
![Alt Text](img/8991eb2ff07cf2fe74080556b0251182.png)

## 风格

现在添加一些样式 public/CSS/my style . CSS
[https://jsfiddle.net/lvtdeveloper/4o8x93ap/4//embedded/css//dark](https://jsfiddle.net/lvtdeveloper/4o8x93ap/4//embedded/css//dark)

## 最后的步骤

npm 运行开发
[![Alt Text](img/eeaab3fd888acf6bec2a7f09aae10876.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yLa61dAZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hc9godrcq1dcunkhs15m.png)

用下面的命令启动 app:
[![Alt Text](img/ae97d4854c9da07cc6e59abdc84f1d81.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BQfRdvt7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7bff8gke72afga81s1ka.png)

搞定=)
[![Alt Text](img/5ad67de5f59c15aa7e1a668ef6d42095.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1GkUSLl7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3x12xij75qbit2jlq8p7.png)

希望这个例子对你有帮助。🐈要有创意，一路上要有乐趣。

[宋写那篇文章的时候 si=DwWg9bE7Sdmur8OTQ-7ijA](https://open.spotify.com/track/4as4XEOR03oGm1STUKl6pa?si=DwWg9bE7Sdmur8OTQ-7ijA)

感谢阅读！编码员们，祝你们愉快💜