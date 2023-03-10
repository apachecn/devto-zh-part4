# 角反应式打字！

> 原文：<https://dev.to/joaqcid/angular-reactive-forms-typed-2j58>

角度反应式表单很棒，它们提供了模型和视图之间的双向数据绑定，支持同步/异步验证、事件处理和状态；但是它们没有提供打字支持。

幸运的是，将这个特性添加到反应式表单非常容易，我们将在本教程中看到如何做到这一点。

首先，我们将创建自己的通用版本的`FormGroup<T>`和`AbstractControl<T>`类。我们只需覆盖最低要求的方法和属性，使我们的表单类型化。让我们创建一个名为`forms.ts`的文件和下面的代码。

```
import {
  FormGroup as NgFormGroup,
  AbstractControl as NgAbstractControl,
  ValidatorFn,
  AbstractControlOptions,
  AsyncValidatorFn
} from '@angular/forms';
import { Observable } from 'rxjs';

export abstract class AbstractControl<T = any> extends NgAbstractControl {

  readonly value: T;
  readonly valueChanges: Observable<T>;

  abstract setValue(value: Partial<T> | T, options?: {
    onlySelf?: boolean;
    emitEvent?: boolean;
  }): void

  abstract patchValue(value: Partial<T> | T, options?: {
    onlySelf?: boolean;
    emitEvent?: boolean;
  }): void
}

export class FormGroup<T = any> extends NgFormGroup {
  readonly value: T;
  readonly valueChanges: Observable<T>;

  constructor(controls: { [key in keyof T]?: AbstractControl; },
    validatorOrOpts?: ValidatorFn | Array<ValidatorFn> | AbstractControlOptions | null,
    asyncValidator?: AsyncValidatorFn | Array<AsyncValidatorFn> | null) {
    super(controls, validatorOrOpts, asyncValidator);
  }

  patchValue(value: Partial<T> | T, options?: {
    onlySelf?: boolean;
    emitEvent?: boolean;
  }): void {
    super.patchValue(value, options);
  }

  get(path: Array<Extract<keyof T, string>> | Extract<keyof T, string> | string): AbstractControl | never {
    return super.get(path);
  }

  controls: {
    [key in keyof T]: AbstractControl<T[key]>;
  };
} 
```

接下来，我们将创建表单模型，在本例中是一个简单的注册。让我们创建一个名为`sign-up-form.ts`的文件。

```
export type SignUpForm = {
  email: string,
  password: string,
  optInNewsletter: boolean
} 
```

最后，我们将在组件中实现泛型`FormGroup`。我们用`SignUpForm`类型实例化泛型`FormGroup`。注意我们导入我们的`FormGroup`而不是 Angular 的。

```
//...
import { FormGroup } from './forms';

export class AppComponent implements OnInit {

  form = new FormGroup<SignUpForm>({
    email: new FormControl(''),
    password: new FormControl(''),
    optInNewsletter: new FormControl('')
  })

  ngOnInit() {
    this.form.patchValue({
      email: 'joaqcid@gmail.com',
      optInNewsletter: true
    })
  }

  submit() {
    console.log("typed forms", this.form.value)
  }
} 
```

在这里，我们可以看到，当我们创建 FormGroup 时，Typescript 如何选择所使用的类型并进行类型检查，或者当我们得到`value`时，我们如何返回定义的类型

[![Type-check form value](img/e33979d4d286fa13c76a3246dbfeb4b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q_GYuhDx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/v6gdlsa8v7yf5uqfnubi.png) 
*键入-检查表格值*

[![Type-check form model when instantiating FormGroup](img/e80d5dba86910dc072f2b77d253c88e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TbM7wsvj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h7n2xgis8ni6fjheil27.png) 
*类型-实例化表单组时检查表单模型*

[![Type-check form model when patching value](img/6a45351cb7fe41443b7c84673b023a2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pSoKnHA5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nx8247kzs9jbfnanlfps.png) 
*键入-检查表格模型时打补丁值*

[![Type-check field value when patching value](img/aa71c82c87453b96f52008407ba68d7e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WfE7Phr0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ilycydxrncp411pradyw.png) 
*打补丁时检查字段值*

酷！这不是很棒吗？类固醇表格！💉💊💉💊

最后，让我们扩展`FormControl<T>`类，以防我们也需要使用它们。

让我们添加下面的代码。

```
import {
  FormControl as NgFormControl,
  //...
} from '@angular/forms';

//...
export class FormControl<T = any> extends NgFormControl {
  readonly value: T;
  readonly valueChanges: Observable<T>;

  setValue(value: Partial<T> | T, options?: {
    onlySelf?: boolean;
    emitEvent?: boolean;
  }): void {
    super.setValue(value, options);
  }

  patchValue(value: Partial<T> | T, options?: {
    onlySelf?: boolean;
    emitEvent?: boolean;
  }): void {
    super.patchValue(value, options);
  }
} 
```

在我们的组件上，让我们导入自己的 FormControl 实现并构建一个。注意，我使用了 ng-bootstrap 中的自定义类型`NgbDate`。

```
import { ..., FormControl } from './forms';
import { NgbDate } from '@ng-bootstrap/ng-bootstrap';

export class AppComponent implements OnInit {
    //...
    formControl =  new FormControl<NgbDate>('')

    ngOnInit() {
    //...
    this.formControl.patchValue(new NgbDate(2019, 9, 11))
    }
} 
```

同样，通过使用 FormControl 的类型化版本，我们可以在修补、设置或获取值时对 FormControl 进行类型检查。

[![FormControl typed](img/3aa9cabcc1a4f817a8e61c00dc5cb0e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--I1_eROAY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9gt9lknh1s8c2jlft807.png) 
*表单控件键入*

## 结论

有了这几行代码，我们能够扩展 Angular 的反应式表单，使它们可以被键入，这可以帮助我们键入表单，如果我们需要的话，还可以添加其他自定义功能。修改行为时要小心，因为它可能会影响当前的表单行为。

您可以查看此 [StackBlitz](https://stackblitz.com/edit/angular-reactive-forms-typed) 中的完整示例。

这篇文章是在阅读了一个公开的 [Angular 的 github 问题](https://github.com/angular/angular/issues/13721)中的不同方法后写的，还有一些库，如 ngx-typed-forms，也提供类似的功能。

* * *

希望你喜欢这篇文章，如果你喜欢，请展示一些❤️.我经常写 Angular，Firebase 和 Google 云平台服务。如果你对更多的内容感兴趣，你可以在 [dev.to](https://dev.to/joaqcid) 、 [medium](https://medium.com/@joaqcid) 和 [twitter](https://twitter.com/joaqcid) 上关注我。