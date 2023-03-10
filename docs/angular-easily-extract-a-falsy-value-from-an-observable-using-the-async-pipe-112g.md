# Angular:使用异步管道很容易从一个可观察对象中提取一个伪值？

> 原文：<https://dev.to/maxime1992/angular-easily-extract-a-falsy-value-from-an-observable-using-the-async-pipe-112g>

无论你已经使用 Angular 很多年了，还是一天前才开始使用，你可能都知道`ngIf`指令。它允许您根据条件显示(创建或删除)一个 DOM 元素。

听起来很简单，但似乎没有多少人知道`ngIf`的一个(固有)功能。

# 目录

*   [从模板中解开观察值](#unwrap-observables-from-the-template)
*   如何从可观察值中解开虚假值？
    *   [将值包装成一个对象](#wrap-the-value-into-an-object)
    *   [从模板中使用 ngIf 本身！](#use-ng-if-itself-from-the-template)

# 从模板中解开可观测量

因为 Angular 与 RxJs 配合得非常好，所以当您通过使用`async`管道和`as`语法:
得到一个可观察对象时，您可以利用`ngIf`指令

```
<div *ngIf="user$ | async as user">
  {{ user.name }}
</div> 
```

**但是，如果你想使用`async`管道从一个可观察对象中提取一个值，而这个值是 falsy，那么会发生什么呢？**

例如一个布尔型的`false`、`0`(数字)、一个空字符串、`null`或`undefined`值等。

该值不会显示，因为`ngIf`条件将被评估为 falsy。

这里有一个长期存在的问题:[https://github.com/angular/angular/issues/15280](https://github.com/angular/angular/issues/15280)

# 如何从可观察值中解开虚假值？

上面这个问题的主要建议是有一个`ngLet`指令，它具有与`ngIf`相同的功能，但是即使值是 falsy 也总是显示模板。

这个指令会是解决这种情况的灵丹妙药吗？如果会，我们如何在没有它的情况下应对呢？

## 将值包装成一个对象

使用`map`操作符将可观察对象中的值包装成一个对象。

示例:

```
const wrappedValue$ = falsyValue$.pipe(map(value => ({ value }))); 
```

如果有一个合适的类型和一个定制的 rxjs 操作符，我们可以做得稍微好一点。这将减少样板文件、代码重复并提高可读性。还不错！

但是因为显示问题修改我们的数据流，感觉我们可以/应该做得更好。我们可以通过使用...只有棱角！

## 使用 ngIf 本身！

Angular 编译器允许你直接从你的模板:

```
<div *ngIf="{ falsyValue: falsyValue$ | async } as data">
  {{ data.falsyValue}}
</div> 
```

这显然不是一个众所周知的功能，但它确实非常有用，原因有很多:

*   在上面的例子中，`data`对象总是被定义的，所以不管该对象中有什么，`ngIf`条件总是被评估为`true`。这可以代替`ngLet`指令

*   我们可以通过一次订阅多个属性来进一步推动事情，不需要嵌套的`div`或`ng-container`！

之前:

```
<ng-container *ngIf="wrappedValue1$ | async as wrappedValue1">
  <ng-container *ngIf="wrappedValue2$ | async as wrappedValue2">
    <ng-container *ngIf="wrappedValue3$ | async as wrappedValue3">
      {{ wrappedValue1 }} {{ wrappedValue2 }} {{ wrappedValue3 }}
    </ng-container>
  </ng-container>
</ng-container> 
```

之后:

```
<div
  *ngIf="{
    value1: value1$ | async,
    value2: value2$ | async,
    value3: value3$ | async
  }"
>
  {{ value1 }} {{ value2 }} {{ value3 }}
</div> 
```

干净多了不是吗？

# 结论

我认为有一个`ngLet`指令可能会使**清晰**我们想要达到的目标，更重要的是，当我们只有**一个可观察对象来管理**时，它可能会有用，能够做以下事情，而不是把它包装成一个对象:

```
<div *ngLet="value$ | async as value">
  {{ value }}
</div> 
```

但是现在，解决方法是微不足道的，不应该阻止您在转换模板端而不是在您的 TS 代码中进行转换。

编码快乐！

# 发现错别字？

如果你在这篇博文中发现了一个错别字、一个可以改进的句子或者任何其他应该更新的内容，你可以通过 git 存储库访问它，并发出一个 pull 请求。请不要发表评论，直接前往[https://github.com/maxime1992/my-dev.to](https://github.com/maxime1992/my-dev.to)并打开一个包含您所做更改的新拉动式请求。