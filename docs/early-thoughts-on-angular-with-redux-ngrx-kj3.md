# 早期关于角坐标的思考

> 原文：<https://dev.to/integerman/early-thoughts-on-angular-with-redux-ngrx-kj3>

我在 Angular 2、4、6 和现在的 8 中构建了许多单页面应用程序(出于某种原因，我更喜欢偶数版本)。

对于 8，我研究了 NgRx，这是一种非常不同的体验。我想分享一些早期的想法，因为我是在这个旅程的早期。这将更多地是关于我的反应，而不是关于代码，尽管我打算以后用一个适当的教程来跟进这个问题。

# 棱角分明的英雄气概

我以前的角度经验围绕着:

*   将一个页面分解成用户界面所需的单个组件
*   为所需的每个主要应用程序数据引入一个*服务*,以及日志记录、web 通信、显示通知等专门服务。
*   与*路线*和*子路线*一起布线
*   使用*解析器*从*服务*中提取数据，并基于选择的*路由*将它们提供给*组件*
*   使用*警卫*来防止*路线*的无效使用

大多数 Angular 开发都包括添加组件或服务，然后在`ngOnInit`和`ngOnDestroy`中管理订阅，并在模板中绑定。依赖注入使得从组件中轻松访问服务变得简单可行。

# 状态反击

但是事情变得复杂了。在具有客户端和服务器状态的应用程序中，用户将进行更改，然后您将调用服务器来告诉服务器有关本地更改的信息。如果服务器拒绝更改，您必须更新 UI。同时，用户可能正在执行导致其他呼叫的其他动作。此外，一些后续调用可能会在其早期调用返回到 UI 之前完成。

所有这些都酝酿了一场混淆国家和通信相关漏洞的机会风暴。为了观察应用程序的状态以及哪些操作导致了用户界面的混乱，我实际上采取了录制我的网络标签与应用程序用户界面混合的视频的方法。

# NgRx 来救场？

[NgRx，或 Angular 的反应库](https://ngrx.io)，为 Angular 带来了状态管理的 Redux 模式。Redux 模式被设计成一个面向状态的循环，其中 UI 调度 reducer 用来更新状态的动作，然后这些动作被调度回 UI。这里的关键因素是状态是在一个集中的点上管理的，只有少数 reducer 操作可以修改状态，所以如果状态管理中出现了 bug，很容易识别出罪魁祸首。

除此之外，[Redux](http://extension.remotedev.io/)的开发工具非常棒。

[![Redux Dev Tools](img/c6baa6462540b88ac609c05932683eaf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_ae6bT8_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wnsqmdgtotd2l85nffcy.png)

这些工具让您可以随时查看应用程序状态的历史，甚至可以在不同的状态版本之间切换，并观察您的 UI 更新。这大大简化了调试异步状态变化的复杂性。

这样做的缺点是，你不能为你的大多数操作处理服务和方法，你必须以一种更加被动的方式思考，考虑事件流、选择器等等。

例如，响应一个用户事件，而不是这个:

```
 public onBeginShiftClick(): void {
    this.shiftService.beginShift();
  } 
```

Enter fullscreen mode Exit fullscreen mode

你会这样做:

```
 public onBeginShiftClick(): void {
    this.state.dispatch(beginShiftAction());
  } 
```

Enter fullscreen mode Exit fullscreen mode

它被分派到一个类似这样的缩减器:

```
const gameReducer = createReducer(GameSimulator.buildDefaultState(),
  on(beginShiftAction, state => GameSimulator.simulate(state)),
  // ....
); 
```

Enter fullscreen mode Exit fullscreen mode

我承认，不太难。

不同之处在于如何从 redux 存储中获取数据。而不是像这样做:

```
@Component({
  selector: 'ssit-crew-page',
  templateUrl: './crew-page.component.html',
  styleUrls: ['./crew-page.component.styl']
})
export class CrewPageComponent implements OnInit {

  public crew: CrewMember[];

  constructor(private crewService: CrewService) {
  }

  ngOnInit() {
    this.crew = this.crewService.crew;
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

你应该做:

```
@Component({
  selector: 'ssit-crew-page',
  templateUrl: './crew-page.component.html',
  styleUrls: ['./crew-page.component.styl']
})
export class CrewPageComponent implements OnInit {

  public crew$: Observable<CrewMember[]>;

  constructor(private store: GameStateStore) {
  }

  ngOnInit() {
    this.crew$ = this.store.select(this.store.getCrewMembers);
  }

} 
```

Enter fullscreen mode Exit fullscreen mode

因为您现在正在处理一个可观察的流，所以您还必须记住在您的模板中使用异步管道(例如`crew | async`),以便自动订阅/取消订阅事件。

此外，您需要在状态存储中实现选择器:

```
const gameSelector = createFeatureSelector<GameState>('game');

export class GameStateStore extends Store<GameState> {
  public readonly getCrewMembers = createSelector(gameSelector, (state: GameState) => state.crew);
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

# 关闭思绪

总的来说，我还处于 NgRx 之旅的早期。有明确的优点和缺点，所以它并不适合每个项目。

## 优点

*   创建非常清晰的状态更改审计跟踪
*   状态变化发生在孤立的位置
*   不需要订阅/取消订阅来自服务的事件(从技术上来说，这些可以用可观察的流和管道来处理，但以前我订阅过，所以我把新的行为捆绑到 NgRx 中)

## 弊

*   比使用单个服务更复杂
*   选择器语法可能很难解释
*   异步管道可能很麻烦，而且会搞乱你的模板
*   我的开发速度要慢得多，但这在很大程度上是由于不断学习不同风味的 Angular

我将继续在 NgRx 和 Angular 上工作，我的学习项目是。我会随时通知你。