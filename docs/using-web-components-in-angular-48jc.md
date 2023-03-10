# 在 Angular 中使用 Web 组件

> 原文：<https://dev.to/coryrylan/using-web-components-in-angular-48jc>

这篇文章是从我的新电子书 [Web Component Essentials](https://webcomponent.dev) 中摘录的章节

[Angular](https://angular.io) 从一开始就被设计用于 Web 组件。Angular 不仅可以使用 Web 组件，还可以通过 Angular Elements API 将 Angular 组件发布为 Web 组件。对于我们的示例，我们将展示如何将基本的下拉组件安装到 Angular CLI 项目中。

[![Web Component Working in Angular](img/b303b6d8a5bb05510eecd65847f96a20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZLlWlFCM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jyvmjxkokodrjn697mkj.png)

首先，我们将使用 Angular CLI 创建一个 Angular 项目。我们需要通过运行以下命令来安装 Angular CLI:

```
npm install -g @angular/cli 
```

这个命令将把 Angular CLI 工具安装到我们的终端/命令行。安装完成后，我们可以运行以下命令来创建 CLI 项目。

```
ng new my-app 
```

这个命令将创建一个 CLI 项目并安装所有必需的 NPM
包。一旦在我们的 CLI 项目中完成，我们可以运行:

```
ng serve 
```

`ng serve`命令将在
T1 本地运行我们的角度应用程序。现在我们已经启动并运行了 Angular 项目，我们需要安装下拉组件。在我们的 Angular 项目中，我们现在可以通过运行:
来安装测试 web 组件

```
npm install web-component-essentials 
```

这个命令将把我们的组件安装到我们的 Angular 项目中，并将在`package.json`中添加一个条目。一旦安装到我们的`app.module.ts`中，我们就可以导入组件了。

```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule, CUSTOM_ELEMENTS_SCHEMA } from '@angular/core';
import 'web-component-essentials';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent],
  schemas: [
    CUSTOM_ELEMENTS_SCHEMA // Tells Angular we will have custom tags in our templates
  ]
})
export class AppModule { } 
```

一旦导入，我们需要将`@angular/core`中的`CUSTOM_ELEMENTS_SCHEMA`添加到应用程序模块中。`CUSTOM_ELEMENTS_SCHEMA`告诉 Angular 我们将使用自定义元素，这些元素在我们的应用程序中不是注册的角度组件。

现在我们的组件已经安装好了，我们可以在角度应用中使用它了。我们来看看`app.component.ts`文件。

```
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  myTitle = 'project-angular';
  open = false;

  toggle(event) {
    console.log(event);
    this.open = event.detail;
  }
} 
```

App 组件是我们 Angular 应用程序的根组件。在我们的组件上，我们将有两个属性。将传递给下拉列表的`myTitle`和用于跟踪下拉列表是打开还是关闭的`open`属性。

App 组件也有一个单独的方法`toggle()`，每当
下拉菜单打开或关闭时都会被调用。接下来，我们来看一下`app.component.html`模板。

```
<h1>Angular Application using Web Components</h1>

<p>
  {{open ? 'open' : 'closed'}}
</p>

<x-dropdown [title]="myTitle" (show)="toggle($event)">
  Hello from Web Component in Angular!
</x-dropdown> 
```

在我们的模板中，我们有一个角度表达式，它根据`open`属性的值显示消息`open`或`closed`。Angular 有两种不同的语法用于绑定到属性和事件。该语法不仅适用于角度组件，也适用于 Web 组件。

第一个绑定是属性绑定语法。这个语法使用
方括号`[title]="myTitle"`来告诉 Angular 应该在组件上设置什么属性。在我们的例子中，我们采用了`myTitle`属性值，并设置了下拉组件的`[title]`属性。

第二个绑定语法是事件语法。使用这种语法，Angular 组件可以监听 DOM 事件以及 Angular 和 Web 组件事件。为了绑定到一个事件，我们使用括号`(show)="toggle($event)"`。在括号中，我们传递我们想要收听的事件的名称。在绑定的右边，我们传递一个我们希望在事件发生时执行的方法。如果我们想将事件值传递给方法，我们使用`$event`关键字来
告诉 Angular 将事件值传递给`log`方法。

所有东西都连接起来后，我们应该会看到类似如下的输出:

[![Web Component Working in Angular](img/b303b6d8a5bb05510eecd65847f96a20.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZLlWlFCM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jyvmjxkokodrjn697mkj.png)

Angular 对于客户端应用程序来说是一个极好的选择，因为它有一个健壮的 API，可以很好地用于大型企业应用程序，同时还添加了出色的 Web 组件支持。点击这里查看完整的[工作示例](https://stackblitz.com/edit/angular-b3kbq3)！