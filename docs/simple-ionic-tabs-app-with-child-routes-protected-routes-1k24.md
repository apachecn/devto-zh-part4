# 带有子路线和受保护路线的简单离子标签应用程序

> 原文：<https://dev.to/aaronksaunders/simple-ionic-tabs-app-with-child-routes-protected-routes-1k24>

我经常看到发布的关于 Ionic 应用的两个问题是
1)我如何处理认证(受保护的路线)和
2)当在 Ionic 中使用标签时，我如何处理子路线。

因此，我将尝试提出一个简单的基本解决方案，用下面的代码解决这两个问题。我只包含了解决上面列出的问题所必需的代码片段，但是完整的源代码可以在这里找到: [ionicv4-tabs-with-detail](https://github.com/aaronksaunders/ionicv4-tabs-with-detail)

*该示例代码基于 Ionic Framework 的 v4 版本*

## 受保护的路线

要获得受保护的路由，您需要能够确定用户的登录状态。为了使事情尽可能简单，我们创建了一个`AuthenticationService`，它有一个将状态设置为真或假的方法，以及一个获取当前登录状态的方法。

```
@Injectable({
    providedIn: "root"
})
export class AuthenticationService {
    isLoggedIn = false;

    constructor() { }

    setLoggedIn(_value) {
        this.isLoggedIn = _value;
    }
    isAuthenticated(): boolean {
        return this.isLoggedIn;
    }
} 
```

接下来我们创建一个新的类`AuthGuardService`，它实现了 [`CanActivate`](https://angular.io/api/router/CanActivate) 接口。

在这个例子中，我们所做的就是调用`AuthenticationService`来查看用户是否通过了身份验证，如果用户没有通过身份验证，那么我们将用户重定向到由路径`/login`定义的路由。我们通过构造一个`UrlTree`对象并返回它来实现，因为我们可以根据 [`CanActivate`文档](https://angular.io/api/router/CanActivate)
从函数中返回一个`boolean`、`Promise`或`UrlTree`

```
import { Injectable } from '@angular/core';
import { CanActivate, ActivatedRoute, Router } from '@angular/router';
import { AuthenticationService } from './authentication.service';

@Injectable({
  providedIn: 'root'
})
export class AuthGuardService implements CanActivate {

  constructor(private auth: AuthenticationService, private router: Router) { }

  canActivate(): boolean {
    let value = this.auth.isAuthenticated()
    if (!value) {
      // initially was just redirecting here, but following the
      // documentation I updated code to return a UrlTree
      // this.router.navigateByUrl("/login", { skipLocationChange: true })

      return this.router.parseUrl("/login");
    }
    return value
  }
} 
```

现在我们有了我们的`AuthenticationService`来告诉我们用户的状态，我们的`AuthGuardService`将用于在呈现路线之前进行检查；我们准备更新`app-routing.module`。

见下文，我们将`AuthGuardService`添加到默认路由中，这样当应用程序首次启动时，用户验证状态将被验证，否则它将重定向到`LoginPageModule`

```
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { AuthGuardService } from '../services/authGuard.service';

// in the routes that need to be protected or the user
// has to be authenticated, we add the AuthGuardService
const routes: Routes = [
  { 
    path: '', 
    canActivate: [AuthGuardService], 
    loadChildren: './tabs/tabs.module#TabsPageModule' 
  },
  { 
    path: 'login', 
    loadChildren: './public/auth/login/login.module#LoginPageModule' 
  },
];
@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { } 
```

## 标签页中的子路线

选项卡路由器模块在这里有一些魔力，可以处理默认路由，还可以正确处理子路由，从而正确管理每个选项卡的导航堆栈。

呈现的第一个组件是`TabsPage`，但是正如您所见，该页面有子模块，第一个是将呈现`HomePage`的`HomePageModule`。

(1)在下面代码段的底部，我们展示了如何处理选项卡模块的默认路由，将其重定向到 home 选项卡。

处理一个选项卡的子路由，或者呈现一个细节页面，用第二个路由定义在`home`路径下演示。我们已经定义了路径`detail`，我们使用`/tabs/home/detail`的完整路径来访问它

(2)这将加载`DetailPageModule`，它将依次加载`DetailPage`，因为它是`DetailPageModule`中的默认路线。

```
const routes: Routes = [
  {
    path: "tabs",
    component: TabsPage,
    children: [
      {
        path: "home",
        children: [
          {
            path: "",
            loadChildren: "./../home/home.module#HomePageModule"
          },
          // (2) loads detail page, pushing it on the navigation stack of 
          // the home tab
          {
            path: "detail",
            loadChildren: "./../detail/detail.module#DetailPageModule"
          }
        ]
      },
      {
        path: "about",
        children: [
          {
            path: "",
            loadChildren: "./../about/about.module#AboutPageModule"
          }
        ]
      },
      {
        path: "contact",
        children: [
          {
            path: "",
            loadChildren: "./../contact/contact.module#ContactPageModule"
          }
        ]
      }
    ]
  },
  // (1) the default route of this module is the home tab so that tab is
  // displayed when directed here from the top level router module.
  {
    path: "",
    redirectTo: "/tabs/home",
    pathMatch: "full"
  }
]; 
```

## 处理申请中的路线

### 登录

为了登录到应用程序，我们在`login.page.ts`文件中包含了以下代码。应用程序将首先调用注入的`AuthenticationService`来设置登录状态，然后它将导航到应用程序的默认路径

```
 login() {
    this.auth.setLoggedIn(true)
    this.router.navigateByUrl("/", { skipLocationChange: true });
  } 
```

### 注销

为了注销应用程序，我们在`home.page.ts`文件中包含了以下代码。应用首先会调用注入的`AuthenticationService`来设置登录状态，然后会导航到应用的登录路径。

```
 logout() {
    this.auth.setLoggedIn(false)
    this.router.navigateByUrl("/login", { skipLocationChange: true });
  } 
```

### 详细页面

为了从`HomePage`选项卡导航子根，我们在页面中包含了以下代码；使用`ion-button`

```
<ion-button routerLink="/tabs/home/detail">Next</ion-button> 
```

# 项目源代码

为了简洁起见，我没有在本文中包含所有的源代码，但是下面列出了 git hub repo。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[aaronksaunders](https://github.com/aaronksaunders)/[ion icv 4-tabs-with-detail](https://github.com/aaronksaunders/ionicv4-tabs-with-detail)

### 带有子路线和受保护路线的简单离子标签应用程序

<article class="markdown-body entry-content container-lg" itemprop="text">

# io nicv 4-带详细信息的选项卡

*   更新至最新版本
*   解决了图标不显示的问题
*   解决了嵌套页面动画效果不佳的问题

## 查看另一个离子 v4 示例

[https://github.com/aaronksaunders/ionic4-sidemenu-auth](https://github.com/aaronksaunders/ionic4-sidemenu-auth)

## 博客帖子

[https://dev . to/aaronksaunders/simple-ionic-tabs-app-with-child-routes-protected-routes-1k 24](https://dev.to/aaronksaunders/simple-ionic-tabs-app-with-child-routes-protected-routes-1k24)

</article>

[View on GitHub](https://github.com/aaronksaunders/ionicv4-tabs-with-detail)

# 更...

这里有一个类似的实现，但是它包括[侧菜单/分割面板](https://ionicframework.com/docs/api/split-pane)以及[标签](https://ionicframework.com/docs/api/tabs)和认证

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[aaronksaunders](https://github.com/aaronksaunders)/[ion C4-side menu-auth](https://github.com/aaronksaunders/ionic4-sidemenu-auth)

### 用 Angular 路由器和侧菜单 UI 构建一个基本的 Ionic 4 登录流程

<article class="markdown-body entry-content container-lg" itemprop="text">

# ionic4-sidemenu-auth 节

用 Angular Router 和侧菜单 UI 构建一个基本的 Ionic 4 登录流程(现在有标签了！！)

### 更新至最新的 Ionic 版本

```
Ionic
   Ionic CLI                     : 5.4.13 (/Users/aaronksaunders/.nvm/versions/node/v10.15.1/lib/node_modules/ionic)
   Ionic Framework               : @ionic/angular 4.11.7
   @angular-devkit/build-angular : 0.803.21
   @angular-devkit/schematics    : 8.1.3
   @angular/cli                  : 8.1.3
   @ionic/angular-toolkit        : 2.1.1
Utility
   cordova-res                          : not installed
   native-run (update available: 0.3.0) : 0.2.9

System:

   NodeJS : v10.15.1 (/Users/aaronksaunders/.nvm/versions/node/v10.15.1/bin/node)
   npm    : 6.11.2
   OS     : macOS Catalina 
```

*   代码最近更新至 Ionic 的最新版本`"@ionic/angular": "^4.3.0",`
*   让它变得更加复杂
    *   证明
    *   侧菜单
    *   制表符
    *   选项卡详细信息页面
*   基于这里完成的伟大工作-[https://devdactic.com/ionic-4-login-angular/](https://devdactic.com/ionic-4-login-angular/)

## 它是如何工作的

我们在模块上放了一个`AuthGuard`,提供对所有成员相关页面和功能的访问。登录页面没有地址，因此可以自由访问。查看 angular.io 文档中关于[路线防护的更多信息](https://angular.io/guide/router#milestone-5-route-guards)

```
const routes
```

…</article>

[View on GitHub](https://github.com/aaronksaunders/ionic4-sidemenu-auth)