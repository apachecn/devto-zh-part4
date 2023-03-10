# 布尔玛码头

> 原文：<https://dev.to/chachan/dockerizando-angular-con-bulma-17pa>

需求:

*   了解坞站的基本工作原理
*   了解 npm 的基本功能

在我的角化学习过程中，我想借此机会测试一个新的 CSS 框架，并寻找替代方案，我得到了一个因其文档和各种组件而引起我注意的框架，我指的是“[bulma . I〖t1〗。](http://bulma.io/)

将球团纳入角型项目可以通过多种方式实现，例如将 cos 链接直接从 CDN 纳入`index.html`或使用[【球团启动】](https://bulma.io/bulma-start/)。另一种方法是将 CSS 作为一个依赖项包括在项目中，并像控制任何其他依赖项一样控制其更新。

最后的代码可以在:[【chagan/angular-bulma-SCS】【github】](https://github.com/chachan/angular-bulma-scss)

撰写本文时使用的版本:
角:8.2.4
bulma: 0.7.5
节点:12.9.1
坞站:18.09.2

*   我们创建了角度项目。为了避免在机床上直接安装依赖项，我通常使用容器。这就是码头。我们可以把它放在`./angular/angular.dockerfile`

```
FROM node:12.9.1

WORKDIR /app
RUN npm install -g @angular/cli 
```

*   我们创造了这个容器

```
$ docker build -t angular:local -f angular/angular.dockerfile ./angular 
```

*   我们启动并进入容器，以便创建角设计

```
$ docker run --rm -it -v $(pwd)/angular:/app -w /app angular:local bash
/app# ng new --defaults --skip-git my-app 
```

这是一个 Angular-cli 命令，它为我们生成了 angular 项目的基础，在我编写此内容时，angular 的当前版本将询问我们希望对我们的样式使用哪个预处理程序。为了利用 bulma 的灵活性([concepts | bulma:a modern CSS framework based on flexbox](https://bulma.io/documentation/customize/concepts/))，我们可以选择 SCS，这是要在角设计中写入的语法。

*如果角工程已经存在*你只需自己定义预处理程序，幸运的是你可以用角-cli:

```
$ docker run --rm -it -v $(pwd)/angular:/app -w /app/my-app angular:local \
  ng config schematics.@schematics/angular:component.styleext scss 
```

`angular/angular.json`结束时你应该可以看到:

```
"schematics":  {  "@schematics/angular:component":  {  "styleext":  "scss"  } 
```

*   我们安装了 bulma，我们又在容器里做了:

```
$ docker run --rm -it -v $(pwd)/angular:/app -w /app/my-app angular:local bash
/app/my-app# npm install bulma 
```

*   在`angular/my-app/src/styles.scss`我们进口 bulma:

```
@import '../node_modules/bulma' 
```

*   由于项目在一个容器内，因此需要作一点修改，以便不仅仅听到“`localhost`”的声音。我们保证在`angular/my-app/package.json`中`scripts.start`的输入内容如下:

```
"start": "ng serve --host 0.0.0.0", 
```

*   我们放了一个测试 HTML，我们可以用以下内容代替`angular/my-app/src/app/app.component.html`的内容:

```
<section class="section">
  <div class="container">
    <h1 class="title">Hello World</h1>
    <p class="subtitle">My first website with <strong>Bulma</strong>!</p>
  </div>
</section> 
```

*   我们启动服务器

```
$ docker run --rm -it -v $(pwd)/angular:/app -w /app/my-app -p 4200:4200 angular:local npm start 
```

*   visit amos[http://localhost:4200/](http://localhost:4200/)

目录结构肯定不是最好的，应该加以改进。