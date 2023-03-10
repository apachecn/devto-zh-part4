# Angular 中 RxJS 的灵活缓存和刷新

> 原文：<https://dev.to/prestonjlamb/flexible-caching-and-refreshing-with-rxjs-in-angular-hlo>

## tldr；

几周前，我写了一篇关于如何使用 RxJS 在我们的服务中缓存数据并与新订户共享的文章，这也使得当新订户在一定时间后到来时，数据可以自动更新。点击这里阅读[那篇文章以获取更多信息，因为这篇文章是建立在那篇文章的基础上的。当我继续我的项目，确保尽可能地缓存调用时，我遇到了一个调用，我们需要向 API 传递一个参数，比如一个 ID。如果可能的话，我仍然想缓存这个调用，但是我不想创建很多我要跟踪的私有变量。我只想缓存请求的最后一项。在服务中使用少量的逻辑，我就能做到这一点。](https://dev.to/blog/content-projection-in-angular)

*   [Stackblitz 示例](https://stackblitz.com/edit/pjlamb12-flexible-caching-and-refreshing-of-data)
*   [GitHub 回购](https://github.com/pjlamb12/pjlamb12-flexible-caching-and-refreshing-of-data)

## 用 RxJS 缓存数据

在 RxJS 中缓存数据是一个方便的特性，可以提高站点的响应性(或者至少用户会有这种感觉)。在一些设备和场景中，节省额外的 HTTP 调用非常重要。因此，当数据不经常改变时，缓存返回的数据并在多个地方共享它是很有意义的，或者当用户离开路线并返回同一页面时也是如此。要了解更多关于我目前最喜欢的方法，请阅读我上一篇关于使用 RxJS [缓存和刷新数据的文章。在那篇文章中，您将学习如何创建一个可观察对象，它将与新订户共享 HTTP 调用数据，但是在用户定义的时间量之后，调用将被重新进行，以便数据是新的。下面是它的样子，作为那些已经读过那篇文章的人的复习:](https://dev.to/blog/content-projection-in-angular) 

```
let returnObs$: Observable<any>;

const createReturnObs = (obs: Observable<any>, time: number, bufferReplays: number) =>
    (returnObs$ = obs.pipe(shareReplay(bufferReplays, time)));

export function renewAfterTimer(obs: Observable<any>, time: number, bufferReplays: number = 1) {
    return createReturnObs(obs, time, bufferReplays).pipe(
        first(null, defer(() => createReturnObs(obs, time, bufferReplays))),
        mergeMap(d => (isObservable(d) ? d : of(d))),
    );
} 
```

Enter fullscreen mode Exit fullscreen mode

> 这个函数创建一个 observable，该 observable 在用户定义的时间内缓存数据，然后如果一个新的订阅者在它“过期”后到来，则再次进行 HTTP 调用并替换缓存的数据。

这个函数非常好用，我今天要展示的新函数使用了上面的方法，但增加了一点灵活性。

## 灵活缓存数据

上面的函数非常适合不需要参数的数据，比如条目列表之类的。在这种情况下，它是完美的，因为你可以创建可观察的数据，并且知道每次有人请求该数据时，它都是相同的。但是，如果您想要获取某个项目的详细信息并缓存它，该怎么办呢？

在我的例子中，我们有一些本月需要加载的数据。大多数情况下，他们只想要当月的数据，所以我们希望缓存响应。但他们偶尔会回到上个月来查看这些数据。在这种情况下，我们希望用新数据替换缓存。然而，我不想做的是拥有一个我试图跟踪的可观测量的大列表，在每次调用函数时创建一个新的列表。这很好，可能行得通，但是我希望在我的服务中有一个可以观察到的东西被传递。

我是这样做的:

```
export class SwapiService {
    constructor(private _http: HttpClient) {}

    private lastCharacterQueried: string;
    private steps$: Observable<any>;

    getCharacter(characterId: string) {
        if (characterId !== this.lastCharacterQueried) {
            this.steps$ = renewAfterTimer(this._http.get(`https://swapi.co/api/people/${characterId}`), 10 * 1000);
            this.lastCharacterQueried = characterId;
        }
        return this.steps$;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我使用星球大战 API([swapi.co](https://swapi.co))来获取单个角色的细节。为此，我调用了`getCharacter`函数，并向该函数传递了一个 ID。我检查 ID 是否与最后传入的 ID 相同。如果是这样，我只是返回我已经创建的可观察对象。如果现在，我创建一个 10 秒后更新的新可观察物，然后保存角色 ID 并返回可观察物。

## 结论

如果您使用这个模式并订阅了 observable，然后在时间限制内创建第二个订阅者，那么 HTTP 调用只进行一次。10 秒钟后，电话再次发出。它工作得很好，允许你想缓存多久就缓存多久。它很灵活，允许您根据需要提供任意多的参数。同样的模式也可以被扩展，也许可以基于一个参数将可观察值放入一个`Map`中，如果它们存在，就从`Map`中访问它们。

我不得不说，我对 RxJS 了解得越多，我就越喜欢它，也越看到它的价值。如果你不太了解 RxJS，我鼓励你花几分钟时间开始钻研。有很多资源，但是[本·莱什](https://twitter.com/BenLesh)、[李美玲](https://twitter.com/ladyleet)和[亚伦·弗罗斯特](https://twitter.com/aaronfrost)是我最喜欢的三个经常谈论/教授/发关于 RxJS 的推特粉丝。