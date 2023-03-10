# NGConf 2019 -不是每个应用都是 SPA

> 原文：<https://dev.to/angular/ngconf-2019-not-every-app-is-a-spa-e84>

## 免责声明

这是我个人对 ngconf 会议的总结。虽然我用自己的话总结这些东西，但所使用的材料，如图像、图表、源代码示例都不是我自己的。其中大部分来自 Youtube 视频或各场会议发言人的幻灯片。

## 其他时段？

这篇文章是从[我的博客](https://juristr.com/blog)交叉发布的。如果你想阅读原文，也包括其他会议，前往[原文](https://juristr.com/blog/2019/05/Angular-8-and-the-Future-NGConf-2019-Roundup/?utm_source=devto&utm_medium=crosspost)。

在 twitter 上关注我。

## 不是每个 App 都是 SPA

*罗布·沃马尔德*

Rob 的目标是 Igor 提到的关于当前正在采用的领域 Angular apps 的图表。

[![](img/5de2e2a05a96d54105ab5c5c7c7f1be6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dAw9uMNA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/rob-graph-angular-adoption.png)

展望未来，团队的目标是瞄准该图中缺失的两条边。

### 中小型应用、演示、Edu 应用

[![](img/68801acb553f9036c3acf990d85d0eb6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jtfgs-bW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/rob-graph-small-apps.png)

针对图的左侧，也就是中小型应用程序所在的地方，答案肯定是角元素。

[![](img/ecd2ac5a6f2cdaf2f9d96fff140b0c9d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LmQEow2c--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/rob-angular-elements.png)

如果这对你来说听起来很新鲜，[看看我的相关文章](https://juristr.com/blog/2019/04/intro-to-angular-elements/)。

混合环境也是一个很好的例子，其中角元素非常适合:

*   许多不同的框架
*   不是每个人都可以从绿地开始
*   谷歌也有这个问题(Angular，AngularJS，Dart，GWT，Polymer，...)
*   在第三方网站上运行的迷你应用程序
*   NgUpgrade

在角度元素的上下文中，将单个组件捆绑为角度元素的注册过程当前为(< Angular v7) still quite verbose:

```
@NgModule({
  imports: [BrowserModule, CommonModule],
  declarations: [HelloWorld],
  entryComponents: [HelloWorld]
})
class HelloWorldModule {} 
```

Enter fullscreen mode Exit fullscreen mode

然后需要注册为角元素:

```
platformBrowser()
  .bootstrapModule(HelloWorldModule)
  .then(({injector}) => {
    const HelloWorldElement = createCustomElement(HelloWorld, {injector});
    customElements.define('hello-world', HelloWorldElement);
  }); 
```

Enter fullscreen mode Exit fullscreen mode

艾薇会有什么改变吗？

[![](img/1c8d915574b0a58ebc88c68a1143cbc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--T0tYxLlw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/rob-elements-with-ivy.png)

在 Ivy 中呈现组件的最简单方式如下:

```
import { Component, Input, Output, renderComponent } from '@angular/core';

@Component({
  selector: 'hello-world',
  template: `...`
})
class HelloWorld {
  @Input() name: string;
  @Output() nameChange = new EventEmitter();
  changeName = () => this.nameChange.emit(this.name);
}

renderComponent(HelloWorld); 
```

Enter fullscreen mode Exit fullscreen mode

那么如何才能让这成为常春藤中的棱角元素呢？Rob 在舞台上展示了它的样子。

```
import { renderComponent } from '@angular/core';
import { HelloWorld } from './hello-world.component';

// manually define the host rather than let Angular look for it
// then pass it as a 2nd argument to the renderComponent
const host = document.querySelector('hello-world');

renderComponent(HelloWorld, { host });

// create a custom element using the native browser API
class HelloWorldElement extends HTMLElement {} 
```

Enter fullscreen mode Exit fullscreen mode

这是第一步。接下来，我们可以使用本地浏览器 API 创建一个定制元素，并从那里调用`renderComponent`。

```
import { renderComponent } from '@angular/core';
import { HelloWorld } from './hello-world.component';

// create a custom element using the native browser API
class HelloWorldElement extends HTMLElement {
  component: HelloWorld;
  constructor()  {
    super();
    // associate "this" as the host element
    this.component = renderComponent(HelloWorld, { host: this })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

注意我们是如何传递`this`(这是自定义元素实例，作为渲染函数的宿主)。我们也可以添加简单包装的属性。

```
import { renderComponent, detectChanges } from '@angular/core';
import { HelloWorld } from './hello-world.component';

// create a custom element using the native browser API
class HelloWorldElement extends HTMLElement {
  component: HelloWorld;
  constructor()  {
    super();
    // associate "this" as the host element
    this.component = renderComponent(HelloWorld, { host: this })
  }

  set name(value) {
    this.component.name = value;
    detectChangs(this.component);
  }
  get name() {
    return this.component.name;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`detectChanges`可以直接从 Angular 导入。这只是一个函数💪(不一定需要 DI 来注入`ChangeDetectorRef`等..)!

要拥有属性，我们只需继续使用原生浏览器 API。

```
import { renderComponent, detectChanges } from '@angular/core';
import { HelloWorld } from './hello-world.component';

// create a custom element using the native browser API
class HelloWorldElement extends HTMLElement {
  static observedAttributes = ['name'];
  component: HelloWorld;
  constructor()  {
    super();
    // associate "this" as the host element
    this.component = renderComponent(HelloWorld, { host: this })
  }

  attributeChangedCallback(attr, oldValue, newValue) {
    this.name = newValue;
  }

  set name(value) {...}
  get name() {...}
} 
```

Enter fullscreen mode Exit fullscreen mode

这只是为了展示用 Ivy 自己构建它是多么容易。你不必每次都这样。最有可能的是，Ivy 的情况是这样的:

```
import { withNgComponent } from '@angular/elements';
import { HelloWorld } from './hello-world.component';

// create a Custom Element that wraps the Angular Component
const HelloWorldElement = withNgComponent(HelloWorld);

// register it
customElements.define('hello-world', HelloWorldElement); 
```

Enter fullscreen mode Exit fullscreen mode

没有平台，没有模块🎉 🎉当然，如果您愿意，您仍然可以使用注射器:

```
...
// create a Custom Element that wraps the Angular Component
const HelloWorldElement = withNgComponent(HelloWorld, {injector});
... 
```

Enter fullscreen mode Exit fullscreen mode

在许多情况下，您已经有了一个想要转换为元素的角度组件。**但是如果你不想要一个角分量，而只是一个角元素呢？**🤔基本上你只是想要有角度的模板系统给你的好处。现在的“问题”是我们有了`NgModule`，它告诉编译器需要哪些依赖项，并帮助它优化最终结果。从技术上来说，Ivy 不需要一个`NgModule`，但是我们仍然需要有一种方法来告诉组件它的模板中还有哪些指令/组件。一个**提议** ( < <这是团队希望得到反馈的早期提议)是允许在`@Component`标签中直接注册依赖项，就像你已经可以用`providers`和已经在`Angular RC4`中的一样(是的，我记得😅).大概是这样:

```
@Component({
  selector: 'hello-world',
  template: `...`,
  providers: [SomeService],
  deps: [SomeDirective, SomePipe]
})
class HelloWorld {} 
```

Enter fullscreen mode Exit fullscreen mode

这肯定更冗长，但如果你愿意，也更直接和“更简单”。为了实现只有一个 ng 元素(没有角分量)的最终目标，可以看起来像这样(基于我们之前讨论的内容):

```
import { NgElement, withElement } from '@angular/elements';
...
@NgElement({
  selector: 'hello-world',
  template: `...`,
  providers: [SomeService],
  deps: [SomeDirective, SomePipe]
})
class HelloWorld extends withNgElement {} 
```

Enter fullscreen mode Exit fullscreen mode

这给了你**一个没有角分量**的角元素。在某些情况下，比如构建设计系统时，这可能是有意义的。

### 按比例放大——或者什么是“角光子”项目？

图表的另一边:扩大规模。

[![](img/5d23a879d9a82f92899fe08b173864d6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HqLByS9L--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/rob-graph-angular-scaleup.png)

在这种背景下(在主题演讲期间-请参阅进一步的内容)，出现了* *角光子这个名称。重要的:

[![](img/8c02aca39b09cf63e00840a99b7a88ad.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0VAd5uif--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/rob-photon.png)

这是一个实验性的研究项目，旨在“决定如何为下一代 Angular 开发人员构建合适的工具”。这是一个合作项目

*   谷歌购物快递(用 Angular 构建)
*   Wiz

[![](img/e70f7c4089c560b9e4e0be035a9f8466.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jc2zc9SF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/rob-collaboration.png)

[![](img/45a48dee96bb50e2366104176a6c79e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dlnBM2Lu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://juristr.com/blog/assets/imgs/ngconf2019/rob-what-wanted.png)

按需加载组件是一个很大的部分。先睹为快，这可能是它的样子

```
import { withLazyNgComponent } from '@angular/elements';

// create a Custom Element that wraps the Angular Component
const HelloWorldElement = withLazyNgComponent(() => import('./hellow-world.component'));

// register it
customElements.define('hello-world', HelloWorldElement); 
```

Enter fullscreen mode Exit fullscreen mode

注意`withLazyNgComponent`,它只在真正需要的时候获取必要的脚本。