# 使用 CLI 从 Vue 构建自定义元素

> 原文：<https://dev.to/firstclown/building-custom-elements-from-vue-using-the-cli-19hh>

如果你在 Vue 中工作，Vue CLI 是一个令人惊奇的东西。在现代 JavaScript 开发中有许多移动部分，CLI 使得管理这些部分变得非常容易。

CLI 带给您的一个好处是能够将 Vue 组件编译成定制的 HTML 标签。我已经在[什么是定制 HTML 元素](//What%20are%20custom%20HTML%20elements.md)和[从 Vue CLI 构建定制元素](//Building%20custom%20elements%20from%20Vue%20CLI.md)中讨论了如何设置。这篇文章讲述了如何编译和导出这些组件，以便它们可以放在任何网页上。

## 将 Vue 传输到自定义组件

将 Vue 转变为官方的、浏览器支持的 Web 组件的过程是通过一个名为`@vue/web-component-wrapper`的 Vue 构建工具来处理的。这将把你的组件构建成 Web 组件标准所需的格式。这种从 Vue 组件到自定义组件的转换已经内置在 Vue CLI 中，所以如果您的项目是用 Vue CLI 创建的，您所要做的就是将组件导出到自定义组件:

```
vue-cli-service build --target wc --name my-blink 'src/components/Blink.vue' 
```

Enter fullscreen mode Exit fullscreen mode

这将在您的`dist`文件夹中创建三个文件:

| 文件名 | 目的 |
| --- | --- |
| `my-blink.js` | 组件的代码减去 Vue 库 |
| `demo.html` | 一个简单的组件演示 HTML 文件 |
| `my-blink.js.map` | 浏览器的调试信息 |
| `my-blink.min.js` | 组件的缩小版本 |
| `my-blink.min.js.map` | 缩小版本的调试信息 |

这里需要知道的重要一点是，在 HTML 中任何需要使用组件的地方，都必须包含 Vue 库。它不包含在构建本身中。如果您查看在构建过程中创建的演示 HTML 文件，您会看到它的实际效果。

```
<script src="https://unpkg.com/vue"></script>
<script src="./my-blink.js"></script>

<my-blink></my-blink> 
```

Enter fullscreen mode Exit fullscreen mode

然后您可以使用这个`demo.html`文件来测试这个组件。

## 如果有多个组件怎么办？

如果您有多个想要转换成 Web 组件的组件，该怎么办？您不需要单独构建每一个，而是可以一次构建全部。

```
vue-cli-service build --target wc-async --name demo 'src/components/*.vue' 
```

Enter fullscreen mode Exit fullscreen mode

这将把你的组件构建到多个文件中，这些文件实际上是惰性加载到浏览器中的。如果你不使用某个组件，它的文件就不会被载入。不错！

但是`--name`呢？当您在单个组件构建中定义它时，它将标记命名为。但是这里有多个组件。它现在做什么？

这里，`--name`被用作标签的前缀。所以如果你的 Vue 组件被命名为`blink`，而`--name`被定义为`demo`，那么你的标签将导出为`demo-blink`。

## 就这样

现在你可以构建你的 Vue 组件，从`dist`中取出`js`文件，并把它们放到任何网页上。然后只需在你的 HTML 和任何页面上的 **BOOM** Vue 组件的任何地方包含标签。