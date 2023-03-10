# 支持[(ngModel)]的自定义角度复选框

> 原文：<https://dev.to/pluralsight/custom-angular-checkbox-with-ngmodel-support-3om9>

今天，我需要在 Angular 8 应用程序的复选框中添加一些自定义样式。我找到了一个简单的例子，完全用 CSS 和 HTML 实现，不包含任何命令性代码。这个发现让我很高兴，我将 CSS 复制到我的页面中，并做了一些调整。然后我想知道怎样才能把它变成一个我可以重用的定制组件。事实证明，这比我想象的要难，但不是压倒性的。这里描述了解决方案。

# 自定义复选框

我试图复制的 UI 设计需要一个自定义风格的复选框。这个需要一个绿色方框内的白色复选标记，未选中时需要一个灰色方框。此外，该设计要求 checkbox 控件本身比正常的要大，并且对标签的间距有特殊的要求。

## CSS

我将从展示最终的 CSS 开始，以防你想继续，但是我不会描述样式。这篇文章的重点是展示角分量。如果你想要一个更完整的样式解释，这里是我得到我使用的 HTML 和 CSS 的地方。[链接:W3School](https://www.w3schools.com/howto/howto_css_custom_checkbox.asp) 。我稍微调整了一下他们的 CSS，但幅度不大。

```
 /* The container */
.cb-container {
  display: inline-block;
  position: relative;
  padding-left: 30px;
  margin-bottom: 12px;
  cursor: pointer;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  line-height: 22px;
}

/* Hide the browser's default checkbox */
.cb-container input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

/* Create a custom checkbox */
.checkmark {
  position: absolute;
  top: 0;
  left: 0;
  height: 20px;
  width: 20px;
  background-color: #ffffff;
  border: 1px solid #00aa00;
  border-radius: 2px;
}

/* On mouse-over, add a grey background color */
.cb-container:hover input ~ .checkmark {
  background-color: #ffffff;
}

/* When the checkbox is checked, add a blue background */
.cb-container input:checked ~ .checkmark {
  background-color: #00aa00;
  border-color: #00aa00;
}

/* Create the checkmark/indicator (hidden when not checked) */
.checkmark:after {
  content: "";
  position: absolute;
  display: none;
}

/* Show the checkmark when checked */
.cb-container input:checked ~ .checkmark:after {
  display: block;
}

/* Style the checkmark/indicator */
.cb-container .checkmark:after {
  left: 6px;
  top:0px;
  width: 7px;
  height: 17px;
  border: solid white;
  border-width: 0 4px 4px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
} 
```

Enter fullscreen mode Exit fullscreen modecheckbox.component.scss

## 组件

按照我想要的方式设计 checkbox 后，我的下一个想法是将它打包成一个 Angular 组件，这样我就可以在其他页面上重用它。然后我意识到 UX 的设计要求复选框标签应该总是在右边，同样的大小，离框同样的距离。在这一点上，它听起来真的越来越像一个定制组件。由于我现在正在考虑一个定制组件，我应该考虑让它与`[(ngModel)]`一起工作。

下面是我设想的自定义复选框组件的使用方式。

```
<app-checkbox 
  text="Remember Me" 
  [disabled]="!isLoggedIn" 
  [(ngModel)]="remember">
</app-checkbox> 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我需要传入一个用于标签的字符串，一个用于设置`disabled`属性的布尔值，以及复选框本身的值的双向绑定。前两个很简单。第三个需要一些自定义的角度代码。

### 创建组件

首要任务是在 Angular CLI 的帮助下创建组件。

```
ng generate component --module shared --inline-template --spec false shared/checkbox 

CREATE src/app/shared/checkbox/checkbox.component.scss (0 bytes)
CREATE src/app/shared/checkbox/checkbox.component.ts (292 bytes)
UPDATE src/app/shared/shared.module.ts (1080 bytes) 
```

Enter fullscreen mode Exit fullscreen mode

我选择了`--inline-template`选项，因为组件的整个 HTML 模板只有三行，我们很快就会看到。

该组件没有业务逻辑可言，所以我跳过了测试。我以后可能会后悔，但现在就够了。这个命令创建了上面列出的两个文件，并在我现有的 SharedModule 中添加了一个导入。想要包含 my CheckboxComponent 的任何其他页面或组件都需要在它们自己的模块中导入这个 SharedModule。

### 添加 HTML 模板

接下来，我需要向新创建的组件添加适度的 HTML。下面是 HTML:

```
<label class="cb-container">{{text}}
<input type="checkbox" [checked]="isChecked" (change)="onChanged($event)" (blur)="onBlur($event)" [disabled]="disabled">
<span class="checkmark"></span></label>
}) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，有一个`<label>`标签包装了一个普通的 HTML `<input type="checkbox">`标签。标签从组件的`text`字段单向绑定。即使标签文本首先出现，CSS 也确保复选框本身被拉到标签的左侧。

接下来，输入的`checked`属性将其从组件的`isChecked`字段单向绑定。这

输入的`change`事件连接到组件的`onChange()`函数，将 DOM 事件作为参数传递。同样，它的`blur`事件连接到组件的`onBlur()`函数。

最后，输入的`disabled`属性单向绑定到组件的`disabled`字段。不出所料，如果设置为“truthy”值，这将导致复选框被禁用。

### 组件字段

HTML 模板完成后，下一步是创建引用的字段和函数。我在组件类的顶部添加了两个字段，并在构造函数之后添加了事件处理程序。

```
// Bindable properties
@Input() text: string;
@Input() disabled = false;

. . . 

onChanged($event) {
  this.isChecked = $event && $event.target && $event.target.checked;
} 
```

Enter fullscreen mode Exit fullscreen mode

这两个字段都用 Angular 的`@Input` decorator 进行了注释，这使得它们的值可以使用标准的 HTML 语法从父组件中指定。

```
<app-checkbox text="Remember me" disabled="false"></app-checkbox> 
```

Enter fullscreen mode Exit fullscreen mode

每当`<input>`改变时，就会调用`onChanged`函数(换句话说，复选框被选中或清除)。`$event`参数是标准的 [HTML DOM 事件对象](https://www.w3schools.com/jsref/dom_obj_event.asp)。在这个函数中，我根据复选框的实际状态，手动将`isChecked`字段设置为`true`或`false`。在检索`checked`属性之前，确保`$event`和`$event.target`都有真值，这是我过于多疑了。这可能有点偏执，但确实有效。

### 没有取值器

在这一点上，有一个自定义的复选框，可以放在任何其他组件中。它会工作，除了两件事:

1.  宿主容器将无法在运行时获得复选框的状态。
2.  不能使用`[(ngModel)]`属性。如果是这样，您将在运行时得到一个令人讨厌的错误，即组件上“没有值访问器”。

幸运的是，这两个问题有一个共同的解决方案。自定义组件需要实现 Angular 的`ControlValueAccessor`接口。

### ControlValueAccessor

如果您查找这个接口，您会发现它由四个函数组成，每个函数都需要在组件上实现。我们将逐一查看这些函数。

#### writeValue()

每当父组件或宿主组件的值发生变化时，Angular 都会调用此函数。您需要做的就是处理传入的值。在这里，我只是存储它，由于它的内部数据绑定，这顺便改变了复选框的状态。

```
writeValue(obj: boolean): void {
  this.isChecked = obj;
} 
```

Enter fullscreen mode Exit fullscreen mode

### 寄存器转换()

Angular 调用这个函数来提供一个回调函数，只要发生了变化，组件就需要调用这个函数。

```
onChange = (_) => {}; // No-op

registerOnChange(fn: any): void {
  // Replace internal function with the one provided
  this.onChange = fn;
} 
```

Enter fullscreen mode Exit fullscreen mode

我在这里遵循的模式是在组件上创建一个默认的`onChange`函数，它什么也不做。然后当 Angular 调用`registerOnChange`时，组件的 onChange 函数被替换为提供的回调函数。

然而，这只是故事的一半。定制组件需要调用它，这发生在绑定到复选框的`onChanged`函数中。对此，组件简单地调用内部的`onChange`函数，这个函数应该是 Angular 注册的回调。如果没有注册回调，则调用默认的无操作函数。这避免了对“is null guard”的需要

```
onChanged($event) {
  this.isChecked = $event && $event.target && $event.target.checked;

  // Call the parent's registered OnChange function, if any.
  // This lets it know the internal component value has changed.
  this.onChange(this.isChecked);
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 注册帐户()

这个稍微晦涩一点，对于 checkbox 控件来说好像没必要。根据[官方文件](https://angular.io/api/forms/ControlValueAccessor#registerOnTouched):

> 当在您自己的值访问器中实现 registerOnTouched 时，保存给定的函数，以便您的类在控件应该被认为模糊或“被触摸”时调用它。"

这不应与移动设备的“触摸”概念相混淆相反，它指的是被触摸或模糊的形式。在这个定制组件中，我连接了复选框的模糊事件。模式与`registerOnChange()`相同。

```
onBlur = (_) => {}; // No-op

registerOnTouched(fn: any): void {
  // Call the parent's registered onTouched function, if any.
  // This lets it know that the checkbox lost focus.
  this.onBlur = fn;
} 
```

Enter fullscreen mode Exit fullscreen mode

#### setDisabledState()

该函数执行您可能会想到的功能:当自定义组件应该被禁用时，它由 forms API 调用。在这种情况下，我将内部的`isDisabled`字段设置为提供的任何布尔值。然后，该值通过数据绑定反映在组件上，并相应地启用或禁用复选框。

```
setDisabledState?(isDisabled: boolean): void {
  this.disabled = isDisabled;
} 
```

Enter fullscreen mode Exit fullscreen mode

在复选框的情况下，一切都很简单。您还可以想象一个更复杂的控件，它可能包含多个输入元素，每个元素都可以被禁用。Angular Forms 将自定义组件视为单个控件。当它禁用该控件时，可能需要根据不同的规则禁用各个内部元素。具体如何在你自己的控制中处理，取决于你自己。

### 注册提供商

让一切都愉快地一起工作还有最后一件事。自定义组件内部的代码是完整的，但是 Angular 仍然对此一无所知。自从 Angular 发布以来，解决方案已经发生了变化，我发现了多个不再有效的解决方案。这个例子是在 Angular 8 上测试的。

注册定制组件的最后一步是让组件在其`@Component`装饰器的`providers`数组中提供`NG_VALUE_ACCESSOR`，如下所示:

```
providers: [{
  provide: NG_VALUE_ACCESSOR,
  useExisting: forwardRef(() => CheckboxComponent),
  multi: true
}] 
```

Enter fullscreen mode Exit fullscreen mode

该代码向 Angular 表明，组件使用现有组件提供了自己的`NG_VALUE_ACCESSOR`。这里的第 22 条军规是组件还没有被定义。Angular 通过使用`forwardRef`功能实现这一点。基本上，它告诉依赖注入系统所提供的组件还没有被定义，但是将在运行时可用。`forwardRef`函数的参数是一个粗箭头函数，它返回定制组件的类。

最后一个属性`multi: true`表示可能有多个`NG_VALUE_ACCESSOR`被提供给应用程序，可能来自多个地方。记住把它设置为`true`就足够了。

## 包装完毕

此时，自定义 checkbox 组件就可以放到应用程序中的任何其他页面或组件上了。因为它是在一个自定义模块中定义的，任何其他想要使用它的组件都需要将该模块导入到它自己的模块(或应用程序的模块)中。

如上所示，这是在另一个组件中使用这个定制组件的完整示例。

```
<app-checkbox 
  text="Remember Me" 
  [disabled]="!isLoggedIn" 
  [(ngModel)]="remember">
</app-checkbox> 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这需要一点努力，但结果是`[(ngModel)]`现在简单地“工作”，这是目标。

最后，因为 Angular 将组件识别为一个表单控件，所以如果您愿意，可以将它添加到一个反应式表单中，并使用自定义验证规则。

# 完整的组件

为了方便起见，下面是完整的组件:

```
import { Component, Input, forwardRef } from '@angular/core';
import { ControlValueAccessor, NG_VALUE_ACCESSOR } from '@angular/forms';

@Component({
  selector: 'app-checkbox',
  template: `
  <label class="cb-container">{{text}}
  <input type="checkbox" [checked]="isChecked" (change)="onChanged($event)" (blur)="onBlur($event)" [disabled]="disabled">
  <span class="checkmark"></span></label>`,
  styleUrls: ['./checkbox.component.scss'],
  providers: [{
    provide: NG_VALUE_ACCESSOR,
    useExisting: forwardRef(() => CheckboxComponent),
    multi: true
  }]
})
export class CheckboxComponent implements ControlValueAccessor {
  // Bindable properties
  @Input() text: string;
  @Input() disabled = false;

  // Internal properties
  isChecked = false;
  onChange = (_ => { });
  onBlur = (_ => { });

  writeValue(obj: boolean): void {
    this.isChecked = obj;
  }

  registerOnChange(fn: any): void {
    this.onChange = fn;
  }

  registerOnTouched(fn: any): void {
    this.onBlur = fn;
  }

  setDisabledState?(isDisabled: boolean): void {
    this.disabled = isDisabled;
  }

  onChanged($event) {
    this.isChecked = $event && $event.target && $event.target.checked;
    this.onChange(this.isChecked);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 了解更多信息

*   [控制值存取器的角度文件](https://angular.io/api/forms/ControlValueAccessor)
*   [W3School 自定义复选框](https://www.w3schools.com/howto/howto_css_custom_checkbox.asp)
*   [HTML DOM 事件对象](https://www.w3schools.com/jsref/dom_obj_event.asp)

# 反馈赞赏

你有什么意见或问题吗？我在这篇文章中犯了什么错误吗？在推特上告诉我。我是 [@walkingriver](https://twitter.com/walkingriver) 。