# 一个有棱角的形式和指令的小技巧🃏

> 原文：<https://dev.to/angular/a-little-trick-with-angular-forms-and-directives-2mep>

想象一下，您希望在应用程序中的多个地方使用一个表单。表单将具有相同的布局，但验证将是不同的。

在我们的例子中，这种形式用于一个多步骤的程序中，并且在程序的每一步中，这种形式变得更加严格。你能让它保持干燥吗？我们遇到了这个问题，并提出了以下解决方案。

### 表单

举个简单的例子，我们有一个人的名字、联系信息和过敏症。
开始时，只需要名字。稍后，我们将需要该人的其他信息。

```
@Component({
  selector: 'person',
  template: `
    <div [formGroup]="form">
      <div>
        <label>Name</label>
        <input type="text" formControlName="name" />
      </div>

      <div>
        <label>Contact info</label>
        <input type="text" formControlName="contactInfo" />
      </div>

      <div>
        <label>Allergies</label>
        <input type="text" formControlName="allergies" />
      </div>

      <strong>{{ form.valid ? 'valid' : 'invalid' }}</strong>
    </div>
  `,
})
export class PersonComponent {
  form = this.formBuilder.group({
    name: ['', Validators.required],
    contactInfo: [''],
    allergies: [''],
  })

  constructor(private formBuilder: FormBuilder) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

默认情况下，我们将人名设为必填字段。

### 指令

通过使用角度指令，我们可以注入组件的实例，这允许我们访问表单。在我们的例子中，要改变表单的有效性，并需要更多的字段。

```
@Directive({
  selector: 'person[stage-one]',
})
export class StageOneDirective {
  constructor(host: PersonComponent) {
    host.form.get('contactInfo').setValidators([Validators.required])
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

为了确保该指令将注入`PersonComponent`,我们必须使该指令特定于 person 表单。为了实现这一点，我们可以使用组件的选择器作为前缀，并将指令的名称附加到它后面，`person[stage-one]`。这样，只有当指令作为属性添加到 person 组件时，才能使用该指令。
因为 person 组件上的表单被设置为 public，所以我们可以从我们的指令中访问表单。如果我们可以访问表单，我们就可以访问表单域和它们的验证器。

我们可以使用相同的方法来创建更严格的验证。

```
@Directive({
  selector: 'person[stage-two]',
})
export class StageTwoDirective {
  constructor(host: PersonComponent) {
    host.form.get('contactInfo').setValidators([Validators.required])
    host.form.get('allergies').setValidators([Validators.required])
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 用法

为了使这个小例子更完整，这就是在另一个组件中使用 person 组件和指令的方式。

```
<!-- Only the name is required -->
<person></person>

<!-- The name and the contactInfo are required -->
<person stage-one></person>

<!-- All fields are required -->
<person stage-two></person> 
```

Enter fullscreen mode Exit fullscreen mode

> 我发现这是一个结合控制值访问器的干净的解决方案，正如在[在企业环境中使用角度表单](https://timdeschryver.dev/posts/working-with-angular-forms-in-an-enterprise-environment)中所讨论的。

在接下来的[闪电战](https://stackblitz.com/edit/angular-forms-directive-trick)中玩转这个例子。