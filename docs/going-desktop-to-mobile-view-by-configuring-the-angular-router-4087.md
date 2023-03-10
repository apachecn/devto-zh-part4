# 通过配置角度路由器从桌面到移动视图

> 原文：<https://dev.to/anoushnet/going-desktop-to-mobile-view-by-configuring-the-angular-router-4087>

使用开发紧急应用程序(使用实时数据)。净而有角。后端是。net 和 Azure 上的 SQL server 和 SignalR。这个应用程序是销售驱动的产品，我们首先推出了桌面版，然后是移动友好版，因为市场和 90-80%的用户都在桌面上查看。

这篇文章描述了我们是如何设计我们的应用程序的。基本上，从一个已经存在的桌面 web 应用程序到一个响应式应用程序。当在文章中提到“移动”一词时，它不是移动原生应用程序或渐进式 Web 应用程序，而是指不同桌面和移动设备上的响应应用程序。

# 架构和设置

我们从根文件夹“src”下的 angular 应用程序开始。我们创建了一个名为“移动”的文件夹。我们有主桌面视图的“应用程序”文件夹，它可以被重命名为“桌面”。这给了我们一个全新的地方来存放我们的移动代码，而不是和所有其他代码放在同一个文件夹中，它更有组织性，更干净，并与所有其他代码分开。

在应用程序路由器模块“AppRoutingModule”中，当加载应用程序时。我们从“app”和“mobile”文件夹中加载应用程序的所有路径的数组。当应用程序知道窗口“window.innerWidth”的大小时，它将通过调用“this.route.resetConfig()”修改路径数组并停止路由配置。在这里，我们还在应用程序的主要组件中监听“window:resize”来调整窗口大小，路由器路径被修改，然后路由器配置被重新加载。因此，用户可以从桌面视图切换到移动视图，反之亦然。

```
const desktopPreloadModules = [
  {
    path: 'dashboard', loadChildren: () =>
      import('app/modules/dashboard/dashboard.module')
        .then(mod => mod.DashboardModule)
  },
  {
    path: 'debug', loadChildren: () =>
      import('app/modules/debug/debug.module')
        .then(mod => mod.DebugModule)
  }
];

const mobilePreloadModules = [
  {
    path: 'dashboard', loadChildren: () =>
      import('mobile/modules/dashboard/dashboard.mobile.module')
        .then(mod => mod.DashboardMobileModule)
  },
  {
    path: 'debug', loadChildren: () =>
      import('mobile/modules/debug/debug.mobile.module')
        .then(mod => mod.DebugMobileModule)
  }
];

const appRoutes: Routes = [
  { path: 'login', component: LoginComponent },
  {
    path: 'main',
    component: MainComponent,
    canActivate: [AuthGuard],
    resolve: [AppDataResolver],
    children: [...desktopPreloadModules, ...mobilePreloadModules]
  },
  { path: '', redirectTo: '/main', pathMatch: 'full' },
  { path: '**', component: LoginComponent }
];

@NgModule({
  imports: [RouterModule.forRoot(appRoutes, { preloadingStrategy: SelectiveStrategy, enableTracing: false, scrollPositionRestoration: 'enabled' })],
  providers: [SelectiveStrategy],
  exports: [RouterModule]
})
export class AppRoutingModule {
  constructor(…) { … }
} 
```

还有 CSS 文件，样式根据窗口大小而变化，我们使用媒体查询，通过调用:

`@media only screen and (max-width: ####px)`

在我们的应用程序中，我们也有一些动态对话框，我们也通过在移动视图或桌面视图中重新加载它们来覆盖这些组件。

更改路线后，需要重新加载所有组件(在当前视图中)。在这里，我们通过简单地导航到一个空路径来解决这个问题，这基本上是重新加载 DOM 中的所有当前组件(它不是从服务器刷新的)。)

# 移动实现

在移动 for 中使用与桌面“app”文件夹相似的路径树，通过使用 OOP 的继承、多态和覆盖方法。我们可以访问桌面服务和组件的所有功能，并通过扩展相同的组件来重用代码。遵循开放/封闭原则。例如:

```
@Component({
  selector: 'my-mobile-component',
  templateUrl: './my.mobile.component.html'
})
export class MyMobileComponent extends MyDesktopComponent { } 
```

如果移动功能不同于桌面功能，我们将覆盖移动文件夹中的桌面方法。

在我们的应用程序中，这是中等到高级的应用程序复杂性。它只在一个地方检查应用程序是移动的还是桌面的，那就是在“AppRoutingModule”中，在路由器之外没有“if (mobile) do this else do that”。我们遵循并坚持这种模式，因为应用程序可能会非常混乱。如果我们有一个移动特定的组件或功能，从桌面扩展类，并覆盖或添加移动特定的功能或视图。遵循单一责任原则。

# 优点

*   整洁有序的代码。
*   易于实现移动视图应用程序。
*   代码的重用，这为我们节省了大量的穿刺和测试时间。桌面代码已经投入生产并被用户使用，我们知道它是否有效。因此，我们相信代码是可行的。
*   遵循单一责任原则
*   遵循开放/封闭原则

# 弊

*   如果我们想覆盖桌面行为或功能，我们需要在移动中扩展它。这可能会给一些更深层次的功能带来一点复杂性。然而，通过重构和使用可靠的原则是可行的。
*   如果我们打破这种模式，如果有人用“如果(移动)这样做，否则那样做”的方式或其他方式来写，很容易得到混乱的代码。