# 以角度反应形式显示错误信息的最佳方式

> 原文：<https://dev.to/ushmidave/best-way-to-show-error-messages-in-angular-reactive-forms-3e31>

当单个 formControl 中涉及多个验证时，我们需要在 HTML 中放置多个*ngIf，并绑定各个验证器的错误消息，这使得代码很笨拙。

这里，我们将讨论以一种更好、更简单的方式显示错误信息的方法。

我们建立了一个 userInfo 表单，用户可以在其中输入用户名和密码等详细信息，用户名包含一个必需的验证器，密码包含必需的和密码验证。我们应该显示错误消息，而不使用*ngIf 条件，我将使用[@ rx web/reactive-form-validators](https://www.npmjs.com/package/@rxweb/reactive-form-validators)验证框架的密码验证。这个验证框架提供了 50 多个跨域验证的验证器。

先安装[@ rx web/reactive-form-validators](https://www.npmjs.com/package/@rxweb/reactive-form-validators)。

> npm 安装@ rx web/反应式表单验证器

现在，在根模块中注册“RxReactiveFormsModule”。

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

现在，我们创建具有各自 formControls 的 userInfo formGroup，并在具有单个或多个验证器的 formControls 上绑定 errorMessage。

# 单一错误信息

首先，我们将创建用户组件，并将单个 formControl 分组到 userInfo formGroup 中。

```
 import { Component, OnInit } from '@angular/core';
  import { FormGroup, FormBuilder, } from "@angular/forms"
  import { RxwebValidators,RxFormBuilder} from '@rxweb/reactive-form-validators';

  @Component({
    selector: 'app-user',
    templateUrl: './user.component.html'
  })
  export class UserComponent implements OnInit {
    userFormGroup: FormGroup

    constructor(
        private formBuilder: RxFormBuilder )
    { }

    ngOnInit() {
        this.userFormGroup = this.formBuilder.group({
            userName:['', RxwebValidators.required()]
        });

    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将在 html 中的 formControl 上绑定`errorMessage`属性。

```
<div>
    <form  [formGroup]="userFormGroup">
      <div class="form-group">
        <label>UserName</label>
        <input type="text" formControlName="userName" class="form-control"  />
       <small class="form-text text-danger">{{userFormGroup.controls.userName.errorMessage}}<br/></small>   
      </div>
      <button [disabled]="!userFormGroup.valid" class="btn btn-primary">Submit</button>
    </form>
  </div> 
```

Enter fullscreen mode Exit fullscreen mode

请参考[工作示例](https://stackblitz.com/edit/angular-emf1yi?file=src/app/errormessage-single.component.html)

# 多个错误信息

我们将在组件
的同一个表单组中创建密码的表单控件

```
 import { Component, OnInit } from '@angular/core';
  import { FormGroup, FormBuilder, } from "@angular/forms"
  import { RxwebValidators,RxFormBuilder} from '@rxweb/reactive-form-validators';

  @Component({
    selector: 'app-user',
    templateUrl: './user.component.html'
  })
  export class UserComponent implements OnInit {
    userFormGroup: FormGroup

    constructor(
        private formBuilder: RxFormBuilder )
    { }

    ngOnInit() {
        this.userFormGroup = this.formBuilder.group({ 
              password : ['', 
  [RxwebValidators.required(),RxwebValidators.password({ validation: { maxLength: 
  10, minLength: 5, digit: true, specialCharacter: true } })]],
        });

    }
  } 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将在 html 中的 formControl 上绑定`errorMessages`属性，并使用*ngFor 显示它们。

```
<div>
  <form  [formGroup]="userFormGroup">
        <div class="form-group">
      <label>Password</label>
      <input type="text" formControlName="password" class="form-control"  />
     <small class="form-text text-danger" *ngFor="let errorMessage of userFormGroup.controls.password['errorMessages']">
       {{errorMessage}}<br/>
     </small>
    </div> 
    <button [disabled]="!userFormGroup.valid" class="btn btn-primary">Submit</button>
  </form> </div> 
```

Enter fullscreen mode Exit fullscreen mode

请参考[工作示例](https://stackblitz.com/edit/angular-31fm66?file=src/app/errormessage-complete.component.ts)

如果您有任何问题/建议，请在下面分享您的意见。