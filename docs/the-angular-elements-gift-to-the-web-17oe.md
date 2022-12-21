# 给网络的棱角(元素)礼物

> 原文：<https://dev.to/bergermarko/the-angular-elements-gift-to-the-web-17oe>

## 角元素介绍

想象一下，你的客户想在第三方网络应用程序上销售她或他的服务。然后，您必须以某种方式将您的服务集成到他们的应用程序中，或者重定向到您的应用程序。这不是一件容易的工作，用户体验很糟糕。那么角元素是如何帮助我们的呢？你听说过 Web 组件技术吗？

> Web 组件是一组 web 平台 API，允许您创建新的自定义的、可重用的、封装的 HTML 标记，以便在网页和 web 应用程序中使用。

这项技术是亚历克斯·罗素在 2011 年 Fronteers 会议上介绍的。现在 Angular 找到了将这项技术引入自己环境的方法。
让我们看看文档是怎么说的。

> Angular 元素是打包成定制元素的 Angular 组件，这是一种以与框架无关的方式定义新 HTML 元素的 web 标准。

酷吧！

## 说够了就让码。

我们将创建元素注册表

```
npm install -g @angular/cli 
ng new registration-form
ng add @angular/elements project=registration-form 
```

@angular/elements 是一个包，包含了我们创建角度元素所需的所有好东西。它包含 document-register-element，这是 W3C 定制元素规范的轻量级版本。记住**棱角分明的元素**还年轻。所以还是有一些问题。其中之一就是你需要把 document-register-element 版本从 **1.7.2 改成 1.8.1** (在 package.json 里改，做 npm 安装)。

所有的准备工作都完成了。让我们把手弄脏吧。

首先，生成新组件。

```
ng g c register 
```

### [T1】registration . component . ts](#registrationcomponentts)

```
@Component({
  selector: 'register',
  templateUrl: './register.component.html',
  styleUrls: ['./register.component.css'],
  encapsulation: ViewEncapsulation.Native
}) 
```

### 这到底是怎么回事？

我们希望有独立的应用程序内的其他应用程序与我们自己的 CSS 风格。看到问题了。来解决这个问题。我们必须告诉 Angular 使用 Shadow Dom(代码的封装部分)来隔离我们的 CSS 样式，并将其编译成 JavaScript。这样我们就可以把它打包成一个文件。还有其他视图封装策略，但 Native 是首选。现在你可以在 component.ts、component.ts 和 component.css 组件文件中做我们都喜欢的漂亮的棱角魔术了。
但是如果你想使用与“父”页面相同的 CSS 样式。只需将封装切换到 None 并删除 styleUrls。
我将跳过开发注册表的部分，快进到真正的神奇发生的地方

```
@NgModule({
  declarations: [
    RegisterComponent
  ],
  imports: [
    BrowserModule,
    FormsModule,
    HttpClientModule
  ],
  entryComponents: [
    RegisterComponent
  ],
  providers: [],
  bootstrap: []
})
export class AppModule {
  constructor(private injector: Injector) {

  }
  ngDoBootstrap() {
    const el = createCustomElement(RegisterComponent, {injector: this.injector});
    customElements.define('register', el);
  }
 } 
```

首先，我们需要将 RegisterFormComponent 添加到 entryComponents 中。因为我们的组件已经定义了，但没有在应用程序中声明。第二个任务是用 ngDoBootstrap 实现手动引导。第三个也是最后一个任务是用 createCustomElement()将 Angular 组件转换为原生 DOM API。

### 就是这样。

我们唯一需要的是编译、捆绑和测试我们的定制元素。
删除所有 app.componet 文件。我们不需要他们。
*注意:不要删除 app.module.ts.*

## 我们还没有完成

现在，这是我们都希望在 Angular-CLI 的下一个版本中更新的东西，您将会看到为什么。

如果你执行 **ng build - prod** 操作，它将创建一堆文件名中包含散列字符串的文件。我们不想这样。

### 所以我们需要做一点黑客攻击。

```
npm install fs-extra concat --save-dev 
```

安装完成后，转到项目根文件夹并创建一个 **build-script.js** 文件。

```
const fs = require('fs-extra');
const concat = require('concat');

(async function build() {
  const files = [
    './dist/register/runtime.js',
    './dist/register/polyfills.js',
    './dist/register/scripts.js',
    './dist/register/main.js'
  ];

  await fs.ensureDir('elements');
  await concat(files, 'elements/register-form.js');
})(); 
```

*注意:这个脚本是针对 Angular 7 的。如果您使用角度 6，您需要在距离/文件夹中调整路径。*
这个脚本会把所有这些文件捆绑成一个 register-form.js 文件供我们使用，并放在 elements 目录下。

下一步，让我们将新的构建脚本添加到 package.json 中。

```
"scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "build:elements": "ng build --prod --output-hashing none && node build-elements.js",
    "test": "ng test",
    "lint": "ng lint",
    "e2e": "ng e2e"
  }, 
```

### 运行脚本

```
ng build:elements 
```

### 测试一下

在根目录下创建基本的 index.html 文件，然后添加。

```
ng serve 
```

还有维奥拉。