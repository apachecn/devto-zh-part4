# 使用 ng-content 进行内容投影

> 原文：<https://dev.to/deerawan/using-ng-content-for-content-projection-56lg>

内容投影是 Angular 中修改组件内容的有用技术。让我们从一个非常简单的场景来学习它。

你有一个使用了`<button>`的按钮组件`button.component.ts`，目前，那里使用的文本是`submit`

`button.component.ts`

```
 import { Component } from '@angular/core';

@Component({
  selector: 'app-button',
  template: `
    <button class="primary-button">
      submit
    </button>
  `,
  styles: [
    '.primary-button { background: #94FEB1; padding: 10px; }'
  ]
})
export class AppButtonComponent  {  
} 
```

然后我们在`app component`的 HTML 模板中两次使用那个组件。

`app.component.html`

```
<app-button></app-button>
<app-button></app-button> 
```

[![Alt Text](img/3b10b2ec974a06e57ccf2a2a60b7d5db.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dhKZlMYV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gpl8hce31y7gzizqmdnb.png)

它肯定会显示两个带有`submit`文本的按钮。

我们如何定制该组件的文本呢？假设我想为第一个按钮显示`login`文本，为第二个按钮显示`register`。

一种方法是使用`@Input`,这样文本可以作为属性传递

`app.component.html`

```
<app-button [text]="login"></app-button>
<app-button [text]="register"></app-button> 
```

这还不错，但是我们可以使用`ng-content`尝试另一种方法。为了使用它，我们将`<ng-content></ng-content>`替换为`submit`文本。

`button.component.ts`

```
<button class="primary-button">
  <ng-content></ng-content>
</button> 
```

然后在 HTML 模板中

`app.component.html`

```
<app-button>login</app-button>
<app-button>register</app-button> 
```

哇，这看起来很熟悉，像是 HTML 标签。看到`<app-button>`的内部内容被投射到我们在按钮组件的 HTML 模板中定义的`<ng-content></ng-content>`中。

[![Alt Text](img/9cd0ce997215a33e2e60ad6b778eacb4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--axk6OjzT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jk4okw4m53uwhctka4rr.png)

这是一个单一的内容投影。有趣的是，我们也可以使用`ng-content`进行多内容投影。

# 多内容投影

假设我们有一个名为 **Banner** 的新组件。看起来是这样的

```
import { Component } from '@angular/core';

@Component({
  selector: 'app-banner',
  template: `
    <div class="banner">
      <div class="banner-title">
        title
      </div>
      <div class="banner-description">
        description        
      </div>
    </div>
  `,
  styles: [
    '.banner { background: #C1FDFE; padding: 10px; border: 1px solid #333 }',
    '.banner-title { font-weight: bold; font-size: 20px; color: #5D83FE }',    
  ]
})
export class AppBannerComponent  {  
} 
```

`app.component.html`

```
<app-banner></app-banner> 
```

这是它显示的时间

[![Alt Text](img/2e5377d56b70e43707da6dfbef5723a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tZx2REA_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/tbh31xa1azjmb11p1jo8.png)

注意，我们为`title`选择了蓝色，为`description`选择了黑色。我们正在尝试使用`ng-content`来修改横幅组件中的`title`和`description`。

你一定觉得像下面这样加进去应该很容易解决

`banner.component.ts`

```
<div class="banner">
  <div class="banner-title">
    <ng-content></ng-content>
  </div>

  <div class="banner-description">
    <ng-content></ng-content>
  </div>
</div> 
```

然后

`app.component.html`

```
<app-banner> 
  <h1>Black Friday Discount</h1>
  <p>50% discount for specific products</p>
</app-banner> 
```

这是怎么回事

[![Alt Text](img/f74ff3329c5c93e86605f6ebe44201bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aG91JoID--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vmxcqlol2u25ubxr8l5z.png)

你能发现什么不对吗？是的，我们的横幅标题没有显示为蓝色。显然，如果我们在模板中指定了多个`ng-content`，Angular 将只使用最新定义的一个。在我们的例子中，它只会取代`banner-description`中的`ng-content`。我们应该把标题和描述投射到正确的地方。

为了解决这个问题，`ng-content`有一个名为`select`的属性。如果用 CSS 匹配选择器指定`select`，它将从传入的内容中只获取匹配选择器的元素。

```
<div class="banner">
  <div class="banner-title">
    <ng-content select="h1"></ng-content>
  </div>
  <div class="banner-description">
    <ng-content select="p"></ng-content>       
  </div>
</div> 
```

对于`banner-title`，我们以`h1`标签为目标，对于`banner-description`，我们从传递的内容中寻找`p`标签。

结果会是

[![Alt Text](img/c0c7b9584227066f741cccac2a7eabef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AoV3CD2R--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2cfd6e952f9ebof3lhcv.png)

这就是我们想要的。厉害！

记住`select`属性接受 CSS 选择器，所以你也可以使用 class 或者任何有效的 CSS 选择器，例如:

```
<ng-content select=".my-title"></ng-content> 
```

[stack blitz 上的代码示例](https://stackblitz.com/edit/20190831-ng-content)

# 总结

`ng-content`是将内容传递给组件的一种方式。它具有`select`属性，允许我们拥有多个内容投影。

希望这篇文章有用！