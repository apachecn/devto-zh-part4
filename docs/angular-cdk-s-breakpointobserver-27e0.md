# 角 CDK 转折点观察者

> 原文：<https://dev.to/prestonjlamb/angular-cdk-s-breakpointobserver-27e0>

## tldr；

有时我们需要知道组件的类文件中浏览器窗口的宽度。您可以在组件中为窗口的 resize 事件设置一个`@HostListener`,但是您必须维护服务，编写测试以确保它正常工作，正确处理可观察到的事件以避免内存泄漏，等等。[角度 CDK](https://material.angular.io/cdk/categories) 的`LayoutModule`有一个`BreakpointObserver`类，你可以将它注入到一个组件中，提供一个你想要监视的断点(或多个断点)，它会在你每次越过断点的阈值时发出(也就是说，如果我想知道屏幕何时小于 700px，它会在我越过 700px 时发出一个值，然后当我越过 700px 时再次发出一个值)。

*   [Stackblitz 示例](https://stackblitz.com/edit/pjlamb12-breakpoint-observer-demo)
*   [GitHub 回购](https://github.com/pjlamb12/breakpoint-observer-demo)

## 这就解决了问题

首先，我已经每天使用 Angular 将近 4 年了，从第一个 Angular 2 测试版发布之前，这是我第一次需要这个功能。所以你可能想知道什么时候或者为什么你会需要它。但也有非常有用的时候，这也是类存在于`@angular/cdk`库中的原因。

我最终需要这个功能，因为我使用的是`ngx-gauge`库，在库中设置的选项之一是线条的粗细。您将它作为输入从库中传递给组件，它会以该厚度绘制图表。此外，您可以传入一个设置高度和宽度的`size`属性。因此，我不能使用 CSS 媒体查询来改变图表的厚度或高度和宽度。我有两个选择:1)在页面上有两个图表，一个用于较大的屏幕，一个用于较小的屏幕，或者 2)根据屏幕大小改变这些输入的值。我选择了后者，因为我不想每次做出改变时都要更新两个图表。

这个解决方案(我们一会儿会谈到)对我来说非常有效，但是我要说的是，我认为在这样做之前，你应该首先尝试使用 CSS 媒体查询。例如，没有必要在组件的类文件中记录屏幕宽度，然后根据屏幕宽度将类应用到 HTML 元素(例如用`ngClass`)。*也许*有一个场景是你需要做的，但一般来说，我认为你最好只使用 CSS 媒体查询来设计你的组件。话虽如此，但是，如果您觉得这是您的组件的最佳选择，那就去做吧！我不想让你觉得用`BreakpointObserver`不好。

## 解

由于 CDK 角的存在，这个问题的解决方案非常简单。第一步是将 CDK 安装到您的项目:

```
npm i @angular/cdk 
```

Enter fullscreen mode Exit fullscreen mode

接下来，将`@angular/cdk/layout`中的`LayoutModule`导入您的模块。比如像`app.module.ts`文件里这样:

```
import { LayoutModule } from '@angular/cdk/layout';

@NgModule({
    imports: [..., LayoutModule, ...]
})
export class AppModule {} 
```

Enter fullscreen mode Exit fullscreen mode

在导入`LayoutModule`之后，像使用其他服务一样将`BreakpointObserver`注入到组件中:

```
import { Breakpoint Observer } from '@angular/cdk/layout';

export class MyComponent {
    constructor(private observer: BreakpointObserver) {}
} 
```

Enter fullscreen mode Exit fullscreen mode

好了，现在一切都设置好了，可以使用了。不过，在使用该服务之前，还有一件事需要弄清楚，那就是您希望得到哪些断点的通知。例如，如果您的站点正在使用 Bootstrap，并且您想知道何时处于小屏幕或更低的屏幕，您可以使用“(max-width: 767px)”。您放入字符串的值是 CSS 媒体查询括号的一部分，包括括号。您可以提供单个字符串值或字符串数组。

一旦你确定了你的断点，你有两个选项可以从`BreakpointObserver`中使用。第一种是`isMatched`法。它只是评估您提供的断点，并告诉您值是否匹配。如果将数组传递给此方法，则必须满足所有条件，函数才能返回 true。

```
const matched = this.observer.isMatched('(max-width: 700px)');
// OR
const matched = this.observer.isMatched(['(max-width: 700px)', '(min-width: 500px)']); 
```

Enter fullscreen mode Exit fullscreen mode

如果您只需要在第一次加载页面时进行检查，或者如果您只想偶尔调用该函数进行检查，那么`isMatched`函数非常有用。如果您希望不断得到断点匹配的提示，您可以使用`observe`方法。`observe`方法允许您订阅并在每次窗口通过您定义的宽度时获得更新。如果您只为函数定义了一个宽度，那么每当您超过或低于这个宽度时，它都会发出一个值。如果您提供了一个宽度数组，那么每当您越过数组中某个宽度的阈值时，就会发出一个值。

```
this.observer.observe('(max-width: 700px)').subscribe(result => {
    console.log(this.result);
    // Do something with the result
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里输出的`result`看起来像这样:

```
{  "matches":  true  |  false,  "breakpoints":  {  "(max-width: 350px)":  true  |  false,  "(max-width: 450px)":  true  |  false  }  } 
```

Enter fullscreen mode Exit fullscreen mode

如果满足任何条件，`matches`属性为真。如果需要，您还可以检查单个断点，以查看它们是否被满足。

如果每次浏览器宽度超过某个值时，您需要更改页面上的某些布局，那么这个函数非常有用。正如我在上面的例子中提到的，我需要根据浏览器的宽度来改变图表的大小和厚度，因为横向和纵向可能不同，所以我决定订阅 observer 方法，并在每次它发出一个值时改变它。

关于`BreakpointObserver`需要知道的最后一件事是，CDK 提供了一些内置断点，如果你愿意，你可以使用它们。它们基于 Google 的材料设计规范，值为:

*   电话听筒
*   药片
*   网
*   手动肖像
*   桌面肖像
*   网络肖像
*   手动景观
*   桌面景观
*   网络景观

你可以通过从 CDK 的`layout`文件夹:
中导入`Breakpoints`来使用它们

```
import { Breakpoints } from '@angular/cdk/layout'; 
```

Enter fullscreen mode Exit fullscreen mode

然后你可以在`observe`或`isMatched`函数中使用一个断点，比如`Breakpoint.Handset`。它们可以作为唯一的输入，或者添加到传递给这些函数的数组中。您也可以将自己的断点与内置断点混合使用..

## 结论

就像我之前说的，你可能永远不会用到这个类或者需要这个功能。它并不经常出现，但知道它就在那里，当你确实需要它时，你可以伸手去拿，这很好。我想了解的一件事是，你是否可以使用这个函数来交换角度组件正在使用的模板文件。同样，这是一种罕见的情况，但如果它与桌面体验非常不同，您可能需要它来替换移动体验的内容。我不知道您是否能做到这一点，但是您至少可以根据`BreakpointObserver`的结果显示和隐藏子组件。

如果你以前用过这个，一定要在 Twitter 上或者通过电子邮件让我知道你是如何使用它的。如果你还没有，但将来需要，也让我知道！我喜欢听到其他开发人员如何使用这样的东西，这样我就可以了解更多！