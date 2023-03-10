# 白雪公主和七种行为模式-设计模式介绍

> 原文：<https://dev.to/maxwell_dev/snow-white-and-the-seven-behavioral-patterns-a-design-patterns-intro-3ljp>

欢迎来到我的“设计模式童话”系列的第三篇文章！它旨在用一种更容易理解的方式解释设计模式——也就是童话中的隐喻。

上一篇文章介绍了行为模式的前半部分，它让多个对象一起完成复杂的任务。这篇文章涵盖了这一组的后半部分，使用了在另一个经典故事**白雪公主和七个小矮人中解释的简单例子。**

这个系列我已经介绍过两次了，这里就不多说了。让我们开门见山吧。

## 用观察者向矮人发出警报

让我们把白雪公主的故事集中在矮人和他们的采矿工作上。所以让我们以七个小矮人开始他们一天工作的方式开始吧:一个人对其他人大喊“嗨-嗬”,这样他们就知道是时候开始了。在影片中，医生是第一个大喊的人，所以我们在这里也这么做。

多克需要一种大喊的方式，并且确信其他矮人会听到并回应，所以他决定使用观察者模式。首先，他为自己编写了一个类，作为一个调用。他从一个基本的方法开始，列出一个需要召唤的矮人名单。

```
class Caller {
  constructor() {
    this.dwarves = [];
    this.yell = null;
  }

  register(dwarf) {
    this.dwarves.push(dwarf);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最重要的属性是`this.dwarves`，这是他要呼喊的真正矮人的列表。开始是空的，所以他加入了`register`方法来快速添加额外的矮人。名单上的每个侏儒现在基本上都在“听”呼叫者做什么。

一旦他有了添加矮人的方法，他就需要更新所有矮人的方法。所以当他召唤时，他需要带走每个矮人并给他们任何他们需要的信息。

```
class Caller {
  constructor() {
    this.dwarves = [];
    this.yell = null;
  }

  register(dwarf) {
    this.dwarves.push(dwarf);
  }

  updateAll() {
    return this.dwarves.forEach(el => el.update(this));
  }

  callOut(yell) {
    this.yell = yell;
    this.updateAll();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`updateAll`函数是让每个正在听的侏儒说“打电话的人在喊，所以你也要喊！”每个切屑都通过了`Caller`，这将告诉他们需要喊什么。

我们可以看到矮人在`DwarfObserver`职业中的反应。

```
class DwarfObserver {
  constructor() {
    this.yell = null;
  }

  update(caller) {
    this.yell = caller.yell;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

每当一个侏儒被“更新”时，我们就告诉他们喊博士喊的。换句话说，当他们听到医生喊的时候，他们应该喊同样的话。这是观察者模式的核心，其中一个对象观察另一个对象的变化，并在变化发生时采取特定的行动。

让我们通过定义 Doc 为调用者，并定义其他四个矮人监听他来设置这个特定的例子。

```
const Doc = new Caller();

const Happy = new DwarfObserver(),
      Bashful = new DwarfObserver(),
      Sneezy = new DwarfObserver(),
      Grumpy = new DwarfObserver(); 
```

Enter fullscreen mode Exit fullscreen mode

我们会把每个矮人都登记到医生那里，这样他们就可以开始听他喊叫了。

```
const dwarves = [Happy, Bashful, Sneezy, Grumpy]
dwarves.forEach(dwarf => Doc.register(dwarf)); 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我们告诉博士叫出来，他会自动更新其他矮人，所以他们也大喊！

```
Doc.callOut('Heigh Ho!');

Doc.yell;
// 'Heigh Ho!'

Happy.yell;
// 'Heigh Ho!'
Bashful.yell;
// 'Heigh Ho!'
Sneezy.yell;
// 'Heigh-Ho!'
Grumpy.yell;
// 'Heigh Ho!' 
```

Enter fullscreen mode Exit fullscreen mode

## 用迭代器过桥

现在矮人开始工作了，但是他们还没有到达矿场。假设现在是凌晨，他们需要穿过一座狭窄的桥才能到达矿井。但是外面太黑了，他们几乎看不到其他的矮人，他们不能冒险让多个矮人同时穿越。

博士认为他们需要一个小心的方法让每个矮人通过，这样可以记录需要通过的人数。所以他写了一个迭代器类来完成这个任务。

```
class DwarfIterator {
  constructor(dwarves) {
    this.index = 0;
    this.dwarves = dwarves; // is an array
  }

  hasNext() {
    return this.index < this.dwarves.length;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在这个班没做什么。它有一个矮人列表，一个索引零，所以它在列表的开始，还有一个函数告诉他们是否在列表的末尾。这很有帮助，但不足以让他们过桥。医生需要它来跟踪谁会在下一个过桥，并向他们大喊。

所以他又增加了两个方法。`nextDwarf`会告诉当前矮人穿越，并随着列表进一步增加指数。他补充道`callNextDwarf`在召唤下一个矮人之前，它会检查下一个是否有矮人。

```
class DwarfIterator {
  constructor(dwarves) {
    this.index = 0;
    this.dwarves = dwarves;
  }

  hasNext() {
    return this.index < this.dwarves.length;
  }

  nextDwarf() {
    return `${this.dwarves[this.index++]}, you may cross!`;
  }

  callNextDwarf() {
    return this.hasNext() ? this.nextDwarf() : "Everyone is here!";
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

有了这个设置，Doc 可以简单地传入矮人列表，并一遍又一遍地使用`callNextDwarf`。迭代器让他们可以很容易地浏览矮人列表，抽象出跟踪他们在哪里以及要采取什么行动的工作。

让我们建立一个矮人列表(我们可以用一个字符串数组代替对象来节省时间)，并将它们传递给迭代器。

```
const crossingDwarfs = [
  "Sneezy",
  "Sleepy",
  "Happy",
  "Doc",
  "Grumpy",
  "Dopey",
  "Bashful"
];

const dwarfCounter = new DwarfIterator(crossingDwarfs); 
```

Enter fullscreen mode Exit fullscreen mode

现在 Doc 可以使用迭代器来跟踪下一个应该调用谁，以及何时调用它们。他只是不停地喊着它告诉他的话，直到他们完成。

```
dwarfCounter.callNextDwarf();
// Sneezy, you may cross!
dwarfCounter.callNextDwarf();
// Sleepy, you may cross!
dwarfCounter.callNextDwarf();
// Happy, you may cross!
dwarfCounter.callNextDwarf();
// Doc, you may cross!
dwarfCounter.callNextDwarf();
// Grumpy, you may cross!
dwarfCounter.callNextDwarf();
// Dopey, you may cross!
dwarfCounter.callNextDwarf();
// Bashful, you may cross!
dwarfCounter.callNextDwarf();
// Everyone is here! 
```

Enter fullscreen mode Exit fullscreen mode

## 用调解人管理每个人的工作

矮人在矿井里，准备工作。有很多地方需要挖掘，每个矮人都移动到不同的地方。在不同的区域之间移动要花费很多时间，而且他们不希望一次在一个区域有一个以上的矮人，因为他们想要各种各样的石头。

这很难管理，因为如果一个矮人想要移动到另一个区域，他们不知道是否有人已经在那里了。Doc 看到了这个问题，作为一个解决问题的侏儒，他写了一个类来使自己成为一个调停者。

中介类类似于观察者的`Caller`类。它有一个矮人列表，但是它并没有提醒所有的矮人发生的变化，而是使用这些矮人的信息来更好地协调和指导他们的行动。T3】

```
class DwarfMediator {
  constructor() {
    this.dwarves = [];
  }

  checkSection(section) {
    return this.dwarves.every(dwarf => dwarf.section !== section);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，协调不同的小矮人采取了`checkSection`的形式。它检查是否有任何其他侏儒已经在那个部分，并分别返回`true`或`false`。这防止了他所协调的所有矮人之间的区域重叠。

Doc 还为他调解的小矮人写了一个类。每一个都需要一个中介，所以他确保每当一个工人被制造出来，它就把自己添加到一个中介的工人列表中。

```
class DwarfWorker {
  constructor(section, mediator) {
    this.section = section;
    this.mediator = mediator;
    this.mediator.dwarves.push(this);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在工作者可以参考中介者和它关于其他矮人的知识。它可以要求中介检查某个特定部分是否可用，如果可用，他们将移动到那里。

```
class DwarfWorker {
  constructor(section, mediator) {
    this.section = section;
    this.mediator = mediator;
    this.mediator.dwarves.push(this);
  }

  askToMove(section) {
    const available = this.mediator.checkSection(section);
    if (available) {
      this.section = section;
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这很好，因为该类没有与中介紧密耦合。仲裁者专注于组织每个工人(他们的部分)提供的信息，并尽可能多地将行动留给工人(转移到新的部分)。

我们可以看到这在行动中是多么有帮助。多克创造了一个实例，他自己是调停者，其他矮人是工人。

```
const MediatorDoc = new DwarfMediator();

const Sneezy = new DwarfWorker("Diamonds", MediatorDoc),
  Sleepy = new DwarfWorker("Rubies", MediatorDoc),
  Happy = new DwarfWorker("Sapphires", MediatorDoc),
  Grumpy = new DwarfWorker("Emeralds", MediatorDoc),
  Dopey = new DwarfWorker("Gems", MediatorDoc),
  Bashful = new DwarfWorker("Crystals", MediatorDoc); 
```

Enter fullscreen mode Exit fullscreen mode

假设喷嚏精想移到“红宝石”区。医生会看到瞌睡虫已经在那里，并阻止他。

```
Sneezy.askToMove("Rubies");
// Sneezy.position is still "Rubies!" 
```

Enter fullscreen mode Exit fullscreen mode

喷嚏精明白了他的意思，呆在原地，转而询问“珍珠”部分。医生看到门开着，允许他移动。

```
Sneezy.askToMove("Pearls");
// Sneezy.position has changed to "Pearls!" 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们有几个对象以一种有组织的方式一起工作，避免了紧密耦合。

## 用纪念品跟踪挖掘进度

随着矮人继续工作，他们意识到由于珠宝和钻石最近丢失了，他们需要更好地记录他们找到了多少。喷嚏精想记录他们每小时挖出多少，所以如果他们意识到一些消失了，他们可以检查他们每小时有多少，看看他们什么时候消失了。

喷嚏精决定为此建立一个纪念图案。这将让他跟踪变化，并检查更早的状态计数。

喷嚏精写道，纪念品模式有三个部分。首先是纪念品本身，它是收集到的岩石的“快照”。他会每小时再做一次。

```
class Memento {
  constructor(jewels, diamonds) {
    this.jewels = jewels;
    this.diamonds = diamonds;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

他还需要一个`originator`，这是一个额外的抽象层，直接处理纪念品。在这种情况下，Sleepy 只需要创建者创建新的纪念品并从旧的纪念品中提取数据。

```
const originator = {
  store: function(jewels, diamonds) {
    return new Memento(jewels, diamonds);
  },
  restore: function(memento) {
    return {
      jewels: memento.jewels,
      diamonds: memento.diamonds,
    };
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

这个模式的最后一部分是`caretaker`，它跟踪一个大组中的纪念品。它有添加和检索 Memento 的方法，但是它与 Memento 类本身没有直接联系——这留给了发起者。管理员用一些基本功能存储和管理它们。

```
class CaretakerDwarf {
  constructor() {
    this.mementos = [];
  }

  addMemento(memento) {
    this.mementos.push(memento);
  }

  getMemento(index) {
    return this.mementos[index];
  }

  getMementoFromHour(hour) {
    const hourIndex = hour - 1;
    return this.getMemento(hourIndex)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在喷嚏精有了他开始追踪纪念品所需的一切。首先，他指定自己为看守人。

```
const Sneezy = new CaretakerDwarf(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，他可以使用发起人添加新的纪念品，以跟踪每小时的珠宝和钻石。

```
Sneezy.addMemento(originator.store(2, 4));
Sneezy.addMemento(originator.store(3, 6));
Sneezy.addMemento(originator.store(0, 2)); 
```

Enter fullscreen mode Exit fullscreen mode

如果 Sneezy 需要查看第二个小时收集了多少，他可以快速还原那个小时的“状态”。它存储在一个变量中，他可以和其他人比较，或者做其他任何事情。

```
const secondHourResults = originator.restore(Sneezy.getMementoFromHour(2));
secondHourResults.jewels;   // 3
secondHourResults.diamonds; // 6 
```

Enter fullscreen mode Exit fullscreen mode

## 通过责任链装载珠宝

一天的工作结束了，矮人需要开始把他们的石头装上车。Happy 很乐意处理这个问题，但也意识到有很多事情需要考虑。

*   每辆车只能装有限数量的石头。没有侏儒可以有任何剩余，没有车可以溢出。
*   根据每个矮人所处的位置，有许多不同的路径可供大车行走。
*   一些矮人可能还在采矿，需要继续传递矿车。

唯一不变的快乐看到的是手推车从一个侏儒移动到另一个侏儒，但是每个侏儒采取的具体行动可能是不同的。这个谜题的核心是沿着矮人链传递一个手推车物体，他意识到责任链模式将解决这个问题。

首先，他为采矿车创建了一个类。每辆车都必须分配一个珠宝限额和一个简单的矮人名单。

```
class MiningCart {
  constructor(limit) {
    this.limit = limit;
    this.jewels = 0;
    this.dwarves = [];
  }

  setNextDwarf(dwarf) {
    this.dwarves.push(dwarf);
  }

  addJewels(jewels) {
    this.jewels += jewels;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

Happy 还添加了一些额外的方法，这样更容易计算出购物车有足够的空间来添加更多的珠宝。

```
class MiningCart {
  constructor(limit) {
    this.limit = limit;
    this.jewels = 0;
    this.dwarves = [];
  }

  setNextDwarf(dwarf) {
    this.dwarves.push(dwarf);
  }

  addJewels(jewels) {
    this.jewels += jewels;
  }

  getAvailableSpace() {
    return this.limit - this.jewels;
  }

  hasEnoughSpace(jewels) {
    return this.getAvailableSpace() - jewels >= 0;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

Happy 还需要一个为每个得到手推车的矮人设计的类，这个类可以跟踪他们是否在采矿以及他们拥有的珠宝。

```
class Dwarf {
  constructor(jewels, isMining) {
    this.jewels = jewels;
    this.isMining = isMining;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后也是最重要的一点，Happy 编写了一个类，它将沿着责任链向下发送购物车。它拿起推车，把它传给名单上的每个矮人，在试图把他们的珠宝加入推车之前，检查他们的采矿状态。这个更新后的购物车被传递给下一个矮人，以此类推，直到它完成这个链。这让手推车和矮人一起工作，以正确的顺序收集珠宝和做其他的操作，而不会让他们靠得太近。完成后的推车，装载了尽可能多的珠宝，最后被送回 Happy。

```
class CartChainOfResp {
  calc(cart) {
    cart.dwarves.forEach(dwarf => {
      const cartHasSpace = cart.hasEnoughSpace(dwarf.jewels),
            dwarfHasFinishedMining = !dwarf.isMining;

      if (dwarfHasFinishedMining && cartHasSpace) {
        cart.addJewels(dwarf.jewels);
      }
    });

    return cart;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看这个模式的运行情况。快乐看到矿井里有三个小矮人，他也能超过手推车。购物车限制为 100 件珠宝，所以 Happy 记下其他矮人的信息，并把它们分配到购物车中。

```
const miningCart = new MiningCart(100),
      Sneezy = new Dwarf(50, false),
      Doc = new Dwarf(25, true),
      Dopey = new Dwarf(50, false);

miningCart.setNextDwarf(Sneezy);
miningCart.setNextDwarf(Happy);
miningCart.setNextDwarf(Dopey); 
```

Enter fullscreen mode Exit fullscreen mode

小车和它的路径设置好了，它就准备好上链了。他创建了他的责任链的一个实例，并把它传递给采矿车，沿着链向上传递。

```
const cartChainOfResp = new CartChainOfResp();
let finishedCart = cartChainOfResp.calc(miningCart);

finishedCart.jewels;
// 100 
```

Enter fullscreen mode Exit fullscreen mode

推车从喷嚏精那里收集了 50 颗宝石，跳过了博士，因为他还在采矿，从笨蛋那里又收集了 50 颗，满载 100 颗宝石返回。

让我们稍微调整一下场景，增加推车的极限和矮人的数量。

```
const miningCart = new MiningCart(150),
      Sneezy = new Dwarf(50, false),
      Doc = new Dwarf(25, false),
      Dopey = new Dwarf(50, false),
      Sleepy = new Dwarf(30, false);

// Same code still here //

finishedCart.jewels;
// 125 
```

Enter fullscreen mode Exit fullscreen mode

手推车会从所有的矮人那里收集珠宝，跳过瞌睡虫，因为他会把手推车放得超过限制，然后带着 125 颗珠宝回来。

现在让我们调整这个场景，这样 Dopey 仍然在挖掘。这为瞌睡虫的宝石腾出了空间，但降低了购物车的总容量。

```
const miningCart = new MiningCart(150),
      Sneezy = new Dwarf(50, false),
      Doc = new Dwarf(25, false),
      Dopey = new Dwarf(50, true),
      Sleepy = new Dwarf(30, false);

// Same code still here //

finishedCart.jewels;
// 105 
```

Enter fullscreen mode Exit fullscreen mode

在所有这些场景中，我们看到责任链执行它的逻辑，不管矮人组成这个链。

## 设计模式城堡逼近

在这个解释性的童话故事中，只剩下一篇关于结构模式的文章。虽然这个系列写起来很有趣，但我也期待着拯救编程公主，并结束这个故事的回购。所以留下来看最后一个条目！

*未完待续...*

*[封面图片由 SafeBooru.org](https://safebooru.org/index.php?page=post&s=view&id=745684)提供*