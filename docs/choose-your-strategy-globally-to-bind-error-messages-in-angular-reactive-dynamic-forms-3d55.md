# 选择您的全局策略，以角度反应动态形式绑定错误消息

> 原文：<https://dev.to/ushmidave/choose-your-strategy-globally-to-bind-error-messages-in-angular-reactive-dynamic-forms-3d55>

在使用角度验证时，我们需要根据应用程序的需要绑定错误消息，比如(1)OnSubmit，(2)OnDirty，(3)on touch，基于此，我们通常使用多个*ngIf 条件或通过在组件中配置它来绑定`errorMessages`。

但是如何在整个应用程序中全局定义错误消息策略呢，在本文中我们将看到如何在整个应用程序中使用相同的错误消息策略。

这可以通过以下步骤实现:

(1)在项目中安装两个包，这两个包的安装命令如下所述:

> NPM install @ rx web/reactive-dynamic-forms

这个包用于构建模型驱动的动态表单。
有关反应式动态表单的更多信息，请参考这篇关于构建角度动态表单的新方法的[文章](https://medium.com/@oojhaajay/new-way-to-build-dynamic-reactive-form-in-angular-c50978c66d99)。

> npm 安装@ rx web/反应式表单验证器

(2)现在我们要在根模块中注册`RxReactiveDynamicFormsModule`和`RxReactiveFormsModule`模块。

```
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { FormsModule,ReactiveFormsModule } from '@angular/forms'; 

import { RxReactiveDynamicFormsModule } from "@rxweb/reactive-dynamic-forms"
import { RxReactiveFormsModule } from "@rxweb/reactive-form-validators"

@NgModule({
  imports:      [ BrowserModule, 
                    FormsModule,ReactiveFormsModule,
                    RxReactiveFormsModule,RxReactiveDynamicFormsModule 
                ],
  declarations: [  ],
  bootstrap:    [  ]
})
export class AppModule { } 
```

Enter fullscreen mode Exit fullscreen mode

(3)您需要在 app 组件中配置全局消息及其策略:

```
import { Component,OnInit } from '@angular/core';
import { ReactiveFormConfig } from '@rxweb/reactive-form-validators';
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
})
export class AppComponent implements OnInit {

  ngOnInit(){
    //if you want to apply global configuration then use below code. 
    ReactiveFormConfig.set({"validationMessage":{"required":"This field is 
  required"},
  "reactiveForm":{"errorMessageBindingStrategy":1}});
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

> 默认情况下，errorMessageBindingStrategy 是 ErrorBindingStrategy.none。

错误消息绑定策略可以是

### errorbindingstrategy . none:

没有绑定错误信息的条件，一旦 FormControl 无效，就会绑定错误信息。

### errorbindingstrategy . onsubmit:

每当提交的 RxFormGroup 属性为 true 时，将绑定无效的 FormControl 错误消息。

### errorbindingstrategy . on dirty:

一旦 FormControl 是脏的，那么无效的 FormControl 错误消息将被相应地绑定。

应使用 markAsDirty()将 FormControl 标记为脏的

### errorbindingstrategy . ontuched:

一旦 FormControl 被触摸，无效的 FormControl 错误信息将被相应地绑定。

应该使用 markAsTouched()将 FormControl 标记为 touched

### errorbindingstrategy . ondirtyortouted:

一旦 FormControl 变脏或被触摸，无效的 FormControl 错误信息将被相应地绑定。

(4)让我们开始构建 json，假设我们有两个字段 firstName 和 lastName，我们想要应用所需的验证

```
{
            name:"firstName",
            type:"text",
            ui:{
              "label":"FirstName"
            },
            validators:{
              required:true
            }
        },
        {
            name:"lastName",
            type:"text",
            ui:{
               "label:"LastName" },
            validators:{
              required:true
            }
        } 
```

Enter fullscreen mode Exit fullscreen mode

(5)我们必须在组件中导入`RxDynamicFormBuilder`、`DynamicFormBuildConfig`和`DynamicFormConfiguration`。RxDynamicFormBuilder 我们在构造函数中定义的属性，用于解析依赖关系。
我们还在组件中导入 server_data.ts 文件。
下面是组件的代码:

```
import { Component, Input,OnInit } from '@angular/core';
import { RxDynamicFormBuilder,DynamicFormBuildConfig } from "@rxweb/reactive-dynamic-forms"
import {SERVER_JSON} from './server-json';

@Component({
  selector:"dynamic-validation",
  templateUrl:'./dynamic-validation.component.html'
})
export class DynamicValidationComponent implements OnInit  {

    dynamicFormBuildConfig:DynamicFormBuildConfig;   

    formData:any[] = SERVER_JSON;

    uiBindings:string[] = ["firstName","lastName"];

    constructor(private dynamicFormBuilder:RxDynamicFormBuilder){}

    ngOnInit(){
            this.dynamicFormBuildConfig = this.dynamicFormBuilder.formGroup(this.formData);          
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

(6) html 实现:

```
<form [formGroup]="dynamicForm.formGroup">
    <div viewMode="basic" [rxwebDynamicForm]="dynamicForm" [uiBindings]="uiBindings">
    </div>
    <button [disabled]="!dynamicForm.formGroup.valid" type="submit" class="btn btn-primary">Submit</button> </form> 
```

Enter fullscreen mode Exit fullscreen mode

请参考工作[示例](https://stackblitz.com/edit/angular-rjkntp-hco8zy?file=src/app/dynamic-validation.component.ts)

如果您有任何疑问或建议，请在下面评论。