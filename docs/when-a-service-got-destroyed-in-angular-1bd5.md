# 当一个服务在 angular 中被破坏

> 原文：<https://dev.to/bo/when-a-service-got-destroyed-in-angular-1bd5>

*Asa Rodger 在 Unsplash 上拍摄的封面照片*

如果我们阅读 OnDestroy lifecycle hook api 描述 [Angular OnDestroy](https://angular.io/api/core/OnDestroy) 我们可以在服务上使用它。
但除此之外的信息，我们无法在上面找到。如果我们查看生命周期挂钩页面。这里什么也没有解释，所以是时候尝试一些东西了。

## 我们如何使用服务？

首先，我们将深入研究服务的使用。可以通过不同的方式向应用程序提供服务:

### 在根级别上

每个人都可以使用这个服务，每个模块都可以使用这个根服务。它是作为单例提供的，所以它只有一个，而且这个服务上的每一个动作，每一个可观察到的东西都在整个应用程序中共享。

如何从两个方面定义它？有了 providedIn，那么你就不要在你的模块
中提供它

```
@Injectable({ providedIn: 'root'})
export class RootService { ... } 
```

Enter fullscreen mode Exit fullscreen mode

提供的 sidemote:

> 你也可以使用`provideIn: module`，但是这还在实验阶段，所以要小心，`providedIn: 'root'`是很好的集成

或者在根模块上注入

```
@Injectable()
export class RootService { ... }

@NgModule({
    providers: [ RootService ],
})
export class AppModule{} 
```

Enter fullscreen mode Exit fullscreen mode

### [模块级上的](#on-module-level)

我们还可以在模块级别创建我们的服务，这些服务是私有的，专用于特定的模块。该逻辑仅在模块
内部共享

```
@Injectable()
export class ModuleService { ... }

@NgModule({
    providers: [ ModuleService ],
})
export class MyCustomModule{} 
```

Enter fullscreen mode Exit fullscreen mode

### 在组件级别上

这些服务是在组件级别注入的，因此只有当组件有效时它们才有效

```
// providedIn
@Injectable()
export class Componentervice { ... }

@Component({
    selector: 'my-component',
  template: '...',
    providers: [ Componentervice ],
})
export class MyComponent{} 
```

Enter fullscreen mode Exit fullscreen mode

## 服务的生命周期。

首先，我们对服务的创建感兴趣。我们可以为它添加一个简单的测试。只需创建如下代码:

```
export class MyService {
    constructor(){
        console.log('constructor initialized')
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了测试这个逻辑，我们有几个测试用例，在

*   根级别
*   模块级
*   惰性加载模块
*   组件级别

### 初始化服务

> 所有服务在第一次使用时初始化。

关于服务的补充说明。仅仅提供它是不够的，我们应该在要初始化的服务上实际执行一个动作，所以将它添加到 providers 数组并不是初始化该服务的一种方式。我们可以以不同的方式使用服务，比如通过一个组件或一个工厂来使用它。

因此，我们知道，所有服务都是在第一次使用时初始化的，这是我们可以肯定的一件事。这也意味着惰性加载服务仅在第一次加载路由时初始化，提供的“根”服务仅在第一次使用时初始化。。

让我们看看输出，这里我们看到一个有趣的模式。正如预期的那样，根、模块和惰性服务提供者只被创建一次。当我们重新创建组件时，它的服务就不会再被创建
[![Alt Text](img/84b0be48b7c93ff320b466cf74d83853.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IffODHhw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tlw90o8u1ixdcanhtl3b.png)
[![Alt Text](img/37d568cf2435bf78799eb312cde3e561.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2BjGCNNj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yhh36r9nho1013gekknk.png)
[![Alt Text](img/28895c994965102184d70b98a93b00f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_WVluFKu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4cwn4r0qy9wxz0w3trfg.png)

但是最有趣的部分是组件提供的服务:每次创建组件时，服务也被创建
[![Alt Text](img/98c232fc44aa25c6d10fc120b44b50c8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--fvsiL-Na--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r9fm1gk48id7wv8m1i8l.png)

> 每次创建组件时，只创建组件提供程序

### 破坏服务

现在我们做同样的事情，但是我们用 ngOnDestroy 钩子破坏了服务。由于某些原因，我们在谷歌中加厚了保存日志复选框，这样即使我们离开页面，我们也可以跟踪我们的日志记录历史。

同样，每个服务的测试代码也是相似的

```
@Injectable()
export class MyService implements OnDestroy {
  name = 'xxx service';

  constructor(private counterService: CounterService) { 
    console.log('Construct the xxx service')
  }  

  ngOnDestroy() {   
    console.log('Destroy the xxx service')
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

首先我们看一下组件服务，因为每次创建组件服务时，我们都希望它在组件销毁时销毁。
[![Alt Text](img/e7cf6fefa720e1058e6bb7df7326e08a.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--iLJrHXC_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nfj09yu73xntbwdannki.png)

如果我们看看其他 3 个服务，我们会发现它们从未被销毁。甚至当我们离开这一页的时候。当你离开页面时，你可能会意外地得到它，但是你不能保证这一点。
[![Alt Text](img/9c84282804eb0258590787d05dde0fb3.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--CwitPVKR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oh41n3t43xunwzrm1q22.png)

所以这个 ngOnDestroy 钩子的结论是:

## 结论

*   您永远无法确定服务是否被销毁，除非您在组件级别上提供它
*   如果你在组件层次上提供服务并重构代码，那么你需要注意这一点

> 在服务上使用这个钩子时要小心

完整试用可在 stackblitz:
[https://stackblitz.com/edit/service-lifecycle?embed=1&view=preview&](https://stackblitz.com/edit/service-lifecycle?embed=1&view=preview&) 上获得