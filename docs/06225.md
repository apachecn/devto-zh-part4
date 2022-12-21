# 角度中的惰性加载组件

> 原文：<https://dev.to/negue/lazy-loaded-components-in-angular-4lnd>

回到过去的 AngularJS 时代，我有一个小小的游乐场应用程序，在那里我可以偷懒加载几乎所有的东西。

自从 Angular 2 出来后，我每隔几个月就搜索一下关于懒加载组件的问题。

在发布后的一段时间，基于你的路径延迟加载 angular 模块变得非常容易，使用简单的 [npm 包/文件](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-4.html)也变得更加容易，但是没有任何“简单”(-ish)的方法来延迟加载一个组件，但是谢天谢地 Ivy 最终会改变这一点。🎉

## 1。等等，在你继续读之前...

建议你先看看[阿图尔关于`Asynchronous modules and components in Angular Ivy`](https://blog.angularindepth.com/asynchronous-modules-and-components-in-angular-ivy-1c1d79d45bd3) 的帖子，很棒的介绍和讲解！

这个组件加载器就是基于他的例子，基于一个固定的`import()-path`来加载组件。

## 2。重构示例组件[链接](https://gist.github.com/negue/5f4435c7e1d2c11449691d342b39cdd5/5bc827ee9fda2115da34ce6d9a0bf24986c6bce9)

我添加了一个简单的映射来保存我所有的懒惰的可加载组件。

```
// list of lazy loaded components
const lazyComponents = {
  'lazy-comp': () => import('../lazy-comp/lazy-comp.component'),
  'other-comp': () => import('../lazy-comp/other-comp.component')
}; 
```

对于每一行`import`, angular-CLI(或者更确切地说是 webpack)将这些导入标记为懒惰，这些导入将被提取到一个单独的块中。

```
chunk {lazy-comp-lazy-comp-component} lazy-comp-lazy-comp-component.js, lazy-comp-lazy-comp-component.js.map (lazy-comp-lazy-comp-component) 1.55 kB  [rendered]
chunk {lazy-comp-other-comp-component} lazy-comp-other-comp-component.js, lazy-comp-other-comp-component.js.map (lazy-comp-other-comp-component) 1.57 kB  [rendered] 
```

现在你只需要设置

```
<my-dynamic-loader component="lazy-comp"></my-dynamic-loader> 
```

并且您的组件是延迟加载的🎉

## 3。重构仍在继续..

现在让我们在加载器组件本身之外注册惰性组件，这样它就更有一点*的动态性*。(现在`DynamicLoaderComponent`可以从一个库中使用，并且可以从任何地方添加额外的惰性组件)

```
DynamicLoaderComponent.LazyComponents = {
  'lazy-comp': () => import('./lazy-comp/lazy-comp.component'),
  'other-comp': () => import('./lazy-comp/other-comp.component')
}; 
```

[重构版本](https://gist.github.com/negue/5f4435c7e1d2c11449691d342b39cdd5/b4307735eda415606e5c035e7d631d9dda69527a)

待续/试用/测试:

*   将服务注入惰性组件
*   `@Input()` / `@Output()`
*   防止多次加载同一个组件/加载的组件缓存
*   关于组件加载/创建/其他的事件
*   回购/项目示例

有什么想法/建议/主意吗？