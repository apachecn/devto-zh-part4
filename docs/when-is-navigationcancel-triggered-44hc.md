# 什么时候触发导航取消？

> 原文：<https://dev.to/dzhavat/when-is-navigationcancel-triggered-44hc>

这篇文章最初发表在我的博客上。

* * *

在过去的几天里，我一直在研究一个角度组件，它依赖于一系列的[路由器事件](https://angular.io/api/router/RouterEvent)。有很多，但我特别关注的是`NavigationCancel`。出现的一个问题是“这个事件是什么时候触发的？”

[文档](https://angular.io/api/router/NavigationCancel)没有真正回答我的问题。下面是目前的内容:

> 表示导航被取消时触发的事件。

虽然这有点帮助，但我希望得到更多的细节。在这篇短文中，我想分享我对触发`NavigationCancel`的案例的发现。

### 当一个路线守卫返回时`false`

这可能是最明显的一个。如果您有一个在导航期间返回`false`的[路线守卫](https://angular.io/guide/router#milestone-5-route-guards)，您将得到一个`NavigationCancel`事件。返回值是`false`、解析为`false`的`Promise`还是发出`false`的`Observable`都无所谓。最终的结果是一样的。

```
canActivate() {
  // Case 1
  return false;

  // Case 2
  return Promise.resolve(false);

  // Case 3
  return new Observable<boolean>(observer => {
    observer.next(false);
    observer.complete();
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

[StackBlitz 示例](https://stackblitz.com/edit/angular-zysw4m)

### 由路由守卫发起重定向

这里有几个案例。从 7.1 版开始，一个`CanActivate`守卫也可以返回一个 [`UrlTree`](https://angular.io/api/router/UrlTree) 对象。在这种情况下，当前导航**将被取消**并根据返回的`UrlTree`开始新的导航。

```
canActivate() {
  // Case 1
  // Manually start a new navigation and cancel the current one by returning `false`
  this.router.navigateByUrl('/hello-new');
  return false;

  // Case 2
  // The router will automatically cancel the current navigation and start a new one 
  return this.router.parseUrl('/hello-new');
} 
```

Enter fullscreen mode Exit fullscreen mode

[StackBlitz 示例](https://stackblitz.com/edit/angular-7a4pty)

就是这样。这些都是会触发`NavigationCancel`的情况。你知道更多吗？在推特上让我知道。