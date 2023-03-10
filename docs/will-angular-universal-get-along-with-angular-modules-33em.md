# Angular Universal 会和 httpClient 相处吗？

> 原文：<https://dev.to/jerintk/will-angular-universal-get-along-with-angular-modules-33em>

我目前正在做一个主要基于 NgModules 的项目。我们有 5 个不同的模块和多个组件和服务。当我们尝试使用 Angular Universal 启用服务器端呈现时，页面没有完全呈现。下面给出的代码是唯一呈现的部分。(查看来源)

当我们在代码中使用 httpClient 时，就会出现这个问题。没有它，一切都很完美。

```
<app-root _nghost-sc0="" ng-version="8.1.2"><router-outlet _ngcontent-sc0=""></router-outlet><app-main _nghost-sc1=""><!----></app-main></app-root> 
```

但是如果我们检查浏览器的开发工具，我们可以看到

```
<app-root _nghost-serverapp-c0="" ng-version="8.1.2"><router-outlet _ngcontent-serverapp-c0=""></router-outlet><app-main _nghost-serverapp-c1=""><!--bindings={
  "ng-reflect-ng-if": "[object Object]"
}--><div _ngcontent-serverapp-c1="" id="home-page"><app-banner _ngcontent-serverapp-c1="" _nghost-serverapp-c2="" ng-reflect-banners="[object Object],[object Object"><p _ngcontent-serverapp-c2="">banner works!</p></app-banner><app-counter _ngcontent-serverapp-c1="" _nghost-serverapp-c3="" ng-reflect-counters="[object Object],[object Object"><p _ngcontent-serverapp-c3="">counter works!</p></app-counter><app-categories _ngcontent-serverapp-c1="" _nghost-serverapp-c4="" ng-reflect-categories="[object Object],[object Object"><p _ngcontent-serverapp-c4="">categories works!</p></app-categories><app-experience _ngcontent-serverapp-c1="" _nghost-serverapp-c5="" ng-reflect-experiences="[object Object],[object Object"><p _ngcontent-serverapp-c5="">experience works!</p></app-experience><app-stay _ngcontent-serverapp-c1="" _nghost-serverapp-c6="" ng-reflect-stays="[object Object],[object Object"><p _ngcontent-serverapp-c6="">stay works!</p></app-stay><app-spotlight _ngcontent-serverapp-c1="" _nghost-serverapp-c7="" ng-reflect-spotlights="[object Object],[object Object"><p _ngcontent-serverapp-c7="">spotlight works!</p></app-spotlight></div></app-main></app-root> 
```

我遵循官方的 angular universal 文档。我在这里做错了什么？