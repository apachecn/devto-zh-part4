# 认证预加载策略

> 原文：<https://dev.to/bergermarko/authentication-preload-strategy-4a07>

我已经决定编写自己的自定义身份验证预加载策略。我从[约翰爸爸](https://dev.to/john_papa)和他的预载策略系列文章中得到一个想法。[我真心推荐你阅读这个主题的系列文章](https://dev.to/angular/preload-all-angular-bundles-1b6l)。那么什么是自定义预加载策略呢？

> 通过这种方式，您可以告诉 Angular 应用程序如何以您选择的预定义方式捆绑您的 javascript 代码。

为此，假设我们有一个认证服务，它将根据用户角色重新加载将要预加载的模块。我不打算深入探讨你将如何或在哪里存储你的策略。因为是哲学问题:-)。我将在环境变量中设置我的预加载策略。(这只是其中一种方式)。

```
strategies: new Map([
    ['default', ['module1']],
    ['admin', ['module1', 'module2', 'module3']],
    ['user', ['module1', 'module2']]
  ]
  ) 
```

我们的路由器设置。

```
const routes: Routes = [
  {path: '', redirectTo: '/home', pathMatch: 'full'}, //not important
  {path: 'home', component: HomeComponent}, //not important
  {
   path: 'module1', 
   loadChildren: '../app/module1/module1.module#Module1Module', 
   data: {preload: true}
  },
  {
   path: 'module2', 
   loadChildren: '../app/module2/module2.module#Module2Module', 
   data: {preload: true}
  },
  {
   path: 'module3', 
   loadChildren: '../app/module3/module3.module#Module3Module', 
   data: {preload: true}
  },
];
@NgModule({
  imports: [RouterModule.forRoot(routes, {preloadingStrategy: RolePreloadService})],
  exports: [RouterModule]
}) 
```

让我们在我们的认证服务中创建我们需要的东西

```
export class OnDemandRolePreloadOptions {
  constructor(public routePath: string, public preload = true) {}
}
@Injectable({
  providedIn: 'root'
})
export class AuthServiceService {

  constructor() { }

  getUserRolePreloadOption(){
    const rolePaths: OnDemandRolePreloadOptions[] = this.generatePaths()
    return from(rolePaths);
  }
  generatePaths(){
    return environment.strategies.get('default') //default preload
    .map( mod => new OnDemandRolePreloadOptions(mod, true));
  }
} 
```

*这只是奉此例*
的简单概括。我们需要将默认策略的 OnDemandRolePreloadOptions 类返回为 observable。

既然我们有了策略。让我们在 role-preload.service.ts
中施展一些魔法

```
export class RolePreloadService implements PreloadingStrategy {
  userRole$: Observable<OnDemandRolePreloadOptions>;

  constructor(private authService: AuthServiceService) { 
    this.userRole$ = this.authService.getUserRolePreloadOption();
  }

  preload(route: Route, load: () => Observable<any>): Observable<any> {

    return this.userRole$.pipe(
      mergeMap(role => {
        const shouldPreload = this.preloadCheck(route, role);
        return shouldPreload ? load() : EMPTY;
      })
    );
  }

  preloadCheck(route: Route, preloadRoleOptions: OnDemandRolePreloadOptions){
    return (
      route.data &&
      route.data['preload'] && 
      [route.path, '*'].includes(preloadRoleOptions.routePath) &&
      preloadRoleOptions.preload
      )
  }

} 
```

因此，preloadCheck()方法是这里最重要的方法。它检查是否为预加载预定义了延迟加载路由，它是否存在于 router 和 preloadRoleOptions 中。如果是这样，它将加载我们基于身份验证的预加载策略。如果您使用管理员帐户登录，它将加载预加载的管理员 javascript 包。

*享受*