# 使用 Akita 实现角度形式的交叉组件验证

> 原文：<https://dev.to/arielgueta/implement-cross-component-validation-in-angular-forms-using-akita-1g0p>

在这篇文章中，我想向你展示如何使用`AkitaFormsManager`插件并获得与[这篇](%E2%80%9Chttps://rangle.io/blog/ngrx-forms/%E2%80%9D) ngrx 文章中描述的相同(甚至更多)的结果。

## 外挂能力

*   Persist Forms -当用户导航回表单时，自动保存当前控件值并根据存储中的值更新表单值。

*   提供了一个 API，因此我们可以从任何地方查询表单的值和属性。这对于多步表单、跨组件验证等非常有用。

### 安装

`npm i @datorama/akita-ng-forms-manager`

### 创建路线

我们将有两页，一页是`PersonPage`，另一页是`ConfigPage` :

```
const routes: Routes = [
  {
    path: '',
    component: PersonComponent,
    pathMatch: 'full'
  },
  {
    path: 'config',
    component: ConfigComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {} 
```

### 人的分量

现在让我们创建`PersonComponent`表单组件:

```
import { Component, OnInit, OnDestroy } from '@angular/core';
import { FormGroup, FormControl, Validators } from '@angular/forms';
import { AkitaNgFormsManage } from '@datorama/akita-ng-forms-manager';

@Component({
  selector: 'person-form',
})
export class HomeComponent implements OnInit, OnDestroy {
  person = new FormGroup({
    firstName: new FormControl(),
    lastName: new FormControl(),
    age: new FormControl(21, Validators.required)
  });

  constructor(private formsManager: AkitaNgFormsManager) {}

  ngOnInit() {
    this.formsManager.upsert('person', this.person);
  }

  ngOnDestroy() {
    this.formsManager.unsubscribe();
  }
} 
```

和模板:

```
<form [formGroup]="person">
  <input formControlName="firstName" placeholder="First Name" />
  <input formControlName="lastName" placeholder="Last Name" />
  <input
    formControlName="age"
    placeholder="Age"
    type="number"
    [ngStyle]="person.invalid && { border: '1px solid red' }"
  />
</form> 
```

这个插件中我最喜欢的部分是我们仍然使用现有的 Angular API 来创建我们的表单。

我们创建了一个基本的反应式表单组，其中包含此人的详细信息。我们注入`AkitaNgFormsManager`并调用`upsert`方法，给它表单名称和`formGroup`。

从那时起，`AkitaNgFormsManager`将跟踪表单值的变化并相应地更新商店。当用户导航回窗体时，它还保持窗体状态。

我们还可以在开发工具中看到一切:

[![](img/5f4db74544ea5c5684ff186130e11524.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qZIliozy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/r5wf5sjeomim92afdedg.gif)

### 配置组件

现在，让我们创建`ConfigComponent` :

```
import { Component, OnInit, OnDestroy } from '@angular/core';
import { FormGroup, FormControl } from '@angular/forms';
import { AkitaNgFormsManager } from '@datorama/akita-ng-forms-manager';

@Component({
  selector: 'app-config',
  templateUrl: './config.component.html',
  styleUrls: ['./config.component.css']
})
export class ConfigComponent implements OnInit, OnDestroy {
  config = new FormGroup({
    minAge: new FormControl(21)
  });

  constructor(private formsManager: AkitaNgFormsManager) {}

  ngOnInit() {
    this.formsManager.upsert('config', this.config);
  }

  ngOnDestroy() {
    this.formsManager.unsubscribe();
  }
} 
```

和以前一样，我们创建一个包含一个`FormControl`的`FormGroup`——用户在`PersonComponent`表单中**需要**输入的最小年龄。我们还用`AkitaNgFormsManager`注册了表单，这样我们就可以从应用程序中的任何其他组件查询它。

### 交叉组件验证

现在，让我们回到`PersonComponent`，看看我们如何基于`minAge`控件值:
使`age`控件验证动态化

```
import { Component, OnInit, OnDestroy } from '@angular/core';
import { FormGroup, FormControl, Validators } from '@angular/forms';
import { AkitaNgFormsManager, setValidators } from '@datorama/akita-ng-forms-manager';

export class HomeComponent implements OnInit, OnDestroy {
  person = new FormGroup({
    firstName: new FormControl(),
    lastName: new FormControl(),
    age: new FormControl(21, Validators.required)
  });

  dispose;

  constructor(private formsManager: AkitaNgFormsManager) {}

  ngOnInit() {
    this.formsManager.upsert('person', this.person);

    this.dispose = this.formsManager.selectValue<number>('config', 'minAge')
      .subscribe(minAge => {
        setValidators(this.person.get('age'), [Validators.min(minAge)]);
      });
  }

  ngOnDestroy() {
    this.formsManager.unsubscribe();
    this.dispose.unsubscribe();
  }
} 
```

我们可以查询在`AkitaNgFormsManager`中注册的任何表单。在我们的例子中，我们使用`selectValue`方法传递我们需要的表单名称和表单字段。注意，我们也可以使用泛型来推断值。

现在唯一剩下的事情就是调用`setValidators`方法(由插件提供),传递控制和一个`Validators`数组。

[![](img/a7c1324e4940b65d95c5607df9279553.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ovJJZd2j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hvv1xfn7opp68ouslve1.gif)

为了保留现有的验证器，我们可以做:

```
this.dispose = this.formsManager.selectValue<number>('config', 'minAge')
 .subscribe(minAge => {
  const age = this.person.get('age');
  const validators = age.validator;
  setValidators(age, [...validators, Validators.min(minAge)]);
}); 
```

在[文档](https://netbasal.gitbook.io/akita/angular-plugins/angular-forms-manager)中阅读更多关于插件的信息。