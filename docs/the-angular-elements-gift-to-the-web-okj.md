# 给网络的棱角(元素)礼物

> 原文：<https://dev.to/bergermarko/the-angular-elements-gift-to-the-web-okj>

## 角元素介绍

想象一下，你的客户想在第三方网络应用程序上销售她或他的服务。然后，您必须以某种方式将您的服务集成到他们的应用程序中，或者重定向到您的应用程序。这不是一件容易的工作，用户体验很糟糕。那么角元素是如何帮助我们的呢？你听说过 Web 组件技术吗？

> Web 组件是一组 web 平台 API，允许您创建新的自定义的、可重用的、封装的 HTML 标记，以便在网页和 web 应用程序中使用。

这项技术是亚历克斯·罗素在 2011 年 Fronteers 会议上介绍的。现在 Angular 找到了将这项技术引入自己环境的方法。
让我们看看文档是怎么说的。

> Angular 元素是打包成定制元素的 Angular 组件，这是一种以与框架无关的方式定义新 HTML 元素的 web 标准。

酷吧！

说够了，让我们编码。
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
ng g c register-form 
```

### 注册-表单.组件. ts

```
@Component({
  selector: 'register-peeker',
  templateUrl: './register-peeker.component.html',
  styleUrls: ['./register-peeker.component.css'],
  encapsulation: ViewEncapsulation.Native
}) 
```

### 这到底是怎么回事？

我们希望有独立的应用程序内的其他应用程序与我们自己的 CSS 风格。看到问题了。来解决这个问题。我们必须告诉 Angular 使用 Shadow Dom(代码的封装部分)来隔离我们的 CSS 样式，并将其编译成 JavaScript。这样我们就可以把它打包成一个文件。还有其他视图封装策略，但 Native 是首选。现在你可以在 component.ts、component.ts 和 component.css 组件文件中做我们都喜欢的漂亮的棱角魔术了。
但是如果你想使用与“父”页面相同的 CSS 样式。只需将封装切换到 None 并删除 styleUrls。
让我们快进到真正的奇迹发生的地方