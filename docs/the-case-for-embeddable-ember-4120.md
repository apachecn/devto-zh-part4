# 可嵌入余烬的情况

> 原文：<https://dev.to/dustinsoftware/the-case-for-embeddable-ember-4120>

由 Karl Bewick 在 Unsplash 上拍摄的照片

在这篇文章中，我对 Ember 的一个重要但经常被忽视的用例提出了一些改进:在非 Ember 应用程序中嵌入 Ember 组件。Ember 非常适合全新的网络应用。但是，对于希望过渡到 Ember 的现有应用程序，我们该讲些什么呢？

考虑一个 2016 年开始使用 React 和 webpack 的单页面应用程序。已经支持引入 ES 模块，并将其整合到产品包中。然而，该团队已经听说了 Ember 的许多工具改进，并希望在现有的 React 应用程序中尝试发布一个小组件。然而，因为该应用程序使用客户端路由器，所以需要一种机制来加载 Ember 应用程序并呈现到 div 中，而不求助于 iframe。

团队可能会选择不采用 Ember，因为他们无法承受几个月的功能冻结来移植组件。这篇文章旨在解决这些痛点，以便团队可以自由地在他们的应用程序中逐步发布 Ember 组件，并随着时间的推移迁移应用程序。

Ember 应用程序是用`ember-cli`工具构建和打包的。因为 CLI 工具和框架是深度集成的，所以可以开发插件来修改构建过程。几个很好的例子是用`ember-cli-typescript`添加类型检查，用`ember-auto-import`为 es 模块生成瘦包装器，或者甚至将导入从模块语法`import { computed } from '@ember/object'`转换为`Ember.computed`。然而，这种工具的一个缺点是它发出的工件对于嵌入式场景来说并不理想。

让我们考虑一下在 React 应用程序中嵌入 Ember 组件会是什么样子:

### 反应元件

```
function ListUsers() {
  const users = [{ id: 1, name: 'masters' }];
  return <UsersWrapper users={users} />;
} 
```

### 余烬组件，从 React 调用

```
<div class="some-app__users">
  {{#each @users as |user|}}
    <div class="some-app__users__id">ID: {{user.id}}</div>
    <div class="some-app__users__id">Name: {{user.name}}</div>
  {{/each}}
</div> 
```

目前还没有办法像这样在现有的 React 应用程序中混合 Ember 组件。然而，如果我们引入一个简单的包装组件:

```
import React, { useEffect, useRef } from 'react';
import TinyEmber from 'tiny-ember-app';
import Users from './ember-components/users';

function UsersWrapper(props) {
  const containerRef = useRef();
  // this is invoked when the host component mounts
  useEffect(() => {
    const mountedInstance = TinyEmber.mount(Users, containerRef.current, props);

    // this is called when the host component unmounts
    return () => mountedInstance.destroy();
  }, []);

  return <div ref={containerRef} />;
} 
```

这个例子中的 TinyEmber 是一个伪库，它在 Ember 本身上提供了一个瘦 API。`mount`方法接受一个对要呈现的组件的引用(它将处理初始化)、一个 DOM 节点和一组初始组件参数。这非常类似于`ReactDOM.render`的设计，但是也接受根成员组件的初始组件参数。请注意，这些初始组件参数将仅用于 Ember 组件的第一次呈现——在父应用程序和 Ember 应用程序之间同步状态更新，留给读者作为练习(在这种情况下可以使用消息传递)。

消费应用程序(在本例中为 React)有一个不同的组件模型，但它仍然可以无缝地挂载和卸载 Ember 组件，并向它们传递数据。随着新应用程序规模的增长，“遗留”组件可以与较新的 Ember 组件共存，而较旧的组件可以一次移植一个。可选地，在将来，这些旧组件可以被完全移除，并且向完全成员组件的过渡完成。

嵌入 Ember 应用程序[已经被记录](https://guides.emberjs.com/release/configuring-ember/embedding-applications/)，但是当前的方法有一些限制。

### 根节点的硬编码选择器

包含元素 div 的选择器是在构建时指定的，从`ember build`发出的输出包含初始化应用程序的语句，并在包完成解析后立即呈现在页面上。当前的初始化代码需要由消费者来处理，这样当父组件被卸载时，组件可以被初始化和销毁，在父应用程序的生命周期内可能会多次。

### 缺少装载/卸载 Ember 组件的 API

目前还没有一个瘦 API 来渲染 Ember 或 Glimmer 组件本身。看起来在 GlimmerJS repo 中有一些对此的支持，可能会编写一个新的独立包装器来一起破解它。不过，如果有一流的 API，我会很高兴。似乎也没有在运行时用初始参数初始化根成员组件的概念。

### 建筑外用构件余烬

组件还需要以可以被外部捆绑器引用的方式导出。例如，`ember build`可以发出一个只包含 Ember 组件的库 Javascript 包，以及类似于 [webpack-node-externals](https://www.npmjs.com/package/webpack-node-externals) 的东西来引用`@ember`供应商导入。这样，如果`lodash`包含在 Ember 组件和主机应用程序中，那么供应商捆绑包将只包含一个副本。Webpack 为发出与 CommonJS 导入语法兼容的包提供了极好的支持，所以这种支持可能会在 serpent 中得到利用。如果这在短期内是不可能的，那么在捆绑者改变土地之前，将根组件暴露为`window.EmberComponents.MyRootComponentNameHere`可以在过渡期间起作用。

### 组件无服务

当前的 Ember 架构可能不适合需要通过单个页面应用程序路由转换来卸载根组件的环境，因为 javascript 上下文不会在虚拟页面转换之间重新加载。如果插件或服务假设只初始化一次，这可能会有问题。出于这个原因，我们应该专注于只支持渲染 Ember 组件，而不支持许多可以注入的整体服务(如 ember-data 和路由器)。在呈现组件的故事稳定之后，可以添加对这些更复杂的服务的支持。

### 现有技术

写完这篇文章，我发现[react-svelite](https://github.com/Rich-Harris/react-svelte)已经存在了！您可以在这里查看[组件实现](https://github.com/Rich-Harris/react-svelte/blob/10dcc68c26885f9c320823f8864a523f6946e46b/index.js)，它支持安装、更新和销毁底层的瘦组件。

### 感谢阅读！

通过公开一个 API 来呈现来自其他 JS 框架的组件，Ember 为现有的单页面应用程序提供了一个更好的迁移故事。尽管 React 被用作一个例子，但是相同的概念也适用于用 jQuery 编写的现有应用程序，或者任何其他前端框架。许多部分已经准备好支持这个 API(比如 Glimmer 组件的 explicit `this.args`和消除对 jQuery 的依赖)。我期待着看到我们今年在实现这一目标方面取得的进展！

感谢 Frank Tan、Shane Warren 和 Chris Krycho 审阅早期草稿。

# 人类的 2019 年