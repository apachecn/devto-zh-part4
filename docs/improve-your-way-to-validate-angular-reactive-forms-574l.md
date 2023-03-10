# 改进验证角反应形式的方法

> 原文：<https://dev.to/ushmidave/improve-your-way-to-validate-angular-reactive-forms-574l>

当涉及到面向大型数据的表单时，验证控件的复杂性会增加，因此有必要使用更好、更有效的方法来验证它们。

最有效的方法之一是基于装饰器的验证，使用它的好处是您可以使用基于模型的策略来构造角度反应形式，其中您只需要在类属性上添加一个装饰器`@alpha()`，`@required()`，您不需要在 HTML 中添加任何额外的代码或在组件中创建任何自定义验证函数

让我们通过几个步骤来实现它:

1)安装[@ rx web/反应式表单验证器](https://www.npmjs.com/package/@rxweb/reactive-form-validators)

> npm 安装[@ rx web/反应式表单验证器](https://www.npmjs.com/package/@rxweb/reactive-form-validators)

2)您还需要在 app.module 中导入`RxReactiveFormsModule`。

```
 import { NgModule } from '@angular/core';
  import { BrowserModule } from '@angular/platform-browser';
  import { FormsModule,ReactiveFormsModule } from '@angular/forms';

  import { AppComponent } from './app.component';

  import {  RxReactiveFormsModule } from "@rxweb/reactive-form-validators"
  @NgModule({
  imports:      [ BrowserModule, 
  FormsModule,ReactiveFormsModule,RxReactiveFormsModule ],
  declarations: [AppComponent],
  bootstrap:    [ AppComponent]
  })
  export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

3)创建用户模型:
让我们创建一个用户模型类，并在其中创建属性，如 firstName、lastName 和 username、password 和 confirmPassword，并传递各自的验证装饰器。

```
import { required, compare, alpha, minLength } from "@rxweb/reactive-form-validators";

export class User {

@required()
firstName:string;

@required()
lastName:string;

@required()
@alpha()
userName:string;

@required()
@minLength({value:8})
password:string;

@required()
@compare({fieldName:'password'})
confirmPassword:string;

} 
```

Enter fullscreen mode Exit fullscreen mode

4)创建用户组件:
现在，让我们创建一个用户组件，用于基于我们的模型类构建反应式表单。为此，您需要导入`RxFormBuilder`并使用用户模型类创建 formGroup 对象。

```
import { Component, OnInit } from '@angular/core';
import { FormGroup } from "@angular/forms"
import { RxFormBuilder } from '@rxweb/reactive-form-validators';
import { User } from './user.model';
@Component({
selector: 'app-user',
templateUrl: './user.component.html'
})
export class UserComponent implements OnInit {
userFormGroup: FormGroup 
constructor(
private formBuilder: RxFormBuilder 
) { }
  ngOnInit() 
  {
  let user = new User();
  this.userFormGroup = this.formBuilder.formGroup(user); 
}
} 
```

Enter fullscreen mode Exit fullscreen mode

5)创建用户表单:
创建一个用户表单，在`formGroup`元素内将所有属性定义为 formControl

```
<h1 class="bd-title" id="content">User Registration Form</h1> <br/>
<form *ngIf="userFormGroup" [formGroup]="userFormGroup">
<div class="form-group">
<label>First Name</label> <input type="text" formControlName="firstName" class="form-control" />
<small class="form-text text-danger" *ngIf="userFormGroup.controls.firstName.errors">{{userFormGroup.controls.firstName.errors.required.message}}<br/></small> </div> <div class="form-group">
<label>Last Name</label> <input type="text" formControlName="lastName" class="form-control" />
<small class="form-text text-danger" *ngIf="userFormGroup.controls.lastName.errors">{{userFormGroup.controls.lastName.errors.required.message}}<br/></small> </div> <div class="form-group">
<label>User Name</label> <input type="text" formControlName="userName" class="form-control" />
<small class="form-text text-danger" *ngIf="userFormGroup.controls.userName.errors && userFormGroup.controls.userName.errors.required">{{userRegistrationFormGroup.controls.userName.errors.required.message}}<br/></small> <small class="form-text text-danger" *ngIf="userFormGroup.controls.userName.errors && userFormGroup.controls.userName.errors.alpha">{{userFormGroup.controls.userName.errors.alpha.message}}<br/></small> </div> <div class="form-group">
<label>Password</label> <input type="password" formControlName="password" class="form-control" />
<small class="form-text text-danger" *ngIf="userFormGroup.controls.password.errors && userFormGroup.controls.password.errors.required">{{userFormGroup.controls.password.errors.required.message}}</small> <small class="form-text text-danger" *ngIf="userFormGroup.controls.password.errors && userFormGroup.controls.password.errors.minLength">{{userFormGroup.controls.password.errors.minLength.message}}<br/></small> </div> <div class="form-group">
<label>Confirm Password</label> <input type="password" formControlName="confirmPassword" class="form-control" />
<small class="form-text text-danger" *ngIf="userFormGroup.controls.confirmPassword.errors && userFormGroup.controls.confirmPassword.errors.compare">{{userFormGroup.controls.confirmPassword.errors.compare.message}}<br/></small> </div> <button [disabled]="!userFormGroup.valid" class="btn btn-primary">Submit</button> </form> 
```

Enter fullscreen mode Exit fullscreen mode

6)全局配置验证消息

用于在`formControl`无效时显示验证消息。您需要在应用程序组件中对其进行全局配置

你必须使用`ReactiveFormConfig` class set 方法并传递验证消息对象。

```
ReactiveFormConfig.set({
  "validationMessage": {
     "required": "This field is required.",
     "minLenght":"Enter minimum length of {{0}} characters.",
     "compare":"The value should be matched with {{0}}.",
     "alpha":"you can only enter alphabets."
}
}); 
```

Enter fullscreen mode Exit fullscreen mode

请参考[工作示例代码](https://stackblitz.com/edit/rxweb-reactive-form-validation-angular-user-registration-7fh1vv?file=src/app/app.component.ts)

使用这种方法的其他好处:

### 条件验证:

在某些情况下，您可能需要根据某些条件触发验证。要使用基于 decorator 的方法实现它，必须在属性上方的 decorator 中传递参数`conditionalExpression`。

```
import {required} from "@rxweb/reactive-form-validators"

export class User{

  @required()  
  firstName:string;

  @required({conditionalExpressions:(x=>x.firstName == "John")})
  lastName:string;

} 
```

Enter fullscreen mode Exit fullscreen mode

请参考[工作示例](https://stackblitz.com/edit/rxweb-reactive-form-validation-angular-user-registration-fyy96m)

### 显示自定义消息:

如果您想在一个特定的字段上显示一个定制的验证消息，您必须在属性上方的装饰器中的`message`参数中传递它。

```
import {required} from "@rxweb/reactive-form-validators"

export class User{

  @required()  
  firstName:string;

  @required({message:"Last Name is required"})
  lastName:string;

} 
```

Enter fullscreen mode Exit fullscreen mode

请参考[工作示例](https://stackblitz.com/edit/rxweb-reactive-form-validation-angular-user-registration-dcfngp)

这个验证框架有几个其他的验证器，可以直接在组件中使用，而不需要创建定制的验证函数

如果您有任何问题/建议，请在下面分享您的意见。