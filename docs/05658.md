# RxJS 中的“欺骗或取消订阅”:自定义角度装饰器

> 原文：<https://dev.to/2muchcoffeecom/trick-or-unsubscribe-in-rxjs-a-custom-angular-decorator-545a>

## 背景

为什么可观测量对您的应用可能是危险的？降低风险的选择有哪些？正如你可能已经猜到的那样，我将谈论“unsubscribe()”，我将很荣幸地向你介绍我的定制解决方案，它拯救了我的一生，也可能拯救了你的一生。

## 可观察世界简介

Angular 的第一个概念之一是通过可观测量的功能反应式编程。Angular 通过 RxJS 库广泛使用 Observable，该库引入了 Observable 类型的实现。我不会在 Angular 或 RxJS 库中详细阐述反应式编程的主题，我将只介绍一些高级原则。

根据 <u>[官文](http://reactivex.io/rxjs/manual/overview.html#introduction)</u>——“可观测量是多个值的懒推集合”。换句话说，它是一个数据流——任何时间值的序列。因此，可观察值是某种高级承诺，随着时间推移，它会将多个值(而不是一个值)推送到回调函数。

为了通知观察对象何时发送数据，并在将来对新数据做出反应，我们需要订阅它，只需调用“subscribe()”方法。正如我上面提到的，可观察对象是某种流本身，这意味着在订阅它之后，它的执行将是无限的。为了取消/完成它并“像婴儿一样睡觉”，我们必须简单地调用一个“unsubscribe()”方法。随和吧？

然而，这是最常见的错误，尤其是在初级用户中，当开发人员只是忘记取消订阅流并继续前进时。一个不再被使用的可观察对象仍然会产生价值。这将直接导致巨大的内存泄漏和应用程序未来不可预测的行为。

## 退订有哪些「高级」选项？

正如我上面提到的，如果你不想射伤自己的腿，你应该记得退订！在 Angular 中，最常见的地方是在“ngOnDestroy”生命周期挂钩中，一旦组件不再使用，Angular 就会执行该挂钩。

当您有一个或两个订阅时，这是最简单的解决方案，但在真正的 Angular 应用程序中，您有几十个订阅。当然，每次“手动”退订都很繁琐。那怎么办呢？让我们考虑一些取消订阅多个可观察对象的“高级”内置方法:

### 1。连锁订阅:

因为 Subscription 是一个本质上具有“unsubscribe()”方法的类，所以它也具有“add()”方法。它允许将一个订阅“添加”到另一个订阅中——父订阅的子订阅。因此，您只需要调用一次 unsubscribe()方法——父订阅取消所有子订阅。看看下面的例子。

```
export class HomeComponent implements OnInit, OnDestroy {
  sub: Subscription = new Subscription();

  constructor(
    private invoicesService: InvoicesService,
    private productsService: ProductsService,
    private customersService: CustomersService,
  ) {
  }
  ngOnInit() {
    this.sub
     .add(
       this.invoicesService.invoices$
         .subscribe(invoices => console.log(invoices))
     )
     .add(
       this.productsService.products$    
         .subscribe(products => console.log(products))
      )
     .add(
       this.customersService.products$    
         .subscribe(products => console.log(customers))
      );
  }
  ngOnDestroy() {
    this.sub.unsubscribe();
  } 
```

然而，在链接中有一个不利的影响——如果一个链接的订阅完成了，例如 products$ stream 抛出一个错误，那么它的下一个后代，我指的是 customers$ stream，将不会被执行。因此，我建议避免链接。

### 2。一系列订阅:

首先，我们创建一个类型为“Subscription[]”的变量，例如“subscriptions”的初始值为空数组。然后，我们创建一个 setter，以避免将每个订阅手动包装在一个“推送”结构中。之后，在 ngOnDestroy 生命周期挂钩中，我们简单地调用数组上的 forEach()方法，并对数组中的每个订阅调用 unsubscribe()方法。查看代码示例:

```
export class HomeComponent implements OnInit, OnDestroy {

  subscriptions: Subscription[] = [];

  private set sub (sub: Subscription) {
    this.subscriptions.push(sub);
  }

  constructor(
    private invoicesService: InvoicesService,
    private productsService: ProductsService,
  ) {
  }

  ngOnInit() {
    this.sub = this.invoicesService.invoices$
      .subscribe(invoices => console.log(invoices));

    this.sub = this.productsService.products$
      .subscribe(products => console.log(products));
  }
  ngOnDestroy() {
    this.subscriptions.forEach(sub => sub.unsubscribe());
  }
} 
```

> ### 3 .RxJS“主题”和"直到"运算符:

首先，我们创建一个变量/流，例如，用 RxJS Subject 的一个新实例来取消订阅$。然后，在任何其他流的管道链内部，我们声明“takeUntil”操作符，只需将我们的 unsubscribe$ stream 传递给它。之后，在 ngOnDestroy 生命周期钩子中，我们调用 next()和 complete()回调我们的主题。这意味着当我们的组件被销毁时，所有的订阅者自动停止接收未来值，因为我们的主题将被完成。让我为您提供一个代码示例:

```
export class HomeComponent implements OnInit, OnDestroy {

  unsubscribe$: Subject<void> = new Subject();

  constructor(
    private invoicesService: InvoicesService,
    private productsService: ProductsService,
  ) {
  }

  ngOnInit() {
    this.invoicesService.invoices$
      .pipe(
        takeUntil(this.unsubscribe$)
      )
      .subscribe(invoices => console.log(invoices));

    this.productsService.products$
      .pipe(
        takeUntil(this.unsubscribe$)
      )
      .subscribe(products => console.log(products));
  }

  ngOnDestroy() {
    this.unsubscribe$.next();
    this.unsubscribe$.complete();
  }
} 
```

> ### 4。 Rxjs "Asynchronous Pipeline":

然而，这是最后一个，也是 Observables 中最可靠、最简洁、最正确的内置退订选项。“AsyncPipe”自动订阅一个可观察对象，返回它发出的最新值，并在组件被销毁时取消订阅。因此，我们不需要做任何事情。所有避免内存泄漏的清理逻辑都是在幕后完成的。太神奇了！请看下面的一个例子:

```
export class InvoicesComponent implements OnInit {

  invoices$: Observable<Invoice[]>;

  constructor(
    private invoicesService: InvoicesService,
  ) {
  }

  ngOnInit() {
    this.invoices$ = this.invoicesService.invoices$;
  }
} 
```

```
<main class="invoices-main">

    <mat-table [dataSource]='invoices$ | async'>
....
    </mat-table> 
<main/> 
```

## 我为什么要定制解决方案，装饰者本身是什么？

AsyncPipe 是可靠的，运行良好，但是，我们经常不仅仅是简单地订阅一个可观察对象并呈现输出，我们还需要在 subscribe()方法中加入一些逻辑。因此，每次我们都必须在组件中重复实现上面提到的高级退订选项之一。

所以，过了一段时间，我决定不要在许多组件中手工做“猴子工作”。我认为将所有取消订阅的逻辑放在一个地方，并在我需要时重用它会很好，此外还可以使我的代码更干净和可维护。而且，多亏了打字稿，我找到了正确、整洁和“棱角分明”的地方——一个室内装潢师。你可能已经知道装饰者在整个 Angular 中被广泛使用，但是如果你不知道装饰者本身是什么，并且问你自己引擎盖下的魔法是什么，让我非常简要地解释一下。

一般来说，Decorator 的主要思想是您可以动态地向对象附加附加功能。更准确地说，在 Typescript 中，装饰器是一个带有参数的纯函数，由@ sign 调用，可以附加到:

*   班级；

*   方法；

*   属性；

*   参数；

*   存取器。

为了以防万一，这里有一个简单的类内示例:

```
function Log() {
  console.log(arguments);
}

@Log
export class HomeComponent {
  ...
}
// printed to console:
// {'0': [Function: HomeComponent]} 
```

总而言之，装饰者只是帮助定制他们在设计时附加的东西。让我们更进一步，我很乐意展示和描述我自己的取消订阅 Observables 的装饰器，我称之为“DestroySubscribers”。

## 我的自定义@DestroySubscribers() decorator

我真的很喜欢 RxJS，但我已经决定自动化取消订阅过程，并在类装饰器和“订阅数组”方法实现的帮助下清理我的代码。

查看“DestroySubscribers”装饰器本身:

```
export function DestroySubscribers(params?) {

 return function (target) {
   params = {
     destroyFunc: 'ngOnDestroy',
     ...params
   };
   const unsubscribableLike: {subscriptions: Unsubscribable[], unsubscribe: () => void} = {
     subscriptions: [],
     unsubscribe,
   };
   const subscriber: string = Reflect.getMetadata('subscription:name', target.prototype, 'subscriber');

   Object.defineProperty(target.prototype, subscriber ? subscriber : 'subscriber', {
     get: () => unsubscribableLike,
     set: subscription => unsubscribableLike.subscriptions.push(subscription),
   });

   if (typeof target.prototype[params.destroyFunc] !== 'function') {
     throw new Error(`${target.prototype.constructor.name} must implement ${params.destroyFunc}() lifecycle hook`);
   }

   target.prototype[params.destroyFunc] = ngOnDestroyDecorator(target.prototype[params.destroyFunc]);

   function ngOnDestroyDecorator(f) {
     return function () {
       unsubscribe();
       return f.apply(this, arguments);
     };
   }

   function unsubscribe() {
     do {
       const sub: Unsubscribable = unsubscribableLike.subscriptions.shift();
       if ( sub && typeof sub.unsubscribe === 'function') { sub.unsubscribe(); }
     } while (unsubscribableLike.subscriptions.length);
   }

   return target;
 };
}

export function CombineSubscriptions(params?) {
 return function (target, propertyKey: string | symbol) {
   Reflect.defineMetadata('subscription:name', propertyKey, target, 'subscriber');
 };
} 
```

从上面的代码中可以看出，“@DestroySubscribers()”装饰器代表了一个“订阅数组”方法，用“@CombineSubscriptions()”装饰器进行了扩展，现在一切都在幕后完成了。我来简单描述一下它的主要代码点。

首先，我用一个空数组创建了一个对象，用于将来的订阅和自定义的取消订阅方法，以便能够一次手动取消所有订阅。然后在 <u>[reflect-metadata](https://www.npmjs.com/package/reflect-metadata)</u> 库和“@CombineSubscriptions”装饰器的帮助下，我从类中获得了当前的属性名，或者将“subscriber”指定为默认名称，并创建 getter 和 setter 方法。之后，我创建了另一个版本的 ngOnDestroy 生命周期挂钩，首先取消对数组中所有订阅的订阅，然后调用并返回默认的原始 ngOnDestroy 方法或传递给 Decorator 的配置中指定的另一个“销毁函数”。就是这样——相当简洁易用。🤓

并且装饰器的实现更加简单。看看这个:

```
@DestroySubscribers({
  destroyFunc: 'ngAfterViewInit',
})
export class HomeComponent implements OnInit, AfterViewInit {

  /*
   Within the @CombineSubscriptions Decorator, you can choose any custom name that you prefer.
   Without the @CombineSubscriptions Decorator, the name by default is 'subscriber'.
  */
  @CombineSubscriptions()
  private subscriber: Unsubscribable;

  constructor(
    private invoicesService: InvoicesService,
    private productsService: ProductsService,
  ) {
  }

  ngOnInit() {
    this.subscriber = this.invoicesService.invoices$
      .subscribe(invoices => console.log(invoices));

    this.subscriber = this.productsService.products$
      .subscribe(products => console.log(products));
  }

  /*
   This method must be declared, even if it's empty.
   Otherwise, the Decorator would throw an Error.
  */
  ngAfterViewInit() {
   console.log('for unsubscribing');
  }
} 
```

> ### Main definition of destroy subscribers decorator:

*   **“subscriber”**-默认情况下代表每个订阅的名称的变量，符合不可订阅的接口。每次当你给“subscribe”变量分配一个订阅时——它被自动推送到底层的订阅数组中。此外，如果您希望在组件销毁之前一次手动取消订阅所有订阅，可以对“subscriber”变量调用 unsubscribe()方法。

*   **" @ combine subscriptions()" Decorator**-如果您想要更改订阅的默认变量名(“subscriber”)并使用您自己的自定义名称，请实现此 Decorator，否则不要应用它。

*   {destroyFunc:' ... '} -将此参数添加到“@ destroy subscribers”Decorator 中，带有一个用于自动退订的钩子的名称，以防您想要更改默认的那个——“ngOnDestroy”生命周期钩子，否则不要应用它。当一个组件被销毁时，更改被调用的函数的能力使您有机会不仅在一个角度范围内使用这个装饰器。

### destroy subscribers 装饰器的实现步骤:

首先，您必须用“@ destroy subscribers()”Decorator 注释该类。

其次，您需要创建一个名为“subscriber”的变量，默认情况下该变量的类型为 unsubscribe，或者如果您想使用自己的自定义名称，只需用“@ combine subscriptions()”Decorator 注释该变量。

第三，当组件不再使用时，您应该将您想要取消订阅的每个订阅分配给该变量。

最后，你必须在一个组件中声明 ngOnDestroy 生命周期钩子，即使它是空的，因为 AOT 编译。否则，装饰器会抛出一个错误。如果您将默认的生命周期挂钩(ngOnDestroy)更改为另一个(ngAfterViewInit ),那么这个方法必须在一个组件中声明，并且 ngOnDestroy 显然是可选的。我告诉过你，这太容易了！

## 结论

总而言之，我想概述一下，装饰者本身没有什么可怕的，而是你应该使用的非常神奇的工具。它们肯定会使你的代码更加可重用、简洁和可读！

此外，感谢 Angular 社区随着时间的推移出现了许多不同的退订解决方案。所有这些都值得注意，让我们的日常生活变得更加轻松！但是，如果你觉得我的方法有用，请不要犹豫发表评论，鼓掌并安装来自<u>[NPM(ngx-destroy-subscribers](https://www.npmjs.com/package/ngx-destroy-subscribers)</u>)的“@DestroySubscribers()”装饰器。

非常感谢你们花时间阅读这篇文章！👏

喜欢吗？我们已经尽力了！去我们的 <u>[博客](https://2muchcoffee.com/blog/)</u> 找更多有用的文章。