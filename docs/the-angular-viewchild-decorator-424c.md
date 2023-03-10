# Angular @ViewChild 装饰器

> 原文：<https://dev.to/angular/the-angular-viewchild-decorator-424c>

在 Angular 8 中，@ViewChild decorator 的静态参数[变成了临时强制参数](https://angular.io/guide/static-query-migration)。
在以前的版本中，Angular 自动决定查询必须是静态的还是动态的，由于我不熟悉这个参数，我认为这是深入研究它并撰写我的第一篇博文的好时机！😄

在这篇文章中，我将简要介绍什么是装饰器以及如何使用它，然后我们将深入探讨`@ViewChild`装饰器并解释其参数的作用。

## 装饰者和装饰者模式

当谈到编程语言时，装饰者这个词可以指两种不同的东西:装饰者模式和实际的装饰者。让我们来揭开这些概念的神秘面纱吧！

### 装饰图案

装饰模式是一种 OOP 设计模式，允许动态地向类或类成员添加行为。这意味着，例如，我们可以在对象实例化时改变类的行为，而不改变进一步实例化的行为。我不想在这里深入解释这种设计模式。

### 装修工

我们将在本文中讨论的装饰器是装饰器模式在编程语言中的具体实现。由于本文是关于 Angular 和 TypeScript 的，我们将使用单词 decorator 来指定这种设计模式在 TypeScript 中的具体实现。

Decorators 是一个实验性的 TypeScript 特性，所以可以随时引入突破性的变化。然而，Angular 语法严重依赖于 decorators。

基本上，TypeScript 中的 decorator 是一个函数，它可以使用以`@`开头的注释附加到类或类成员(属性或方法)上。装饰者可以接受参数。
例如，我们可以这样定义一个用于组件的`@isAdmin`装饰器:

```
user = this.userService.getUser();

@isAdmin(user)
deleteEntry(entryId: string) {
// Delete some entry.
} 
```

Enter fullscreen mode Exit fullscreen mode

例如，这个装饰器可以用来限制具有管理员角色的用户对该方法的访问。

装饰声明可能是这样的:

```
import { jwtDecode } from 'jwt-decode';

function isAdmin(user: User) {
  return jwtDecode(user.token).isAdmin;
} 
```

Enter fullscreen mode Exit fullscreen mode

很酷，不是吗？装饰者可以通过将行为包装在可重用的函数中来帮助我们构建代码。

如果你熟悉 Angular，你可能会注意到我们是如何声明 Angular 组件、模块等的。例如，Angular 组件是一个用`@Component` decorator 注释的类，这个 decorator 接受一些参数，比如它的模板 URL 和它的变化检测策略。

Angular 提供的另一个装饰器是`@ViewChild`。我们要关注的就是这篇文章！

## 棱角分明`@ViewChild`装饰工

可以在属性上应用`@ViewChild`装饰器，并允许配置一个**视图查询**。

### 选择器

这个装饰器的第一个参数是**选择器**。Angular 将使用选择器尝试匹配模板中的元素，用装饰器注释的属性将引用第一个匹配元素**。**

一个选择器可以有几种形式，所以让我们来研究一下，并写一些例子。

*   任何带有`@Component`或`@Directive`装饰器的类

```
@Component({
  selector: 'user-card'
})
export class UserCard {
  @Input() firstName: string;
  @Input() lastName: string;
  @Input() age: number;
}

@Component({
  selector: 'myComp',
  template: `
    <user-card [firstName]="'Roger'" [lastName]="'Dupont'" [age]="53">
    </user-card>
  `
})
export class MyCompComponent {
  @ViewChild(UserCard, { static: false }) userCard: UserCard;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
@Directive({
  selector: 'myMenu'
})
export class MenuDirective {
  open() {}
  close() {}
}

@Component({
  selector: 'my-comp',
  template: `
    <div myMenu></div>
  `
})
export class MyCompComponent {
  @ViewChild(MyMenuDirective, { static: false }) menu: MyMenuDirective;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   字符串形式的模板引用变量

```
@Component({
  selector: 'my-comp',
  template: `
    <div #someElement></div>
  `
})
export class MyCompComponent {
  @ViewChild('someElement', { static: false }) someElement: ElementRef;
} 
```

Enter fullscreen mode Exit fullscreen mode

*   模板参考

```
@Component({
  selector: 'my-comp',
  template: `
    <ng-template></ng-template>
  `
})
export class MyCompComponent {
  @ViewChild(TemplateRef, { static: false }) someTemplate: TemplateRef;
} 
```

Enter fullscreen mode Exit fullscreen mode

*角度文档说明有两种其他选择器的可能性:*

*   *当前组件的子组件树中定义的任何提供者(如`@ViewChild(SomeService) someService: SomeService` )*
*   *通过字符串令牌定义的任何提供者(例如`@ViewChild('someToken') someTokenVal: any` )*

然而，我不知道如何应用这些案例。如果有人有答案并想伸出援手，她或他将非常受欢迎。😉

### `static`参数

这就是我们，成为临时强制的参数！我们来看看它的作用是什么。

`static`参数，我相信你已经猜到了，在这里告诉 Angular 查询应该静态运行还是动态运行。但是这在实践中改变了什么呢？
基本上，当视图查询将被解析时，它会改变**。**

Angular 建议在`ngAfterViewInit` [生命周期钩子](https://angular.io/guide/lifecycle-hooks)中检索视图查询结果，以确保依赖于绑定解析(如在`*ngFor`循环或`*ngIf`条件中)的查询匹配已经准备好，因此将被查询找到。要获得这种行为，必须将`static`参数设置为`false`。

让我们看一个例子(打开 StackBlitz 控制台查看日志):

[https://stackblitz.com/edit/angular-viewchild-static-false?embed=1&&file=src/app/hello.component.ts](https://stackblitz.com/edit/angular-viewchild-static-false?embed=1&&file=src/app/hello.component.ts)

将`static`参数设置为 false 涵盖了我们的大多数用例。但是，我们可能会遇到这样的情况:在调用 ngAfterVewInit 挂钩之前，我们需要访问视图查询结果。将`static`设置为 true 通过允许访问 ngOnInit 生命周期钩子中的视图查询结果来允许这种行为，但是**它只对可以静态解析的查询有效**。我们想用`@ViewChild`获取的元素必须不在* `ngFor`循环或`*ngIf`条件中。

让我们看一个例子:

[https://stackblitz.com/edit/angular-viewchild-static-true?embed=1&&file=src/app/hello.component.ts](https://stackblitz.com/edit/angular-viewchild-static-true?embed=1&&file=src/app/hello.component.ts)

如 Angular 文档中所述，`static`在版本 8 中只是强制性的，以方便更改默认值并避免进一步的错误。通过让开发人员考虑这个参数，他们为`@ViewChild`的下一个默认行为做好了准备。

从版本 9 开始，`static`参数默认值将为`false`。之前的行为(缺省值由 Angular 根据视图查询结果的使用方式自动确定)可能会导致一些棘手的错误。

### `read`参数

`read`参数是可选的。此参数允许更改查看查询结果的类型。事实上，每种选择器都有其默认类型:

*   任何带有`@Component`或`@Directive`装饰器的类都是➡️类
*   作为字符串的模板引用变量️️️➡️ `️️️ElementRef`
*   这个结果是真的吗

但是，我们可能希望使用模板引用变量作为字符串进行查询，并使用目标元素的实际类型。同样，我们可以使用一个类作为选择器，并希望通过`ElementRef`类型访问它。

示例的非穷尽列表:

```
@Component({
  selector: 'my-comp',
  template: `
    <user-card #userCard></user-card>
  `
})
export class MyCompComponent {
  // We set read to the UserCard type corresponding to a component class, so the view query result will be of type UserCard.
  @ViewChild('userCard', { read: UserCard, static: false }) userCard: UserCard;
} 
```

Enter fullscreen mode Exit fullscreen mode

使用组件或指令类允许访问该类的属性。例如，一个代表带有用户信息的卡片的`UserCard`组件可以包含一个方法，因此这个方法可以从视图查询结果中以编程方式使用。它看起来像是`this.userCard.flip();`。

```
@Component({
  selector: 'my-comp',
  template: `
    <user-card></user-card>
  `
})
export class MyCompComponent {
  // We set read to ElementRef so, even if the selector is a component class, the query view result will be of type ElementRef.
  @ViewChild(UserCard, { read: ElementRef, static: false })
  userCard: ElementRef;
} 
```

Enter fullscreen mode Exit fullscreen mode

是一个本地元素的包装器，所以它对访问 HTML 属性、类等东西很有用。

```
@Component({
  selector: 'my-comp',
  template: `
    <div #myContainer></div>
  `
})
export class MyCompComponent {
  @ViewChild('myContainer', { read: ViewContainerRef, static: false })
  myList: ViewContainerRef;
} 
```

Enter fullscreen mode Exit fullscreen mode

`ViewContainerRef`允许获取作为容器的元素。当我们需要操作 DOM(例如动态添加或删除节点)时，这是一个很好的选择。

这个参数使我们的查询非常灵活，因为返回的类型可以独立于我们选择使用的选择器类型。

## 快速浏览`@ViewChildren`

还有一个叫`@ViewChildren`的棱角装饰师。

正如我们之前看到的，一个`@ViewChild`查询只返回第一个匹配的元素。那么如果我们想得到所有匹配元素的列表呢？这正是`@ViewChildren`的作用。

它像`@ViewChild`一样需要一个`selector`和一个`read`参数，但没有`static`。唯一可用的行为是动态的，所以查询将只在`ngAfterViewInit`生命周期钩子中解析。

[https://stackblitz.com/edit/angular-viewchildren-always-dynamic?embed=1&&file=src/app/hello.component.ts](https://stackblitz.com/edit/angular-viewchildren-always-dynamic?embed=1&&file=src/app/hello.component.ts)

`@ViewChildren`返回一个`QueryList`对象，其中包含一个`EventEmitter`对象。`QueryList`是动态更新的，因此如果匹配的元素被添加或删除，`QueryList`将发出一个新事件，因此我们可以订阅它并对值的变化做出反应。

## 第一篇野外文章

耶，你到达了我的第一篇文章的结尾，祝贺你！

欢迎任何建议和意见😄

## 有用的链接和来源

*   [排版装饰者文档](https://www.typescriptlang.org/docs/handbook/decorators.html)
*   [装饰者模式维基百科页面](https://en.wikipedia.org/wiki/Decorator_pattern)
*   [静态查询迁移(为什么 ViewChild 静态参数变成临时强制的)](https://angular.io/guide/static-query-migration)
*   [角度生命周期挂钩](https://angular.io/guide/lifecycle-hooks)
*   [Angular ElementRef documentation](https://angular.io/api/core/ElementRef)
*   [角度模板参考文件](https://angular.io/api/core/TemplateRef)
*   [角度视图容器参考文件](https://angular.io/api/core/ViewContainerRef)