# 如何使用 RxJS 正确操作滚动到底部行为

> 原文：<https://dev.to/ataherinmobly/how-to-act-properly-to-scroll-to-bottom-behavior-using-rxjs-nd6>

大家好，我是阿姆尔·塔赫尔，一名跨平台的软件工程师，热爱 Javascript 编码，非常欣赏干净的模块化技术，使您的应用程序具有可维护性和可伸缩性。

有没有想过当用户在一个有角度的应用程序中滚动到底部时你会怎么做？例如，如果你有 3 个组件，每当用户滚动到底部时触发一些动作(获取更多数据或改变一些样式)，你会怎么做？

rxjs 提供了一个非常强大的工具包，您可以使用它来实现对这种问题的清晰解决。

首先，让我们听听用户的滚动行为，并检查他是否在 AppComponent.ts
的底部

```
 window.onscroll = () => {
      const d = document.documentElement;
      const offset = d.scrollTop + window.innerHeight;
      const height = d.offsetHeight;
      if (offset === height) {
        // do some action here.
      }
    }; 
```

现在，每当用户在底部，上述条件将被触发。我们可以使用 rxjs 强大的工具来使用该事件，以在所需的组件中共享这种滚动行为。

在服务中，比如说主题服务

```
public isAtBottom$ = new BehaviourSubject<Boolean>(false); 
```

这里我们定义了一个行为主题，通过订阅它，我们可以在任何组件中监听它！

```
if (offset === height) {
this.theming.isAtBottom$.next(true);
} 
```

现在，每当我们到达底部时，我们触发行为主体为真。

在任何组件中，只要我们处于底层，我们就可以简单地订阅行为主题来触发我们想要的事件。

ComponentA

```
this.theming.isAtBottom$.subscribe(res => {
if (res) {
// do something
}); 
```

就是这样！我希望你们喜欢这个想法，现在你们几乎可以在任何组件中这样做，同时只在一个部分(AppComponent.ts)中监听滚动事件