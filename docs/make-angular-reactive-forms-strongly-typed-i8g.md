# 使角反应形式成为强类型。

> 原文：<https://dev.to/ushmidave/make-angular-reactive-forms-strongly-typed-i8g>

反应形式是复杂的，因此它们必须用各自的模型进行强类型化。

这可以通过`IFormGroup`来实现

让我们看看如何:

1)安装[@ rx web/反应式表单验证器](https://www.npmjs.com/package/@rxweb/reactive-form-validators)

> NPM I @ rx web/反应式表单验证器

2)创建用户模型类并声明 fullName 的属性。

```
import { prop } from "@rxweb/reactive-form-validators"

export class User{

  @prop()
  fullName:string;

} 
```

Enter fullscreen mode Exit fullscreen mode

3)现在创建一个组件，它使用`IFormGroup<User>`使你的表单被模型强类型化。

```
import { Component, OnInit } from '@angular/core';

import { RxFormBuilder,IFormGroup } from '@rxweb/reactive-form-validators';
import { User } from "./user.model"

@Component({
    selector: 'app-user',
    templateUrl: './user.component.html'
})
export class UserComponent implements OnInit {

    userFormGroup: IFormGroup<User>

    constructor(
        private formBuilder: RxFormBuilder
    ) { }

    ngOnInit() {
        this.userFormGroup  = this.formBuilder.formGroup(User) as IFormGroup<User>;
        this.userFormGroup.controls.fullName.setValue("John");
        let fullName = this.userFormGroup.value.fullName;

    }

} 
```

Enter fullscreen mode Exit fullscreen mode

4)在 HTML:
中创建用户表单

```
<div>
    <form [formGroup]="userFormGroup">
        <div class="form-group">
            <label>Full Name</label>
            <input type="text" formControlName="fullName" class="form-control" />
        </div>
    </form> </div> 
```

Enter fullscreen mode Exit fullscreen mode

请参考这个[工作示例](https://stackblitz.com/edit/angular-jmshh8)