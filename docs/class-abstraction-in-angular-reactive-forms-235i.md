# 角反应形式中的类抽象

> 原文：<https://dev.to/ushmidave/class-abstraction-in-angular-reactive-forms-235i>

当在角度反应形式中使用模型驱动方法时，我们可能需要使用基类的属性到派生类中。

这里有一种方法可以将基类的属性访问到派生类中，同时隐藏继承类的复杂性。

第一步:
安装[***@ rx web/reactive-form-validators***](https://www.npmjs.com/package/@rxweb/reactive-form-validators)

> NPM I @ rx web/反应式表单验证器

步骤 2:
假设你有两个类`Product`和`Order`，你想使用产品的属性进入订单

```
import {  prop } from "@rxweb/reactive-form-validators"

export class Product{

  @prop()
  productId:number;

  @prop()
  productName:string;
}

export class Order extends Product  {
   @prop()
   orderId:number;

   @prop()
   orderType:string;
} 
```

Enter fullscreen mode Exit fullscreen mode

第三步:
使用`RxFormBuilder`的`formGroup`方法创建组件，以使用模型对象

```
import { Component, OnInit } from '@angular/core';
import { FormGroup } from "@angular/forms"

import { RxFormBuilder } from '@rxweb/reactive-form-validators';

import { Order } from './order.model';

@Component({
    selector: 'app-class-complete',
    templateUrl: './class-complete.component.html'
})
export class ClassCompleteComponent implements OnInit {

    userFormGroup: FormGroup

    constructor(
        private formBuilder: RxFormBuilder
    ) { }

    ngOnInit() {
        let order = new Order();
        this.userFormGroup = this.formBuilder.formGroup(order);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

步骤 4:
在组件 HTML 中创建表单

```
<form *ngIf="userFormGroup" [formGroup]="userFormGroup">
    <div class="form-group">
        <label>Product Id</label>
        <input type="text" formControlName="productId" class="form-control" />
    </div>
    <div class="form-group">
        <label>Product Name</label>
        <input type="text" formControlName="productName" class="form-control" />
    </div>
        <div class="form-group">
        <label>OrderId</label>
        <input type="text" formControlName="orderId" class="form-control" />
    </div>
     <div class="form-group">
        <label>Order Type</label>
        <input type="text" formControlName="orderType" class="form-control" />
    </div>
    <button [disabled]="!userFormGroup.valid" class="btn btn-primary">Submit</button> </form> 
```

Enter fullscreen mode Exit fullscreen mode

下面是[的工作示例](https://stackblitz.com/angular/kdmmlrbbynx?file=src%2Fapp%2Fclass-complete.component.ts)

如有疑问/建议，请在下面评论