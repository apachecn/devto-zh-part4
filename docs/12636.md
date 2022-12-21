# 从旧路由到新路由的惰性路由的动态导入:Angular 8

> 原文：<https://dev.to/teclogiq/dynamic-imports-for-lazy-routes-from-old-towards-new-angular-8-dc1>

**延迟加载的路由**现在使用标准的**动态导入**语法，而不是自定义字符串。这意味着当模块丢失或拼写错误时，TypeScript 和 linters 将能够更好地抱怨。

所以一个延迟加载的导入看起来像这样:

`{ path: '/cart', loadChildren: './cart/cart.module#CartModule' }`

现在看起来像这样:

`{ path:`/推车`, loadChildren: () => import(`。/cart/cart.module `).then(m => m.CartModule) }`

如果你使用 **ng upgrade** 命令来升级你的应用程序，语法上的变化会被考虑进去。

点击了解更多关于 Angular 8 [的更新](https://medium.com/@teclogiqs/angular-8-insights-of-the-latest-version-of-angular-11b1f0b19029)