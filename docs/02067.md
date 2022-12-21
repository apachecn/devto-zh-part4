# 保护您的角度模块💂‍

> 原文：<https://dev.to/angular/guarding-your-angular-modules-lio>

许多角度模块需要使用静态`forRoot()`功能导入，通过该功能，我们可以配置模块。
一个例子，也许是最著名的，是需要路由集合的角度路由器模块，另一个例子是需要应用的根 reducers 的 NgRx 存储。
在内部，这些模块将初始化所需的服务，它将在这些服务之间建立编排，以便能够完成它的工作。

但是有时我们会犯错误，在整个应用程序中多次使用`forRoot()`函数。当这种情况发生时，我们可能不会注意到它，但它通常是导致意外行为的原因，令人遗憾的是，这很难调试。

这之所以是个问题，是因为这些模块必须被视为[单态](https://en.wikipedia.org/wiki/Singleton_pattern)。
当一个模块被延迟加载并且也在使用`forRoot()`函数时，延迟加载的模块将创建模块服务的第二个实例。这些实例在创建它们的上下文中使用。延迟加载的模块将使用它创建的实例，而不是使用根实例。

如果我们以 NgRx `StoreModule`的`forRoot()`函数为例来设置 NgRx Store。这意味着我们最终会有多个 store 实例，所有的配置都不同。如果一个特性模块要分派一个动作，它不会到达“真正的”根缩减器，它只会到达由懒惰模块配置的缩减器。如果从根模块内部分派相同的动作，那么它将到达“真正的”根缩减器。

第二个问题是，当 Angular 引导应用程序时，同一个模块被急切地多次加载。根据模块的加载方式，只有一个模块会被注册和使用。如果您没有意识到这一点，这可能会导致模块配置错误。

就我个人而言，我会浪费很多时间来跟踪和解决由此引起的问题，在这篇文章中，我们将研究一种解决方案来防止这种情况。

> [为什么一个共享模块给一个懒加载的模块提供服务是不好的？](https://angular.io/guide/ngmodule-faq#q-why-bad)

### 举个例子

例如，假设我们有一个`ThemeModule`来设置应用程序的主题。
应用程序的主题可以通过`ThemeModule.forRoot(color)`进行设置，并在项目开始时进行配置。随着时间的推移，我们的`ThemeModule`被提取到一个库中，使其可以在多个应用程序中重用，我们的`ThemeModule`是如此的成功，以至于它在多个代码库中被大量使用。突然，在我们的应用程序中，当我们导入另一个模块时，我们的主题颜色神奇地改变了，我们现在处于一个大的颜色节日中，这都是因为我们没有保护好`ThemeModule`。

那么，如何才能避免这种情况呢？这个问题的答案是根保护，在接下来的代码片段中，我们将看到如何设置它们。

### 显示代码

让我们首先实现`ThemeModule`，通过添加一个静态的`forRoot()`函数来设置`ThemeModule`。

```
@NgModule()
export class ThemeModule {
  static forRoot(themeConfig: ThemeConfig): ModuleWithProviders<ThemeModule> {
    return {
      ngModule: ThemeModule,
      providers: [{ provide: Theme, useValue: themeConfig }],
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码片段创建了一个带有`themeConfig`参数的`Theme`，`Theme`如下所示。

```
@Injectable()
export class Theme {
  constructor(config: ThemeConfig) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

要使用`ThemeModule`，我们可以使用`AppModule`中的`forRoot()`功能。

```
@NgModule({
  imports: [ThemeModule.forRoot({ color: '#dd0031' })],
})
export class AppModule {} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`ThemeModule`现在可以使用了，但是如果它被第二次导入，它也创建了主题的第二个实例。
其结果是，当加载功能模块时，模块将使用该主题而不是根主题。

[https://stackblitz.com/edit/angular-theme-guard-start?embed=1&&](https://stackblitz.com/edit/angular-theme-guard-start?embed=1&&)

### 这可以通过三个简单的步骤来避免

#### 1。创建一个`InjectionToken`

```
export const THEME_ROOT_GUARD = new InjectionToken<void>(
  'Internal Theme ForRoot Guard',
) 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们需要为我们的卫士创建一个 [`InjectionToken`](https://angular.io/api/core/InjectionToken) 。

#### 2。在模块的提供者中提供`ROOT_MODULE_GUARD`

```
@NgModule()
export class ThemeModule {
  static forRoot(themeOptions): ModuleWithProviders<ThemeRootModule> {
    return {
      ngModule: ThemeRootModule,
      provides: [
        {
          provide: Theme,
          useFactory: createTheme,
          deps: [themeOptions],
        },
        {
          provide: THEME_ROOT_GUARD,
          useFactory: createThemeRootGuard,
          deps: [[Theme, new Optional(), new SkipSelf()]],
        },
      ],
    }
  }
}

export function createThemeRootGuard(theme) {
  if (theme) {
    throw new TypeError(
      `ThemeModule.forRoot() called twice. Feature modules should use ThemeModule.forFeature() instead.`,
    )
  }
  return 'guarded'
} 
```

Enter fullscreen mode Exit fullscreen mode

通过工厂函数`createThemeRootGuard`，我们为`THEME_ROOT_GUARD`令牌提供了一个值。这个工厂函数需要一个`theme`参数来检查主题是否已经创建，如果已经创建，它将抛出一个错误。
使用`deps`我们可以提供`Theme`，第一次调用函数时`Theme`还没有初始化，所以我们使用`Optional`将参数标记为可选，否则依赖注入容器会抛出一个错误，因为它无法找到`Theme`的值。
为了不实例化`Theme`，我们可以使用`SkipSelf`。如果我们不这样做，我们将直接得到 2 个`Theme`实例。

更多关于 [`Optional`](https://angular.io/api/core/Optional) [`SkipSelf`](https://angular.io/api/core/SkipSelf) 的信息可以在 [angular.io](//angular.io) 上找到。

#### 3。在模块中注入保护令牌

```
@NgModule()
export class ThemeModule {
  constructor(
    @Optional()
    @Inject(THEME_ROOT_GUARD)
    guard: any,
  ) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

当模块被导入时，我们在`ThemeModule`中注入`InjectionToken`来创建`THEME_ROOT_GUARD`。
每次创建`ThemeModule`的新实例时，都会调用`createThemeRootGuard`工厂函数来创建`THEME_ROOT_GUARD`。第二次出现这种情况时，参数`theme`将有一个值，从而导致异常。

[https://stackblitz.com/edit/angular-theme-guard?embed=1&&](https://stackblitz.com/edit/angular-theme-guard?embed=1&&)

通过这样做，我们可以防止开发者使用`ThemeModule`通过`forRoot()`函数不止一次地意外导入我们的模块。
我们明确表示，我们没有预料到这种情况会发生，如果我们的模块使用不当，我们可以为使用我们模块的开发人员节省一些时间和挫折。

### 第二种方案却不同

还有第二个解决方案，把你的模块当作一个单例，这需要更少的设置，但是行为有点不同。
如果我们注入`ThemeModule`本身，我们可以简单地检查`ThemeModule`是否已经初始化。

```
@NgModule()
export class ThemeModule {
  constructor(@Optional() @SkipSelf() themeModule: ThemeModule) {
    if (themeModule) {
      throw new TypeError(`ThemeModule is imported twice.`)
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 有关该解决方案的更多信息，请参见[角度文档](https://angular.io/guide/singleton-services#the-forroot-pattern)

[https://stackblitz.com/edit/angular-theme-guard-end-2?embed=1&&](https://stackblitz.com/edit/angular-theme-guard-end-2?embed=1&&)

### 区别

第二种方法的一个缺点是，如果我们想添加用`forChild`函数第二次加载`ThemeModule`的能力，这将抛出同样的错误。

发生这种情况是因为我们在模块的构造函数中有防护。与第一种解决方案相比，我们在`createThemeRootGuard()`函数中设置了防护，它只在`forRoot()`函数中提供，这意味着只有当我们用`forRoot()`导入模块时，它才会被检查。

根据您的使用案例，您可以选择符合您需求的最佳解决方案。

对于一个真实的例子，你可以看看[角路由器](https://github.com/angular/angular/blob/master/packages/router/src/router_module.ts)的实现。

* * *

在 Twitter 上关注我，地址: [@tim_deschryver](https://twitter.com/tim_deschryver) 。