# 不要遵循 RxJS 最佳实践

> 原文：<https://dev.to/nikpoltoratsky/don-t-follow-rxjs-best-practices-4893>

如今，越来越多的开发人员学习 RxJS，并根据最佳实践正确使用它。但是我们不应该。所有这些所谓的最佳实践都需要学习新的东西，并在项目中添加额外的代码。此外，使用我们冒着风险的最佳实践来创建一个良好的代码库，让您的团队成员开心！🌈
别再做灰团了！打破规则！停止使用最佳实践！

以下是我对你如何处理 Angular 中那些所谓的 RxJS 最佳实践的建议:

*   [不退订](#dont-unsubscribe)
*   [订阅内线订阅内线订阅内线…](#subscribe-inside-subscribe)
*   [永远不要使用纯函数](#pure-functions)
*   [始终手动订阅，不使用异步管道](#subscribe-manually)
*   [暴露你的服务对象](#expose-subjects)
*   [总是将流传递给子组件](#pass-streams-to-children)
*   [大理石图？不，不是给你的](#marble-diagrams)

* * *

# 不退订

每个人都说我们必须总是取消订阅 observables 来防止内存泄漏。

但我不能认同。说真的，是谁决定你必须退订 observables 的？你没必要这么做。我们来玩个游戏吧！那些角度组件的退订实现哪个最好？

带`takeUntil`符的那个？

```
@Component({ ... })
export class MyComponent implements OnInit, OnDestroy {

  private destroyed$ = new Subject();

  ngOnInit() {
    myInfiniteStream$
      .pipe(takeUntil(this.destroyed$))
      .subscribe(() => ...);
  }

  ngOnDestroy() {
    this.destroyed$.next();
    this.destroyed$.complete();
  }
} 
```

还是带`takeWhile`运算符的那个？

```
@Component({ ... })
export class MyComponent implements OnInit, OnDestroy {
  private alive = true;
  ngOnInit() {
    myInfiniteStream$
      .pipe(takeWhile(() => this.alive))
      .subscribe(() => ...);
  }
  ngOnDestroy() {
    this.alive = false;
  }
} 
```

没错。都不是！`takeWhile`和`takeUntil`操作符都是隐式的，可能很难读懂🤓(讽刺)。最好的解决方案是将每个订阅存储在一个单独的变量中，然后在组件销毁时以一种显式的方式取消订阅:

```
@Component({ ... })
export class MyComponent implements OnInit, OnDestroy {

  private subscription;

  ngOnInit() {
    this.subscription = myInfiniteStream$
      .subscribe(() => ...);
  }

  ngOnDestroy() {
    this.subscription.unsubscribe();
  }
} 
```

这在你有多个订阅的情况下特别有效:

```
Component({ ... })
export class MyComponent implements OnInit, OnDestroy {

  private subscription1;
  private subscription2;
  private subscription3;
  private subscription4;
  private subscription5;

  ngOnInit() {
    this.subscription1 = myInfiniteStream1$
      .subscribe(() => ...);
        this.subscription2 = myInfiniteStream2$
      .subscribe(() => ...);
        this.subscription3 = myInfiniteStream3$
      .subscribe(() => ...);
        this.subscription4 = myInfiniteStream4$
      .subscribe(() => ...);
        this.subscription5 = myInfiniteStream5$
      .subscribe(() => ...);
  }

  ngOnDestroy() {
    this.subscription1.unsubscribe();
    this.subscription2.unsubscribe();
    this.subscription3.unsubscribe();
    this.subscription4.unsubscribe();
    this.subscription5.unsubscribe(); 
  }
} 
```

但是这个解决方案还不完美。有哪些地方可以做得更好？你感觉如何？我们怎样才能使代码更加清晰易读呢？是的，我有你的答案！让我们删除所有丑陋的退订声明。

```
@Component({ ... })
export class MyComponent implements OnInit {

  ngOnInit() {
    myInfiniteStream$
      .subscribe(() => ...);
  }
} 
```

太棒了。我们已经删除了所有多余的代码，现在它看起来更简单，甚至为我们节省了一点硬盘上的内存。但是`myInfiniteStream$`订阅会发生什么呢？

算了吧！😅让我们把那个工作留给垃圾收集器，否则，它为什么存在，对吗？

* * *

# 使用内线认购内线认购内线…

每个人都说我们应该使用`*Map`操作符来链接观察对象，而不是订阅内部订阅，以防止回调地狱。

但我不能认同。说真的，为什么不呢？为什么我们要使用所有那些`switchMap` / `mergeMap`操作符？你对这个代码有什么看法？易读？你真的那么喜欢你的队友吗？

```
getUser().pipe(
  switchMap(user => getDetails(user)),
  switchMap(details => getPosts(details)),
  switchMap(posts => getComments(posts)),
) 
```

你不觉得它太整洁可爱了吗？你不应该这样写代码！你还有另外一个选择，看看这里:

```
getUser().subscribe(user => {
  getDetails(user).subscribe(details => {
    getPosts(details).subscribe(posts => {
      getComments(posts).subscribe(comments => {  

        // handle all the data here
      });
    });
  });
}) 
```

好多了，是吧？！如果你讨厌你的队友，又不想学习新的 RxJS 操作符，就一直这样写代码吧。

要光明！让你的团队成员对回调地狱有一点留恋。

* * *

# 从不使用纯函数

每个人都说我们应该使用纯函数来使我们的代码可预测并且更容易测试。

但我不能认同。说真的，为什么要用纯函数呢？可测性？可组合性？这很难，但影响全球会容易得多。我们来看看例子:

```
function calculateTax(tax: number, productPrice: number) {
 return (productPrice * (tax / 100)) + productPrice; 
} 
```

例如，我们有一个计算税收的函数，这是一个纯函数，对于相同的参数，它总是返回相同的结果。很容易测试并与其他函数组合。但是，我们真的需要这种行为吗？我不这么认为。使用不带参数的函数会更容易:

```
window.tax = 20;
window.productPrice = 200;

function calculateTax() {
 return (productPrice * (tax / 100)) + productPrice; 
} 
```

事实上，还会有什么问题呢？😉

* * *

# 总是手动订阅，不使用异步

每个人都说我们必须在 Angular 模板中使用`async`管道来促进组件中的订阅管理。

但我不能认同。我们已经与`takeUntil`和`takeWhile`讨论了订阅管理，并一致认为这些运营商是邪恶的。尽管如此，我们为什么要用另一种方式对待`async`管道呢？

```
@Component({  
  template: `
    <span>{{ data$ | async }}</span>
  `,
})
export class MyComponent implements OnInit {

  data$: Observable<Data>;

  ngOnInit() {
    this.data$ = myInfiniteStream$;
  }
} 
```

你看到了吗？干净，可读，易于维护的代码！啊。是不允许的。对于我来说，将数据放在局部变量中并在模板中使用该变量会好得多。

```
@Component({  
  template: `
    <span>{{ data }}</span>
  `,
})
export class MyComponent implements OnInit {
  data;

  ngOnInit() {

    myInfiniteStream$
      .subscribe(data => this.data = data);
  }
} 
```

* * *

# 揭露臣民脱离自己的服务

在 Angular 中使用可观察的数据服务有一个非常常见的实践:

```
@Injectable({ providedIn: 'root' })
export class DataService {

  private data: BehaviorSubject = new BehaviorSubject('bar');

  readonly data$: Observable = this.data.asObservable();

  foo() {
    this.data$.next('foo');
  }

  bar() {
    this.data$.next('bar');
  }
} 
```

这里我们将数据流公开为可观察的。只是为了确保它只能通过数据服务接口进行更改。但是会让人很困惑。

你想改变数据，你必须改变数据。

如果我们可以改变这个地方的数据，为什么还要添加额外的方法呢？让我们重写服务，使其更易于使用；

```
@Injectable({ providedIn: 'root' })
export class DataService {
  public data$: BehaviorSubject = new BehaviorSubject('bar');
} 
```

耶！你看到了吗？我们的数据服务变得更小，更容易阅读！此外，现在我们可以把几乎任何东西放入我们的数据流！太棒了，你不觉得吗？🔥

* * *

# 总是将流传递给子组件

你听说过智能/转储组件模式吗，它可以帮助我们将组件彼此分离？此外，该模式防止子组件触发父组件中的动作:

```
@Component({
  selector: 'app-parent',
  template: `
    <app-child [data]="data$ | async"></app-child>
  `,
})
class ParentComponent implements OnInit {

  data$: Observable<Data>;

  ngOnInit() {
    this.data$ = this.http.get(...);
  }
}

@Component({
  selector: 'app-child',
})
class ChildComponent {
  @Input() data: Data;
} 
```

你喜欢吗？你的队友也喜欢。万一你想报复他们，你需要用下面的方式重写你的代码:

```
@Component({
  selector: 'app-parent',
  template: `
    <app-child [data$]="data$"></app-child>
  `,
})
class ParentComponent {

  data$ = this.http.get(...);
  ...
}

@Component({
  selector: 'app-child',
})
class ChildComponent implements OnInit {

  @Input() data$: Observable<Data>;

  data: Data;

  ngOnInit(){
    // Trigger data fetch only here
    this.data$.subscribe(data => this.data = data);
  }
} 
```

你看到了吗？我们不再在父组件中处理订阅。我们只是将订阅传递给子组件。相信我，如果你遵循这种做法，你的团队成员会在调试过程中痛哭流涕。

* * *

# 大理石图？不，不是给你的

你知道什么是大理石图吗？没有吗？对你有好处！

让我们假设我们编写了下面的函数并打算测试它:

```
export function numTwoTimes(obs: Observable<number>) {
  return obs.pipe(map((x: number) => x * 2))
} 
```

我们很多人会用大理石图来测试函数:

```
it('multiplies each number by 2', () => { 
  createScheduler().run(({ cold, expectObservable }) => {
    const values = { a: 1, b: 2, c: 3, x: 2, y: 4, z: 6 }
    const numbers$ = cold('a-b-c-|', values) as Observable<number>;
    const resultDiagram = 'x-y-z-|';
    expectObservable(numTwoTimes(numbers$)).toBe(resultDiagram, values);
  });
}) 
```

但是，谁会想学习大理石图的新概念呢？谁想写干净简洁的代码？让我们以通用的方式重写测试。

```
it('multiplies each number by 2', done => {
  const numbers$ = interval(1000).pipe(
    take(3),
    map(n => n + 1)
  )
  // This emits: -1-2-3-|

  const numbersTwoTimes$ = numTwoTimes(numbers$)

  const results: number[] = []

  numbersTwoTimes$.subscribe(
    n => {
      results.push(n)
    },
    err => {
      done(err)
    },
    () => {
      expect(results).toEqual([ 2, 4, 6 ])
      done()
    }
  )
}) 
```

耶！现在看起来好一百倍！

* * *

## 结论

如果你读了上面所有的建议，你就是一个英雄。酪好吧。如果你认识到你的思路，我有一个坏消息要告诉你。这是个玩笑。

[![](img/afd88962a06991ac0a0fb2e4915d1107.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cuqyIqfO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/7zwMdyn/image.png)

拜托，千万别做我在那篇文章里说的事。永远不要让队友哭着恨你。永远努力做一个体面整洁的人。拯救世界——使用模式和最佳实践！

我决定让你振作起来，让你的一天过得好一点。希望你会喜欢。

请继续关注，如果你有什么特别想听的角度话题，请告诉我！