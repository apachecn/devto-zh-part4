# candeactivate:确认用户是否要退出页面并放弃更改

> 原文：<https://dev.to/felipedsc/candeactivate-confirmando-se-o-usuario-deseja-abandonar-a-pagina-e-descartar-alteracoes-5bma>

### 导言

您可能已经遇到了一些弹出窗口，通知您表单中有更改，如果您退出页面，则会丢弃这些更改。
忘记保存表格是比较常见的，所以在本文中，我将演示一个简单的实现，其功能由角自身提供，即**【candeactivette】**。

* * *

### 摘要

我们将创建一个将应用于路线的**守护**。此 **guard** 将有一个界面，由我们的组件实现，当用户可以离开页面 e **false** 时，此实现将返回一个 **true** 。**简单，不是吗？**

* * *

## 让我们举个例子

此示例包含一个表单和两个链接之间的导航。当用户在表单中修改了某个内容但未保存并尝试转到“其他页面”时，将显示一条确认消息。

### 守护创作

**OnComponentDeactivate** 是组件将实现的接口，它可以同步或异步返回一个 **boolean** 或 **UrlTree** 。

```
import { ActivatedRouteSnapshot, CanDeactivate, RouterStateSnapshot, UrlTree } from '@angular/router';
import { Observable } from 'rxjs';

export interface OnComponentDeactivate {
  canDeactivate: () => Observable<boolean> | Promise<boolean> | boolean | UrlTree;
}

export class CanDeactivateGuard implements CanDeactivate<OnComponentDeactivate> {
  constructor() { }

  canDeactivate(
    component: OnComponentDeactivate,
    currentRoute: ActivatedRouteSnapshot,
    currentState: RouterStateSnapshot,
    nextState: RouterStateSnapshot
  ): Observable<boolean | UrlTree> | Promise<boolean | UrlTree> | boolean | UrlTree {
    return component.canDeactivate();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*备注。:将 UrlTree 与 Candace 一起使用并不常见。我不认为这是预期的行为，但当返回 UrlTree 时，角会再次尝试“禁用”当前页面，最终产生无限循环。我找到的唯一解决方法是在 candeactivate 内进行以下验证:*

```
 const response = component.canDeactivate();

        if (response instanceof UrlTree 
            && nextState.url == response.toString()) {
            return true;
        }

        return  response; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 包括警卫服务提供商

此**守护**将作为一项服务运行，并应位于**提供者**部分。我的 **app.module** 修改如下:

```
@NgModule({
  declarations: [
    AppComponent,
    FormularioComponent,
    OutraPaginaComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    ReactiveFormsModule
  ],
  providers: [
    CanDeactivateGuard
  ],
  bootstrap: [AppComponent]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 值班警卫

我们快到了。我们还必须定义哪条路线会触发**【candeactivetateguard】**。在我的 **app-routing.module.ts** 中，我定义了我的窗体组件将触发它:

```
const routes: Routes = [
  { path: "", redirectTo: "/formulario", pathMatch: "full" },
  { path: "formulario", component: FormularioComponent, canDeactivate: [CanDeactivateGuard] },
  { path: "outra", component: OutraPaginaComponent }
]; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 实现一个接口

现在只剩下在我们的组件上实现接口了。在本例中，我使用 javascript 的方法*确认*，该方法在用户响应后将返回 **boolean** 。没有什么可以阻止您使用更漂亮的模式或应用任何其他逻辑来允许或不允许关闭组件，只要它提供了一个预期的返回。
我通过**脏**属性检查了用户是否对表格做了任何更改。表单保存后，我通过**【markaspriste()**重置此状态，用户不再需要确认是否要退出页面:

```
import { Component, OnInit } from '@angular/core';
import { Router } from '@angular/router';
import { FormGroup, FormControl, Validators } from '@angular/forms';
import { OnComponentDeactivate } from '../can-deactivate.guard';
import { Observable } from 'rxjs';
import { UrlTree } from '@angular/router';

@Component({
  selector: 'app-formulario',
  templateUrl: './formulario.component.html',
  styleUrls: ['./formulario.component.css']
})
export class FormularioComponent implements OnInit, OnComponentDeactivate {

  form: FormGroup;

  constructor(private router: Router) { }

  ngOnInit() {
    this.form = new FormGroup({
      "nome": new FormControl(null, [Validators.required, Validators.minLength(3)])
    });
  }

  salvar() {
    this.form.markAsPristine();
  }

  canDeactivate(): boolean | Observable<boolean> | Promise<boolean> | UrlTree {
    if (this.form.dirty) {
      return confirm("As alterações no formulário não foram salvas e serão descartadas, deseja prosseguir?");
    } else {
      return true;
    }
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### Vamos ver functionando？

在表单域中输入任何内容，然后尝试导航到“其他页面”。[如果您没有看到下面的示例，请单击](https://stackblitz.com/edit/fdsc-can-deactivate?embed=1&file=src/app/formulario/formulario.component.ts)[此处](https://stackblitz.com/edit/fdsc-can-deactivate?embed=1&file=src/app/formulario/formulario.component.ts)。

[https://stackblitz.com/edit/fdsc-can-deactivate?embed=1&&](https://stackblitz.com/edit/fdsc-can-deactivate?embed=1&&)