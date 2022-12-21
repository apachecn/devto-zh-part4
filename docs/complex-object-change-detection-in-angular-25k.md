# 复杂物体角度变化检测

> 原文：<https://dev.to/cliffeby/complex-object-change-detection-in-angular-25k>

[![Angular Logo](img/9887bc00a4f26feaca7ede479cfd0902.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4NiaU-1r--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://d6vdma9166ldh.cloudfront.net/medimg/bd9734c9-def0-47ee-b9ec-027fcfe3cae8.png)

我正在开发一个有几个亲子组件的 Angular 应用程序。使用@Input()和@Output()装饰器在组件之间传递数据进展顺利。当我开始使用角度材料表进行数据显示时，我的数据对象的复杂性增加了。突然，我在视图中发现了不一致的行为。有时数据被更新；其他时候部分，或根本没有。

研究“变更检测循环”和关于如何强制或限制变更检测的帖子，对解决我的问题没有什么帮助。关于区域、状态改变触发器和 DOM 树的博客是信息性的，但是没有一个解释不一致的行为。在数百个控制台日志和断点之后，我发现了我的问题。持有数组引用的对象属性仅在对象中的另一个值属性改变时更新。我怀疑我在帖子里读到过类似的东西，但对于一个非计算机科学专业的人来说，它超出了我的理解范围。

让我们看一个例子。我的角度数据模型在类 Match:
中定义

```
 export class Match {
  name: string;
  playerNames: string[];
} 
```

我的应用程序组件创建了一个 Match 实例，它有三个方法:

1.  addName -向 playerNames 数组添加一个名称。
2.  changeMatchName -更改匹配名称，以及
3.  传播-稍后讨论。

```
export class AppComponent {
  title = "chg-detection";
  match = new Match();
  counter: number;
  constructor() {
    this.match.playerNames = [];
    this.match.name = 'Match 0';
    this.counter = 1;
  }
  addName() {
    this.match.playerNames.push("Bob" + this.match.playerNames.length);
  }
  changeMatchName(counter) {
    this.match.name = "Match " + counter.toString();
    this.counter++;
  }
  spread() {
    this.match.playerNames = [
      ...this.match.playerNames,
      "Chuck" + this.match.playerNames.length
    ];
  }
} 
```

html 是:

```
 <div style="text-align:center">
    <h1>
      Welcome to {{ title }}!
    </h1>
    <img width="300" alt="Angular Logo" src="https://d6vdma9166ldh.cloudfront.net/medimg/bd9734c9-def0-47ee-b9ec-027fcfe3cae8.png">
  </div>
  <div>1: {{match.name}} Player names: {{match.playerNames}} # of players :{{match.playerNames.length}}</div>
  <br>
  <div>2: {{match.name}} Player names: {{match.playerNames}} </div>
  <br>
  <button  color="primary" (click)="addName()">
    Add Player
  </button>
  <button  color="primary" (click)="changeMatchName(counter)">
    Change Match Name
  </button>
  <button  color="primary" (click)="spread()">
    Spread
  </button> 
```

请注意， **div 行 1 包含一个值属性 length，它不包含在 div 行 2** 中。

## 问题

*当应用程序启动时，它输出:*

1:匹配 0 名玩家姓名:玩家人数:0

2:匹配 0 个玩家姓名:

第一步:我添加了一个玩家，第一行添加了玩家的名字，但是第二行没有。

1:匹配 0 玩家姓名:Bob0 玩家数量:1

2:匹配 0 个玩家姓名:

第二步:我更改了比赛名称。第 1 行和第 2 行都将玩家名字更新为匹配对象的当前状态

1:匹配 1 玩家姓名:Bob0 玩家人数:1

第二场:第一场比赛球员姓名:Bob0

## 那么到底发生了什么？

由于 addName()方法是**在 match.playerNames 数组上推**一个值，所以数组的引用值没有改变。只有在视图中数组长度的“值”发生变化的第 1 行，Angular 的变化检测循环才更新插值表达式的 div。因此，不仅组件和它的视图有不同的变更检测循环，而且视图在更新什么方面是细粒度的。这导致第 1 行显示玩家姓名的当前值，第 2 行显示旧值。

在第 2 步中，更改由 value 指定的比赛名称，会在两个 div 行上创建一个变化检测循环，从而导致两行显示球员的当前状态。

这个 [StackBlitz](https://stackblitz.com/edit/angular-sa1un1) 链接提供了一个工作演示。

## 为什么会出现这种不一致？

对于大多数应用程序，我希望对象属性的当前值是视图中所预期的。我还没有看到为什么数组或其他引用对象不是变化检测的一部分的解释，但我怀疑这是基于性能的，因为导航整个树和特别大的数组可能是昂贵的。为了支持这个结论，Angular 提供了一个“改变检测策略”。OnPush”策略将变更检测限制在组件树的一部分。的基本原理。onPush 就是性能提升。

## 修罗

有几个选项可以强制检测参考值的变化。它们都依赖于角度变化检测原理，即**新的**物体总是被更新。

1.  使用 Redux 存储的 ngrx 方法。
2.  不可变. js 的使用
3.  ES6 扩展运算符的使用。

我选择了 spread 操作符，因为它看起来最容易实现，最容易理解，并且是 javascript 的固有特性。spread 操作符的形式为 data = {…data，new}，其中 new 替换或添加现有数据对象的值，并创建一个新的**对象值。更多关于传播可以在这里找到[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)**

在我的例子中:

```
this.match.playerNames.push(‘Bob’) 

becomes

this.match.playerNames = [...this.match.playerNames, 'Chuck']; 
```

另一种方法是使用服务来检索数据对象。在我的例子中，服务器数据模型使用 match 中的玩家 id 属性来引用所有玩家属性。在该模型中包含 playerNames 会给后端数据存储增加冗余数据，或者会使用本地存储创建复杂的 angular 服务。

在这里阅读更多