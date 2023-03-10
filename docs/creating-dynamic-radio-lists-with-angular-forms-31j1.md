# 用角度形式创建动态单选列表

> 原文：<https://dev.to/coryrylan/creating-dynamic-radio-lists-with-angular-forms-31j1>

当构建具有复杂表单的实际应用程序时，我们经常需要动态地创建表单。通常，我们需要加载数据，然后
基于该数据构建表单。在本教程中，我们将了解如何从异步数据源创建单选按钮列表。

在我们的例子中，我们将创建一个简单的单选按钮列表，它遍历订单列表
,供用户选择，就像下面的单选按钮列表一样。

```
<label><input type="radio" name="orders" value="1" checked />order 1</label>
<label><input type="radio" name="orders" value="2" />order 2</label>
<label><input type="radio" name="orders" value="3" />order 3</label>
<label><input type="radio" name="orders" value="4" />order 4</label> 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们需要在角度上创建我们的形式。第一步是将`ReactiveFormsModule`导入到我们的应用程序模块中。通过将
反应式表单模块导入我们的应用程序模块，我们为整个应用程序启用了反应式
表单 API。

```
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { ReactiveFormsModule } from '@angular/forms';

import { AppComponent } from './app.component';

@NgModule({
  imports:      [ BrowserModule, ReactiveFormsModule ],
  declarations: [ AppComponent ],
  bootstrap:    [ AppComponent ]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们需要使用`FormBuilder`服务创建表单。该服务使创建复杂的角度表单和管理验证变得容易。为了使用`FormBuilder`服务，我们通过在组件的构造函数中列出依赖性来使用 Angular 的依赖性注入系统。Angular 实例化并传入一个`FormBuilder`服务的实例供组件使用。

```
import { Component } from '@angular/core';
import { FormBuilder, FormGroup, FormArray, FormControl, ValidatorFn } from '@angular/forms';

@Component({
  selector: 'my-app',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  form: FormGroup;

  constructor(private formBuilder: FormBuilder) {
    this.form = this.formBuilder.group({
      orders: ['']
    });
  }

  submit() {
    console.log(this.form.value);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

表单构建器创建一个`FormGroup`实例。表单组包含一个到多个`FormControl`。我们在这个表单中的单个输入是我们的`orders`
单选列表输入。每个控件都有一个默认的初始值和一个一对多可选验证规则的列表
。

现在，我们将把数据硬编码到我们的组件中，我们将使用它来创建我们的单选列表输入。

```
import { Component } from '@angular/core';
import { FormBuilder, FormGroup, FormArray, FormControl, ValidatorFn } from '@angular/forms';

@Component({
  selector: 'my-app',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  form: FormGroup;
  orders = [];

  constructor(private formBuilder: FormBuilder) {
    this.form = this.formBuilder.group({
      orders: ['']
    });

    this.orders = this.getOrders();
  }

  getOrders() {
    return [
      { id: '1', name: 'order 1' },
      { id: '2', name: 'order 2' },
      { id: '3', name: 'order 3' },
      { id: '4', name: 'order 4' }
    ];
  }

  submit() { ... }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们使用 ngFor 遍历订单数据，在 HTML 模板中构建单选列表项。

```
<form [formGroup]="form" (ngSubmit)="submit()">

  <label *ngFor="let order of orders;">
    <input formControlName="orders" type="radio" name="orders" [value]="order.id" />
    {{order.name}}
  </label>

  <button>submit</button>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

对于数据中的每个订单，我们创建一个单选输入，并将订单 id 绑定为单个单选输入的值。需要注意的一点是，在 HTML 中将数据绑定到值属性时，值总是被视为字符串。

现在我们已经创建了动态单选按钮列表，让它异步获取数据。我们通常从 API 请求中获取数据。在这个例子中，我们使用一个可观察对象来模拟一个 API 请求，以发出我们的数据。

```
import { Component } from '@angular/core';
import { FormBuilder, FormGroup, FormArray, FormControl, ValidatorFn } from '@angular/forms';
import { of } from 'rxjs';

@Component({
  selector: 'my-app',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  form: FormGroup;
  orders = [];

  constructor(private formBuilder: FormBuilder) {
    this.form = this.formBuilder.group({
      orders: ['']
    });

    // mimic async orders
    of(this.getOrders()).subscribe(orders => {
      this.orders = orders;
      this.form.controls.orders.patchValue(this.orders[0].id);
    });
  }

  getOrders() {
    return [
      { id: 100, name: 'order 1' },
      { id: 200, name: 'order 2' },
      { id: 300, name: 'order 3' },
      { id: 400, name: 'order 4' }
    ];
  }

  submit() { console.log(this.form.value) }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用 RxJS `of()`操作符，我们可以通过 async 将我们的数据转换成，类似于我们通过 Angular 的 HTTP 客户端服务请求数据。我们订阅异步数据，并将其分配给 orders 属性。一旦我们的异步数据被加载，我们需要更新无线电列表，以具有初始默认值。我们可以通过在表单控件上使用`patchValue`方法来做到这一点。

```
of(this.getOrders()).subscribe(orders => {
  this.orders = orders;
  this.form.controls.orders.patchValue(this.orders[0].id);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果我们注销表单的值，我们应该从单选列表中看到所选订单
的`id`。

```
{ orders: 100 } 
```

Enter fullscreen mode Exit fullscreen mode

你可以在这里找到完整的工作演示！