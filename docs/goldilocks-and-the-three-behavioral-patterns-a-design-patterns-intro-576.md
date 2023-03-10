# 金发女孩和三种行为模式——设计模式介绍

> 原文：<https://dev.to/maxwell_dev/goldilocks-and-the-three-behavioral-patterns-a-design-patterns-intro-576>

欢迎来到我的“设计模式童话”系列的第二篇文章！

本系列旨在为新程序员或没有计算机科学背景的人提供设计模式的基本介绍。这些是对常见编程问题的既定解决方案，了解它们非常有用。然而，我很难找到解释他们不淹没在行话。本系列旨在通过在我们都知道的背景下解释它们来解决这个问题——童话！

这篇文章将我们的幻想冒险从创造转移到行为模式。虽然创造模式是制造新对象的方法，但行为模式是使多个对象积极合作完成复杂任务的方法。实际上有比这三组中的任何一组都多的行为模式，所以我会写两篇文章来讨论它们。

在这里，我将用一个聚焦于主角不良行为的童话来讲述第一批行为模式:*金发姑娘和三只熊！*

## 根据策略准备好自行车

我们版本的《金发姑娘》开头与原版相似。三只熊(熊爸爸、熊妈妈和熊宝宝)煮了一些粥，放在外面冷却。但是在这里，我们假设他们在等待的时候骑着自行车去兜风。更有趣，对吗？

熊找到了他们的自行车，并意识到他们的新模型让他们改变了自行车的阻力。这让他们自己的旅行变得更难或更容易。熊检查组成他们的自行车的类。

```
class BearBike {
  constructor(weight, strategy) {
    this.weight = weight;
    this.strategy = strategy;
  }

  setWeight(weight) { this.weight = weight; }

  getResistance() {
    return this.strategy(this.weight);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

`BearBike`类接受熊的重量来决定阻力。然而，实际的算法并不存在。它传递给自行车，以获得阻力作为一个`strategy`。

他们意识到这是策略设计模式，其中所需的算法与对象分离开来并被传入。这让熊在计算自行车阻力时有了更大的控制权，让他们单独编写和管理算法。它们可以用在这里，也可以用在任何他们想用的课堂上！

熊写了一些函数来得到他们想要的阻力。熊爸爸想为他的新饮食计划增加阻力。熊妈妈想要的更少，因为她跑了一大段路后感到酸痛。熊宝宝想要的更少，因为他还在成长，而且因为他带了额外的旅行装备，所以多拿了一大块。

```
const PapaBearStrategy = (weight) => {
  return weight * 1.25;
}

const MamaBearStrategy = (weight) => {
  return weight * 0.8;
}

const BabyBearStrategy = (weight) => {
  return (weight * 0.6) - 20;
} 
```

Enter fullscreen mode Exit fullscreen mode

熊现在需要做的就是用它们各自的权重和策略创建类实例。自行车将使用每种策略给每只熊所需的阻力。

```
const PapaBike = new BearBike(150, PapaBearStrategy);
PapaBike.getResistance();
// 187.5

const MamaBike = new BearBike(120, MamaBearStrategy);
MamaBike.getResistance();
// 96

const BabyBike = new BearBike(75, BabyBearStrategy);
BabyBike.getResistance();
// 45 
```

Enter fullscreen mode Exit fullscreen mode

如果愿意，这些熊可以使用更复杂的算法，引入其他类来根据它们吃的东西、它们的减肥目标，甚至热量和湿度来计算阻力。所有这些逻辑都可以被带入他们的策略中，但是因为它只是传递给自行车，所以没有什么会有重叠太多的风险，所以他们会互相破坏。通常，松散耦合和可维护性是设计模式的额外好处。

## 与翻译沟通行程

准备好新自行车后，熊们正沿着一条路线骑行。熊爸爸在前面，让他的 GPS 引导他们穿过树林到海滩。这些熊以前从来没有骑自行车去过海滩，所以他们很大程度上依靠 GPS 来到达那里。

问题是全球定位系统没有向一群人宣布方向的选项。爸爸可以继续检查它，并大声告诉它去哪里，但上次他这样做时，他分心了，崩溃了。所以他写了一个程序，让 GPS 为他们宣布新的方向。

但是全球定位系统有大量的数据在流动，很难全部弄清楚。熊爸爸通过根据他想要的语言将他的程序分解成小部分来处理这个问题。他使用解释器设计模式，因为**让他设计对象，并使它们基于代码所做的语言元素一起工作。**

这有点难以理解(我自己还在努力)，所以最好能在实践中看到。

熊爸爸想让全球定位系统喊出这样的句子:“把 X 转到 Y 码左右！”第一部分是“转弯”，所以他写了一个定义转弯的类。GPS 只能看到度数，但熊爸爸想让它成为基本的左转、右转或直行。所以他用一个`interpret`函数来设置他的`Turn`类，这个函数为他完成了这项工作。

```
class Turn {
  constructor(degrees) {
    this.degrees = degrees;
  }

  interpret() {
    if (0 < this.degrees && this.degrees < 135) { return 'right'; }
    if (225 < this.degrees && this.degrees < 360) { return 'left'; }

    return 'straight';
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

他走向远方。这个问题比较棘手，因为 GPS 获取距离的方式有两个奇怪之处:

1.  GPS 测量距离的单位是米，而熊习惯于码。公平地说，GPS 是加拿大制造的。
2.  GPS 找不到下一个转弯的准确距离。它得到一个最小和最大的距离，熊需要找到它们之间的距离。

熊爸爸一次解决一个问题。首先，他编写了一个定义基本单位`Distance`的类，将米转换成码。

```
class Distance {
  constructor(meters) {
    this.meters = meters;
  }

  interpret() {
    return this.meters * 1.09361;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，他编写一个`AverageDistance`类，该类接受两个`Distance`实例，并获取它们之间的平均距离。

```
class AverageDistance {
  constructor(start, end) {
    this.start = start;
    this.end = end;
  }

  interpret() {
    const difference = (this.end.interpret() - this.start.interpret()) / 2;
    return Math.round(this.start.interpret() + difference);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

熊爸爸现在需要的是一个类，它需要一段距离和一个转弯，并创建一个 GPS 可以向他的家人喊出的句子。

```
class CallOut {
  constructor(distance, direction) {
    this.distance = distance;
    this.direction = direction;
  }

  interpret() {
    return `Go ${this.direction.interpret()} in around ${this.distance.interpret()} yards!`;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这些都可以放在一起如下图。由于我们已经定义了解释更抽象的数据的语法`distance`和`turn`,所以语法很有表现力，也很容易理解。它读起来不像一个程序，更像一个句子，每个对象都是其中的一个单词。

```
new CallOut(new AverageDistance(new Distance(minDistance), new Distance(maxDistance)), new Turn(turn)) 
```

Enter fullscreen mode Exit fullscreen mode

您可以在下面看到这些解释器对象的运行。

```
const turn1 = new CallOut(new AverageDistance(new Distance(100), new Distance(140)), new Turn(38));
turn1.interpret();
// Go right in around 131 yards!

const turn2 = new CallOut(new AverageDistance(new Distance(20), new Distance(50)), new Turn(340));
turn2.interpret();
// Go left in around 38 yards!

const turn3 = new CallOut(new AverageDistance(new Distance(85), new Distance(107)), new Turn(170));
turn3.interpret();
// Go straight in around 105 yards! 
```

Enter fullscreen mode Exit fullscreen mode

这也使得熊爸爸更容易跟随，因为现在熊可以安全准确地沿着路线前进了！

## 和客人一起让粥凉了

让我们来看看童话中的另一个角色，金发姑娘！她在森林里游荡，看到一个陌生人的家没有锁门，她就像任何正常人一样，跳着华尔兹进去吃他们的食物。

金发姑娘看到了三碗粥，但是每碗都太热了，不能吃。她是如此急于入侵他们的家，没有人有足够的时间冷静！她想等到每个都刚刚好，但不知道要等多久。她查看了每碗粥的代码，但她对所看到的内容感到惊讶。

```
class Porridge {
  constructor(weight) {
    this.weight = weight;
    this.coolingTime = 0;
  }

  getCoolingTime() {
    return this.coolingTime;
  }

  accept(visitor) {
    visitor(this);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

冷却时间为 0，所以按照`Porridge`类，已经可以吃了。这说不通啊！她还看到该类允许一个`visitor`。她不确定这是什么意思，所以她在厨房里寻找线索。

最终，她看到了一个藏在餐具室里的功能，名字里有“访客”。它包括设定食物冷却时间的逻辑。

```
const cooldownVisitor = (food) => {
  if (food.weight > 15) {
    food.coolingTime = 20;
  } else if (food.weight < 6) {
    food.coolingTime = 5;
  } else {
    food.coolingTime = 10;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

金发女孩看到当`Porridge`接受这位访客时，它会根据食物的重量根据需要改变冷却时间。这是可行的，但她仍然很困惑。为什么不把这个逻辑直接写在类中，而不是写在这个函数中呢？她最终看到了另一个类似于`Porridge`的班级。利用`cooldownVisitor`也有类似的性质和方法。

```
class Coffee {
  constructor(weight) {
    this.weight = weight;
    this.coolingTime = 0;
  }

  restOnCounter() {
    return this.coolingTime;
  }

  addSuger(cubes) {
    console.log(`Let us add ${cubes} cubes of sugar!`);
  }

  accept(visitor) {
    visitor(this);
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这有助于 Goldilocks 看到这种访问者模式的好处。冷却逻辑需要在多个地方使用，但是让两个完全不同的职业用这种逻辑扩展一个职业是不切实际的。访问者提供了一个更易于管理的选项，将这个逻辑分离成其他对象并传递给它们。现在它们可以用于不同的领域，而不会损害其他类的简单性或可维护性。

使用这个访问者，Goldilocks 可以根据他们的体重创建`Porridge`实例，添加访问者，并获得冷却时间。

```
const PapasPorridge = new Porridge(16),
      MamasPorridge = new Porridge(12),
      BabysPorridge = new Porridge(5);

PapasPorridge.accept(cooldownVisitor);
MamasPorridge.accept(cooldownVisitor);
BabysPorridge.accept(cooldownVisitor);

PapasPorridge.getCoolingTime();
// 20
MamasPorridge.getCoolingTime();
// 10
BabysPorridge.getCoolingTime();
// 5 
```

Enter fullscreen mode Exit fullscreen mode

虽然没有咖啡出来，她可以想象查找咖啡的冷却时间是什么样子。

```
const PapasCoffee = new Coffee(14);
PapasCoffee.accept(cooldownVisitor);
PapasCoffee.restOnCounter(); 
```

Enter fullscreen mode Exit fullscreen mode

随着冷却逻辑的加入，她准备好在陌生人的房子里吃食物，不管里面有什么！

## 品尝着粥与粥

粥凉了，金发姑娘可以吃了。尽管她很饿，但她也想在陌生人家里睡一会儿，因为为什么不呢？

金发姑娘想知道当她吃粥的时候，她的身体什么时候准备好睡觉。她知道自己要采取的行动——准备好粥，吃粥，吃完。但是在访问者模式扔给她的循环之后，她想清楚地知道她什么时候准备好。她查阅一些管理自己状态的代码。

**一个对象的状态控制着它能或不能做什么动作，并在状态改变时调整这些动作。**金发姑娘可能处于不允许她睡觉的`hungry`状态，需要先达到`full`状态。她的状态决定了她是否能完成重要的事情，所以知道那是什么很重要。

金发姑娘从基础类开始，她的其他状态都是建立在基础类之上的。它确保所有状态都可以访问当前状态、下一个状态，并且可以移动到下一个状态。

```
class PersonState {
  constructor(state, nextState) {
    this.state = state;
    this.nextState = nextState;
  }

  next() {
    return new this.nextState();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

她也检查了她的三个不同的州。读了一遍，她发现她的状态会影响她的想法，是否能跑，是否能睡。她还从`super()`论证中看到了各自的下一个状态是什么。例如，从`HungryState`继续前进会将其更改为`EatingState`。

```
class HungryState extends PersonState {
  constructor() {
    super('Hungry', EatingState);
    this.thoughts = "I'm hungry!";
    this.canRun = true;
    this.canSleep = false;
  }
}

class EatingState extends PersonState {
  constructor() {
    super('Eating', FullState);
    this.thoughts = "This is tasty!";
    this.canRun = false;
    this.canSleep = false;
  }
}

class FullState extends PersonState {
  constructor() {
    super('Full', HungryState);
    this.thoughts = "So full! I want to take a nap.";
    this.canRun = false;
    this.canSleep = true;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

最后，也是最重要的，Goldilocks 检查组成她自己的`Person`类。

```
class Person {
  constructor() {
    this.state = new HungryState();
  }

  sleep() {
    if (this.state.canSleep) {
      return 'Time to find a bed! Zzzzz...';
    } else {
      return 'I can\'t sleep until I\'ve eaten!'
    }
  }

  nextState() {
    this.state = this.state.next();
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

`sleep`功能取决于当前状态是否允许人睡觉。金发姑娘需要改变状态，让她睡觉。她将使用`nextState`函数来实现这一点，该函数将移动到下一个状态。

考虑到这一点，金发姑娘坐下来吃一些粥，并根据自己的状态检查自己的行动。

```
const Goldilocks = new Person();

Goldilocks.state.thoughts;
// I'm hungry!
Goldilocks.sleep();
// I can't sleep until I've eaten! 
```

Enter fullscreen mode Exit fullscreen mode

她在`HungryState`里，所以她开始吃东西。这触发了`nextState()`并将她移动到`EatingState`。她的思想会随着状态而改变，但她的睡眠能力不会。

```
Goldilocks.nextState();
Goldilocks.state.thoughts;
// This is tasty!
Goldilocks.sleep();
// I can't sleep until I've eaten! 
```

Enter fullscreen mode Exit fullscreen mode

一旦金发姑娘吃完了，她终于到达了`FullState`，这改变了她的想法和睡眠动作。

```
Goldilocks.nextState();
Goldilocks.state.thoughts;
// So full! I want to take a nap.
Goldilocks.sleep();
// Time to find a bed! Zzzzz... 
```

Enter fullscreen mode Exit fullscreen mode

管理这些状态变化是困难的，但它们确保了金发姑娘不会在她不应该采取行动的时候采取行动。这就是为什么对于程序来说，只有当它们能够(或者应该)采取行动时，状态才是至关重要的。

还要注意，状态并不总是以一种可预测的、有序的方式变化。它通常依赖于接收到的数据或用户输入，这可能更加不稳定。这通常会使状态与准确的数据和视图层保持同步成为一项艰难但至关重要的任务。

但是金发姑娘已经控制住了局面。吃饱后，她继续小睡。

## 用模板测量返回路径

回到树林里，熊们已经走到了路的尽头，想要往回走。熊爸爸查看他的地图，发现有三条路线可以回家。

1.  山谷小路是最简单的返回路线，但是有一个陡坡。
2.  河边的小路比较容易倾斜，但是过桥会增加距离。
3.  街道是最平坦的，但是车辆的移动增加了距离。

另外，无论他们走什么路线，他们都需要绕过一个会增加 10 码的事故点。

有很多计算，虽然有些对于每次旅行都是相同的，但其余的对于每条路线都是不同的。这是一个很难妥善管理的组合。

熊爸爸意识到模板模式是处理这一问题的最佳方式。模板定义了算法的骨架，因此子类可以根据需要改变步骤。这与策略模式不同，后者根据需要交换算法集。这种模式允许您更改算法本身的部分内容。

首先，熊爸爸创建起始模板。它增加了每条路线所需的额外 10 码。

```
class DistanceTemplate {
  getDistanceHome = (distance) => this.routeDistance(distance) + 10;
} 
```

Enter fullscreen mode Exit fullscreen mode

他包含了一个`routeDistance`函数，但是没有在这里定义它。这是因为每个子类都将自己定义它，以便对算法进行必要的修改。

首先，我们有山谷路线。它需要骑自行车的距离乘以它的陡坡，所以这很简单。这部分算法必须在`routeDistance`函数中。

```
class ValleyDistance extends DistanceTemplate {
  constructor() {
    super();
    this.incline = 1.7;
  }

  routeDistance = (distance) => distance * 1.7;
} 
```

Enter fullscreen mode Exit fullscreen mode

接下来是河边路线。它会计算坡度，并为过桥增加额外的 20 码。

```
class RiversideDistance extends DistanceTemplate {
  constructor() {
    super();
    this.incline = 1.4;
    this.bridge = 20;
  }

  routeDistance = (distance) => (distance * 1.4) + this.bridge;
} 
```

Enter fullscreen mode Exit fullscreen mode

最后是街道路线。这是最复杂的，考虑到它的轻微倾斜和绕过汽车。模板模式允许它添加额外的类方法以在`routeDistance`中使用。

```
class StreetDistance extends DistanceTemplate {
  constructor() { super(); }

  carsDistance = (distance) => Math.round(distance / 5);

  inclineDistance = (distance) => distance * 1.05;

  routeDistance = (distance) => this.inclineDistance(distance) + this.carsDistance(distance);
} 
```

Enter fullscreen mode Exit fullscreen mode

写好所有路线后，熊爸爸需要做的就是创建类实例并计算回家的最佳路线。每个都返回基于起始模板构建的完整算法。

```
const fromValley = new ValleyDistance();
fromValley.getDistanceHome(100);
// 180

const fromRiverside = new RiversideDistance();
fromRiverside.getDistanceHome(100);
// 170

const fromStreet = new StreetDistance();
fromStreet.getDistanceHome(100);
// 135 
```

Enter fullscreen mode Exit fullscreen mode

看起来街道是最好的方式！模板模式让 Papa Bear 管理每条路线的复杂性，同时不重复代码并保持每个对象的松散耦合。

## 用命令唤醒金发女孩

当熊回家时，金发姑娘准备睡觉了。但是可以理解的是，她担心房子的主人会不高兴，因为一个陌生人吃了他们的食物，睡了他们的床。如果他们把她吓跑了怎么办？她需要留下一个更好的唤醒她的方法。

金发姑娘随身带着一个报警器，但她担心房子的主人不知道如何使用它，甚至不知道它是什么。她决定把如何使用她的闹钟写进命令里。命令模式将关于一个动作的所有东西保存到一个单独的对象中，因此任何使用它的东西都不需要理解动作本身。他们只需要得到对象，运行`execute`函数，剩下的就交给 set 动作了。

睡觉前，金发姑娘检查了一下班上的闹钟。

```
class Alarm {
  constructor() {
    this.ringing = false;
    this.volume = 0;
  }

  quietAlarm() {
    this.volume = 30;
    this.ringing = true;
  }

  loudAlarm() {
    this.volume = 100;
    this.ringing = true;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果有人来了，她会希望他们激活`quietAlarm`功能。因此，她编写了一个命令类，它接收一个报警实例，并在提示时执行`quietAlarm`。

```
class WakeCommand {
  constructor(alarm) {
    this.alarm = alarm;
  }

  execute() {
    this.alarm.quietAlarm();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

Goldilocks 现在需要做的就是创建闹钟实例，并将其附加到她的命令实例上。

```
const alarmClock = new Alarm(),
      wakeGoldilocksCommand = new WakeCommand(alarmClock); 
```

Enter fullscreen mode Exit fullscreen mode

金发姑娘把这个东西放在身边，然后去睡觉了。

过了一会儿，三只熊到家了。他们生气他们的粥被吃了，更生气的是有人睡在他们的一张床上！熊爸爸正准备咆哮，但注意到女孩旁边的`wakeGoldilocksCommand`。熊爸爸不知道它是做什么的，但是因为它是一个命令模式，他不需要知道。具有所需动作的对象已经存储在其中，并将为他完成工作！

他很快编写了一个允许他使用这个命令的类。

```
class Bear {
  constructor(wakeCommand) {
    this.wakeCommand = wakeCommand;
  }

  wakeUpStranger() {
    this.wakeCommand.execute()
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，他创建这个类的一个实例，传入他找到的`wakeUpGoldilocks`命令，并运行`wakeUpStranger`函数来使用该命令。

```
const PapaBear = new Bear(wakeUpGoldilocks);
PapaBear.wakeUpStranger(); 
```

Enter fullscreen mode Exit fullscreen mode

熊爸爸发现自己伸手去拿一个不熟悉的闹钟，并设置了一个安静的闹钟。金发姑娘被轻轻地从午睡中唤醒，看见了熊，尖叫着跑出了房子。熊耸耸肩，从此过上了幸福的生活。

## 设计模式探索继续

这篇文章标志着这个设计模式和童话故事系列的中点。在这个过程中，仍然存在剩余的行为和结构模式。但是很快我们将杀死设计模式龙，拯救编码公司城堡中的编程公主！

*未完待续...*

[封面图片由 SafeBooru.org 提供](https://safebooru.org/index.php?page=post&s=view&id=2692747)