# 如何在 Angular 8 中使用表单生成器来验证反应式表单

> 原文：<https://dev.to/bnevilleoneill/how-to-use-form-builders-in-angular-8-to-validate-reactive-forms-239m>

**由[nwo se lotana](https://blog.logrocket.com/author/nwoselotanna/)撰写**

之前，我们讨论了如何在 Angular 8 中用表单组管理反应式表单控件。这是一个关于在 Angular 8 中建立反应形态的新帖子。这里是我们在上一篇文章中讨论的概念的简要回顾。

## 窗体控件和窗体组成角度

表单控件基本上是可以保存任何表单元素的数据值和验证信息的类，这意味着您在反应式表单中的每个表单输入都应该被表单控件绑定。它们是组成反应形式的基本单位。

表单组是基本上包装表单控件集合的结构。正如控件使您可以访问元素的状态一样，组也提供了相同的访问权限，但访问的是包装控件的状态。初始化时，表单组中的每个表单控件都由名称标识。

## 生成表单控件

设置表单控件，尤其是对于非常长的表单，很快就会变得既单调又紧张。Angular 提供了一个助手服务来解决这个问题，让你可以一直遵守避免重复的干巴巴的概念。这项服务称为表单生成器服务。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 开始之前…

为了能够完成本文的演示，您应该具备:

*   计算机上安装的节点版本 11.0
*   节点程序包管理器版本 6.7(通常随节点安装一起提供)
*   角度控制器版本 8.0
*   Angular 的最新版本(版本 8)

```
// run the command in a terminal
ng version 
```

Enter fullscreen mode Exit fullscreen mode

确认您使用的是版本 8，如果不是，请更新到 8。

*   点击这里下载本教程的起始项目[来完成演示。](https://github.com/viclotana/ng-group)
*   使用以下命令解压缩项目并初始化终端中的节点模块:

```
npm install 
```

Enter fullscreen mode Exit fullscreen mode

其他值得拥有的东西有:

*   初级水平的角度框架的工作知识
*   熟悉 Angular 中的表单控件将是一个优势，但不是一个要求

## 演示

在本教程中，您将经历一个使用 form builder 构建反应式表单的代码之旅。如果你从一开始就关注了这篇文章，你将会下载并在 VS 代码中打开[启动项目](https://github.com/viclotana/ng-group)。如果你打开`employee.component.ts`，文件应该是这样的:

```
import { Component, OnInit } from '@angular/core';
import { FormControl, FormGroup } from '@angular/forms'
@Component({
  selector: 'app-employee',
  templateUrl: './employee.component.html',
  styleUrls: ['./employee.component.css']
})
export class EmployeeComponent implements OnInit {
  bioSection = new FormGroup({
    firstName: new FormControl(''),
    lastName: new FormControl(''),
    age: new FormControl(''),
    stackDetails: new FormGroup({
      stack: new FormControl(''),
      experience: new FormControl('')
    }),
    address: new FormGroup({
        country: new FormControl(''),
        city: new FormControl('')
    })
  });
constructor() { }
ngOnInit() {
  }
  callingFunction() {
    console.log(this.bioSection.value);
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以看到每一个表单控件——甚至是划分它的表单组——都是拼写出来的，所以随着时间的推移，您作为开发人员会不断重复自己的工作。表单生成器有助于解决这个效率问题。要使用表单生成器，您必须首先注册它。

### 注册表单生成器

要在组件中注册表单生成器，首先要做的是从角度表单导入:

```
import { FormBuilder } from ‘@angular/forms’; 
```

Enter fullscreen mode Exit fullscreen mode

下一步是注入 form builder 服务，这是一个可注入的提供者，它带有反应式表单模块。注入表单生成器后，您就可以使用它了。导航到`employee.component.ts`文件并复制到下面的代码块:

```
import { Component, OnInit } from '@angular/core';
import { FormBuilder } from '@angular/forms'
@Component({
  selector: 'app-employee',
  templateUrl: './employee.component.html',
  styleUrls: ['./employee.component.css']
})
export class EmployeeComponent implements OnInit {
  bioSection = this.fb.group({
    firstName: [''],
    lastName: [''],
    age: [''],
    stackDetails: this.fb.group({
      stack: [''],
      experience: ['']
    }),
    address: this.fb.group({
        country: [''],
        city: ['']
    })
  });
constructor(private fb: FormBuilder) { }
ngOnInit() {
  }
  callingFunction() {
    console.log(this.bioSection.value);
   }
} 
```

Enter fullscreen mode Exit fullscreen mode

这与您在开始时看到的前一个代码块做了完全相同的事情，但是您可以看到代码更少，结构更多——因此，资源得到了最佳利用。表单生成器不仅有助于提高反应式表单的代码效率，而且对表单验证也很重要。

### 表单验证

使用 Angular 中的反应式表单，您可以在表单生成器中验证表单。使用命令运行开发中的应用程序:

```
ng serve 
```

Enter fullscreen mode Exit fullscreen mode

您会发现，即使您没有在文本框中输入值，表单也会提交。这可以很容易地用反应式表单中的表单验证器来检查。第一件要做的事，就像反应形式的所有元素一样，是从角度形式中导入它。

```
import { Validators } from '@angular/forms'; 
```

Enter fullscreen mode Exit fullscreen mode

现在，您可以通过指定表单控件来试验验证器，为了激活提交按钮，必须填写表单控件。将下面的代码块复制到`employee.component.ts`文件中:

最后要做的是确保提交按钮的活动设置得到相应的设置。导航到`employee.component.html`文件，并确保提交语句如下所示:

```
<button type=”submit” [disabled]=”!bioSection.valid”>Submit Application</button> 
```

Enter fullscreen mode Exit fullscreen mode

如果您现在运行您的应用程序，您会看到如果您不为名字设置输入，您就不能提交表单——这不是很酷吗？你可以从这里的官方指南中获得更多很酷的表单验证技巧。

### 显示输入值和状态

您应该知道的最后一件事是如何使用 value 和 status 属性来实时显示您的反应式表单的输入值，以及它是否可以被提交。

反应式表单 API 允许您在模板部分的表单组或表单控件上使用值和状态属性。打开您的`employee.component.html`文件，复制下面的代码块:

```
<form [formGroup]="bioSection" (ngSubmit)="callingFunction()">
    <h3>Bio Details
</h3>

  <label>
    First Name:
    <input type="text" formControlName="firstName">
  </label> <br>
<label>
    Last Name:
    <input type="text" formControlName="lastName">
  </label> <br>
<label>
    Age:
    <input type="text" formControlName="age">
  </label>
<div formGroupName="stackDetails">
    <h3>Stack Details</h3>

    <label>
      Stack:
      <input type="text" formControlName="stack">
    </label> <br>

    <label>
      Experience:
      <input type="text" formControlName="experience">
    </label>
  </div>
<div formGroupName="address">
    <h3>Address</h3>

    <label>
      Country:
      <input type="text" formControlName="country">
    </label> <br>

    <label>
      City:
      <input type="text" formControlName="city">
    </label>
  </div>
<button type="submit" [disabled]="!bioSection.valid">Submit Application</button>
  <p>
    Real-time data: {{ bioSection.value | json }}
  </p>
  <p>
    Your form status is : {{ bioSection.status }}
  </p>
</form> 
```

Enter fullscreen mode Exit fullscreen mode

这将在您使用表单时，在界面中为您显示提交的值和状态。本教程的完整代码可以在 GitHub 上找到。

## 结论

本文概述了表单生成器，以及它是如何提高表单控件和表单组的效率的。它还展示了用反应式表单轻松处理表单验证是多么重要。黑客快乐！

* * *

**编者按:**看到这个帖子有问题？你可以在这里找到正确的版本。

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6FG5kvEL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i2.wp.com/blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png%3Fresize%3D1200%252C677%26ssl%3D1)

[log rocket](https://logrocket.com/signup/)是一个前端日志工具，让你重放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 动作和状态，LogRocket 还记录控制台日志、JavaScript 错误、stacktraces、带有头+体的网络请求/响应、浏览器元数据、自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

帖子[如何在 Angular 8 中使用表单生成器来验证反应式表单](https://blog.logrocket.com/form-builders-angular-8-validate-reactive-forms/)最先出现在[日志博客](https://blog.logrocket.com)上。