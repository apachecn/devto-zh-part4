# 无痛强类型角反应形式

> 原文：<https://dev.to/leonelngande/painless-strongly-typed-angular-reactive-forms-3pac>

几天前，当我在 Angular 中研究强类型反应形式时，我看到了[Alex Klaus](https://alex-klaus.com/stongly-typed-angular-forms/)写的这篇文章。鉴于反应式表单目前不支持强类型(参见问题 [#13721](https://github.com/angular/angular/issues/13721) 和 [#17000](https://github.com/angular/angular/issues/17000) )，他建议使用 [Daniele Morosinotto](https://twitter.com/dmorosinotto) 解决方案，该方案涉及利用类型脚本声明文件(`*.d.ts`)。

> 我见过的最优雅的解决方案是由 Daniele Morosinotto 提供的，他建议利用 TypeScript 声明文件(*.d.ts)来引入扩展标准接口的通用接口(例如 AbstractControl、FormControl 等)。).它没有引入任何新功能，也没有占用编译后的 JavaScript，但同时执行了强类型检查。太棒了。

采用该解决方案非常简单:

1.  从[下载`TypedForms.d.ts`他的要旨](https://gist.github.com/dmorosinotto/76a9272b5c45af1f78a61e7894df5777)并在你的项目中另存为`src/typings.d.ts`(Angular 6+已经知道如何使用这个文件，详见 [Angular docs](https://angular.io/guide/using-libraries#library-typings) )。
2.  开始使用新类型(`FormGroupTyped<T>`、`FormControlTyped<T>`等)。)每当你需要强类型验证的时候(参见[中的例子，要点](https://gist.github.com/dmorosinotto/76a9272b5c45af1f78a61e7894df5777))。

导入类型后，我的类型化表单声明现在看起来像这样:

```
form: FormGroupTyped<MyTypedInterface>; 
```

然而，我在让它与`FormBuilder`服务一起工作时遇到了问题，于是[联系了](https://twitter.com/_AlexKlaus/status/1143318167189504000)给他。他的建议是将生成的 FormGroup 转换成类型化的。

```
this.form = this.formBuilder.group({
     field1: null,
     field2: null
}) as FormGroupTyped<MyTypedInterface>; 
```

然而，哪一个成功了…现在我的 IDE 在 html 模板文件中显示了一个错误:

```
<form novalidate [formGroup]="form" (submit)="submit(form.value)"></form>

<!-- Error message: Type FormGroupTyped<MyTypedInterface> is not assignable to type FormGroup --> 
```

我现在可以接受这一点，直到 Angular 团队在反应式表单中增加了强类型，我知道原因。请在下面的评论中或在 [Twitter](https://twitter.com/leonelngande) 上分享你的想法。

2019 年 9 月 4 日更新:

我发现了一个解决模板错误的简单方法。简单地创建一个 getter，将类型化表单转换为 FormGroup 返回，这将导致 Angular 在模板中不再抱怨。

```
 get untypedForm() {
    return this.form as FormGroup;
  } 
```

```
<form novalidate [formGroup]="untypedForm" (submit)="submit(form.value)"></form>

<!-- No more error message in form template --> 
```