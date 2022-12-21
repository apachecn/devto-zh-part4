# 了解 Angular 的控制值访问器接口

> 原文：<https://dev.to/bitovi/understanding-angular-s-control-value-accessor-interface-5e7k>

如果您经常在 Angular 中处理表单，那么您可以学习的最强大的东西之一就是如何使用控件值访问器接口。CVA 接口是窗体控件和它们在 DOM 中的元素之间的桥梁。扩展 CVA 接口的组件可以创建自定义窗体控件，其行为与常规输入或单选按钮相同。

## 为什么要使用控制值访问器接口？

有时，您可能需要创建一个自定义的 form 元素，以便能够用作常规的 FormControl。(为了更好地理解 FormControls 和其他 Angular Form 类，您可能想阅读我的文章[这里](https://dev.to/angular/managing-nested-and-dynamic-forms-in-angular-1he6))例如，创建一个更新单个值的五星评级 UI。我们将在演示中使用这个例子。

[![star rating input](img/acc5e75f4cbb49f3c0f81b2c28dcc322.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1NApGLsr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u7nuzhlku5y7k4mwtsfn.gif)

这里的用户界面上发生了很多事情——当鼠标悬停在星星上时，星星会改变颜色，并为每个评级显示不同的文本，但我们所关心的是保存一个数值 0-5。

## 实施 CVA

要在组件中使用 CVA 接口，您必须实现它的三个必需方法:`writeValue`、`registerOnChange`和`registerOnTouched`。还有一个可选方法`setDisabledState`。

在两种情况下调用`writeValue`方法:

*   当 formControl 实例化时

```
rating = new FormControl({value: null, disabled: false}) 
```

*   当 formControl 值更改时

```
rating.patchValue(3) 
```

每当值改变时，就应该调用`registerOnChange`方法——在我们的例子中，当点击一个星号时。

每当我们的 UI 被交互时，比如一个模糊事件，就应该调用`registerOnTouched`方法。您可能熟悉从像 Bootstrap 或 NGX-Bootstrap 这样具有`onBlur`方法的库中实现 Typeaheads。

在两种情况下调用`setDisabledState`方法:

*   当用禁用的属性实例化 formControl 时

```
rating = new FormControl({value: null, disabled: false}) 
```

*   当表单控件禁用状态改变时

```
rating.disable();
rating.enable(); 
```

实现 CVA 的星级评定组件可能如下所示:

```
export class StarRaterComponent implements ControlValueAccessor {
  public ratings = [
    {
      stars: 1,
      text: 'must GTFO ASAP'
    },
    {
      stars: 2,
      text: 'meh'
    },
    {
      stars: 3,
      text: 'it\'s ok'
    },
    {
      stars: 4,
      text: 'I\'d be sad if a black hole ate it'
    },
    {
      stars: 5,
      text: '10/10 would write review on Amazon'
    }
  ]
  public disabled: boolean;
  public ratingText: string;
  public _value: number;

  onChanged: any = () => {}
  onTouched: any = () => {}

  writeValue(val) {
    this._value = val;
  }

  registerOnChange(fn: any){
    this.onChanged = fn
  }
  registerOnTouched(fn: any){
    this.onTouched = fn
  }

  setDisabledState(isDisabled: boolean): void {
    this.disabled = isDisabled;
  }

  setRating(star: any) {
    if(!this.disabled) {
      this._value = star.stars;
      this.ratingText = star.text
      this.onChanged(star.stars);
      this.onTouched();
    }
  }

} 
```

还必须使用 NG_VALUE_ACCESSOR 和 forwardRef 告诉 Angular，实现 CVA 的组件是一个值访问器(记住，接口不是在 TypeScript 中编译的)。

```
import { Component, forwardRef } from '@angular/core';
import { ControlValueAccessor, NG_VALUE_ACCESSOR } from '@angular/forms';

@Component({
  selector: 'gr-star-rater',
  templateUrl: './star-rater.component.html',
  styleUrls: ['./star-rater.component.less'],
  providers: [     
    {
      provide: NG_VALUE_ACCESSOR, 
      useExisting: forwardRef(() => StarRaterComponent),
      multi: true     
    }   
  ]
})
export class StarRaterComponent implements ControlValueAccessor {
... 
```

## 使用您的新 CVA 组件

现在，为了使用您的奇特的新 CVA 组件，您可以将它视为普通的旧 FormControl。

```
this.galaxyForm = new FormGroup({
  rating: new FormControl({value: null, disabled: true})
}); 
```

```
<form [formGroup]="galaxyForm" (ngSubmit)="onSubmit()">
  <h1>Galaxy Rating App</h1>
  <div class="form-group">
    <label>
      Rating:
      <gr-star-rater formControlName="rating"></gr-star-rater>
    </label>
  </div>
  <div class="form-group">
    <button type="submit">Submit</button>
  </div>
</form> 
```

Tada！没那么可怕吧。角反应形式的问题或需要帮助？让我知道！