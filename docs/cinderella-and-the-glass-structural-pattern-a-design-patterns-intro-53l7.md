# 灰姑娘和玻璃结构模式-设计模式介绍

> 原文：<https://dev.to/maxwell_dev/cinderella-and-the-glass-structural-pattern-a-design-patterns-intro-53l7>

欢迎来到我的“设计模式童话”系列的第四篇文章！它旨在用一种更容易理解的方式解释设计模式——也就是童话中的隐喻。

到目前为止，这个系列已经涵盖了创造模式(用于制造对象)和行为模式(使对象一起工作)，在最后一篇文章中，我们将以结构模式结束。这些定义了类和对象之间的特定关系，可以以可维护和有效的方式完成更艰巨的任务。

我为这些图案搭配的童话是另一个经典，灰姑娘。这篇文章复述了这个故事，用设计模式替换了几个元素，同时解释了它们是如何工作的。

如果你到目前为止一直在关注上涨，你就已经知道这是怎么回事了。所以让我们切入正题，这样我们就可以用我们的设计模式知识快乐地生活下去了！

## 挥舞魔棒打出门面

我们开始复述灰姑娘的故事，仙女教母在舞会前安慰心烦意乱的灰姑娘。她的礼服已经毁了，没办法参加了。所以 FG 用她的魔杖解决了这一切，送她上路。

然而，她很难找到她的魔杖并记住咒语。FG 几乎不记得所有复杂的魔法计算和简单悬浮物品的功能。她需要一个快速的方法来使用不同的类，这些类组成了一个简单的咒语。所以为了制作这个简化版，她写了一个 Facade。

```
class wandWaveFacade {
  constructor(target) {
    this.target = target;
  }

  BibbidiBobbidiBoo() {
    const neededElement = new getMagicElement("levitate"),
          neededEnergy = new calculateMagicEnergy(
            neededElement,
            this.target.weight
          ).calc();

    return new levitateItem(this.target, neededEnergy).levitate();
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

她以最容易记忆的部分——咒语本身——来命名她门面上的第一个功能。该函数调用以下所有类的实例来完成这项神奇的工作。

```
class getMagicElement {
  // Super secret magical secrets here, not for you!
}

class calculateMagicEnergy {
  // You don't have a wand so you can't make use of magic
}

class levitateItem {
  // Content censored as per paragraph 3 of article 17 in the International Treaty of Magical Movement and Teleportation
} 
```

Enter fullscreen mode Exit fullscreen mode

不，你看不到任何秘密的魔法代码。那是因为我发过誓要保密，而不是因为我懒。谢天谢地，我不写它符合门面的角色。它将复杂的魔法法则简化成简单的魔杖一挥，加上一些有趣的单词。FG 一次性完成所有工作，她所要做的就是使用下面写的内容来访问它。

```
const pumpkin = 'pumpkin',
      waveWand = new wandWaveFacade(pumpkin);

waveWand.BibbidiBobbidiBoo();
// The pumpkin is levitated! 
```

Enter fullscreen mode Exit fullscreen mode

假设她能找到她的魔杖并记住单词，FG 现在拥有了她所需要的一切，以一种简单直接的方式继续做各种各样的魔法。

## 用飞锤创造神奇的火花

*注意:我永远在轻量级模式中挣扎，所以万一我搞砸了，[阅读这篇文章](https://refactoring.guru/design-patterns/flyweight)。*

在魔杖挥舞过程中，FG 不能处理的一部分是魔杖的闪光。她知道，如果没有一根在魔法时间“闪闪发光”的魔杖，她就不会被认真视为通过无法解释的现象预测人们命运的魔法监督者。

然而，只要魔杖一挥，就会产生大量的火花。几乎成吨的火花。每个人只需要一小块魔法，但是在制造了这么多之后，它会减慢她的魔法速度，让她在几个咒语之后就用完了。因此，她需要正确数量的火花，而不是每一个都吸收通常的能量。

为了解决这个问题，她从制作火花的简单课程开始。

```
class Sparkle {
  constructor(type) {
    this.type = type;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，FG 制造了一个制造所有不同火花的工厂。她意识到最适合她需要的模式是轻量级模式。

```
class SparkleFlyweightFactory {
  constructor() {
    this.sparkles = {};
  }

  create(name) {
    let sparkle = this.sparkles[name];
    if (sparkle) return sparkle;
    this.sparkles[name] = new Sparkle(name);
    return this.sparkles[name];
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

让我们来分析一下这个 Flyweight 工厂是如何工作的，以及它是如何解决这个问题的。

1.  当创建一个实例时，工厂从一个空的`sparkles`对象开始存储所有的火花。
2.  当想要制造更多火花时，使用`create`方法。它做的第一件事是看看我们是否已经用相同的名字做了一个闪光点。如果是这样，它返回那个而不创建新的。
3.  如果它以前没有产生过这种类型的火花，它会产生一个新的火花，将其添加到对象中，然后返回它。

现在，FG 可以制造她想要的所有火花，任何复制品都将返回已经存在的东西。这本身就节省了大量的能量，但是轻量级模式做得更多。

为了看看它还做了什么，让我们看看 FG 用来制造火花的魔杖类。

```
class Wand {
  constructor(sparkleFactory) {
    this.sparkleFactory = sparkleFactory;
  }

  addSparkle(sparkle) {
    return this.sparkleFactory.create(sparkle);
  }

  getAllSparkles() {
    return this.sparkleFactory.sparkles;
  }

  bigSparkle(sparkleName) {
    const sparkle = this.sparkleFactory.create(sparkleName);
    return `This is a giant ${sparkle.type} sparkle!`;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

大多数都很容易理解，因为前两个函数是关于添加火花和通过 Flyweight 工厂获得完整列表的。但是 FG 为什么要把`bigSparkle`方法放在魔杖里呢？它在`Sparkle`或`SparkleFlyweightFactory`类中同样有效，对吗？

事实证明，制造更大的火花需要相应更大的魔法来保持这一功能。在每个闪闪发光的物体上重复这个功能需要大量的魔法，她又回到了同样的问题上！所以为了解决这个问题，她把这个功能移到了魔杖上。现在，该功能只需要创建一次，并在魔杖上调用所需的闪光。节省了大量的魔法，避免了同样的问题。

从本质上讲，这就是 Flyweight 要解决的问题:**用尽可能少的能量制造和管理大量的对象。**这里的物体(火花)仅由自身独有的属性组成，如火花类型。这是 flyweight 对象的*固有状态。对于每个 spark 来说，任何相同的东西，比如调用大 spark 的函数，都在一个对象中共享，这个对象只被调用一次来管理它们。内在状态(想要)之外的一切都是外在的*状态。**

当 FG 使用她的魔杖并开始为她的法术制造火花时，我们可以看到它的好处。

```
const WandSparks     = new SparkleFlyweightFactory(),
      GodmothersWand = new Wand(WandSparks);

GodmothersWand.addSparkle("bright red");
GodmothersWand.addSparkle("silver");
GodmothersWand.addSparkle("dull blue");
GodmothersWand.addSparkle("diamond shine");

GodmothersWand.addSparkle("bright red");
GodmothersWand.addSparkle("silver");
GodmothersWand.addSparkle("dull blue");
GodmothersWand.addSparkle("diamond shine"); 
```

Enter fullscreen mode Exit fullscreen mode

通常你会期望魔杖中有八个不同的火花物体，但 Flyweight 只返回她制作的四个独特的物体。

```
GodmothersWand.getAllSparkles();

// { 'bright red'    : Sparkle { type: 'bright red' },
//   'silver'        : Sparkle { type: 'silver' },
//   'dull blue'     : Sparkle { type: 'dull blue' },
//   'diamond shine' : Sparkle { type: 'diamond shine' } } 
```

Enter fullscreen mode Exit fullscreen mode

此外，她可以使用一个更大的功能，如制造一个大的火花，而不会占用太多的能量。

```
GodmothersWand.bigSparkle("diamond shine")
// This is a giant diamond shine sparkle! 
```

Enter fullscreen mode Exit fullscreen mode

随着火花在行动中显示出她的魔法有多认真，FG 准备把南瓜变成一辆马车。

## 用油漆工制作南瓜马车

FG 看到山那边有一个南瓜，意识到它非常适合做马车。通常她会编写一个类来从头开始制作南瓜马车，但是为了节省魔法能量，她想从现有的南瓜对象开始工作。

```
class Pumpkin {
  constructor() {
    this.size = 3;
    this.color = "#FFA500"; // Orange
    this.vines = 4;
  }
}

const pumpkin = new Pumpkin(); 
```

Enter fullscreen mode Exit fullscreen mode

这意味着她必须动态地改变当前的南瓜。有些属性她会调整，有些她会添加，有些她会完全改变。为此，她编写了一个 Decorator，并将南瓜传递给它。

```
class PumpkinCarriageDecorator extends Pumpkin {
  constructor(pumpkin) {
    super(pumpkin);
    this.size = this.size * 4;
    this.color = "#fffcf0"; // Marble white
    this.wheels = this.vines;
    this.vines = 0;
  }

  isRidable() {
    return this.size > 10;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

Decorators 非常适合 his，因为它们是为通过动态扩展来添加和改变对象的功能和能力而构建的，而不是从头开始创建一个新对象。

现在她创建了一个装饰器，传入南瓜，然后神奇地把它变成了一辆南瓜马车！

```
const pumpkinCarriage = new PumpkinCarriageDecorator(pumpkin);

pumpkinCarriage;
// { size: 12, color: '#fffcf0', vines: 0, wheels: 4 }
pumpkinCarriage.isRidable();
// true 
```

Enter fullscreen mode Exit fullscreen mode

## 用代理人让客人进入舞会

灰姑娘穿着她的新衣服，闪亮的马车来到舞会，她的随从动物变成了人，还有一张神奇的请柬。当她在城堡前停下时，她看到城堡的官员正在用一个类来制作他们的新客人的实例。这种情况发生在她和同时到达的另外两个人身上。

```
class Guest {
  constructor(name, invitation, gender) {
    this.name = name;
    this.hasInvitation = invitation;
    this.gender = gender;
  }
}

const partyCrasher = new Guest("Rachel", false, "female"),
      maleGuest = new Guest("Jeff", true, "male"),
      cinderella = new Guest("Cinderella", true, "female"); 
```

Enter fullscreen mode Exit fullscreen mode

所以当灰姑娘到达时，她看到这个类获取了关于她的名字、邀请和性别的信息。然而，就在经过城堡入口时，她看到那些进入的人变成了聚会客人的实例，而不仅仅是普通的城堡客人。灰姑娘知道她需要成为派对的客人，因为他们有很多额外的派对方法。

```
class PartyGuest {
  constructor(name) {
    this.name = name;
  }

  declare() {
    return `I, ${this.name}, am at the party!`;
  }

  meetRoyals() {}
  lookForPrince() {}
  eatHorsDoeuvres() {}
} 
```

Enter fullscreen mode Exit fullscreen mode

当她和另外两个客人接近入口时，她看到另一个皇室成员对他们进行了另一次阶级斗争。灰姑娘的心怦怦直跳，以为自己成了客人，但看到这是另一个名为代理的类，她感到不安和困惑。

```
class PartyProxy {
  constructor(guest) {
    this.guest = guest;
  }

  canAttend() {
    const isFemale = this.guest.gender === "female";
    return isFemale && this.guest.hasInvitation
      ? new PartyGuest(this.guest.name)
      : "Sorry, you cannot attend";
  }
}

const partyCrasherGuest = new PartyProxy(partyCrasher),
      malePartyGuest = new PartyProxy(maleGuest),
      cinderellaGuest = new PartyProxy(cinderella); 
```

Enter fullscreen mode Exit fullscreen mode

灰姑娘问皇室成员他们在做什么，到处乱走，对某个人进行阶级歧视。皇室解释说，进入派对是非常排外的，所以他们必须确保只有符合所有要求的客人才是真正的客人。这就是为什么它们被做成**一个代理对象，充当占位符并确保基于它们的需求限制某些对象的创建。它基本上是一个保存资源的看门人。**

所以当灰姑娘和另外两个客人作为代理接近时，守卫调用`canAttend`方法来检查客人的性别和邀请。如果他们不符合条件，门口的皇室成员会拒绝客人，而不会浪费一个`PartyGuest`实例。

```
partyCrasherGuest.canAttend();
// Sorry, you cannot attend
malePartyGuest.canAttend();
// Sorry, you cannot attend 
```

Enter fullscreen mode Exit fullscreen mode

但是如果他们通过了，它会为他们创建并返回一个`PartyGuest`,这样他们就可以做派对客人做的所有事情。

```
cinderellaGuest.canAttend();
// PartyGuest { name: 'Cinderella' } 
```

Enter fullscreen mode Exit fullscreen mode

灰姑娘过去了，终于进入了舞会！

## 让灰姑娘与桥共舞

当灰姑娘进入舞会时，王子正在和出席者交谈并决定和谁跳舞。他用一门课让自己成为领舞，找出哪些女人想跳舞。

然而，他想问的女人太多了，他不能一个一个地问。王子想问所有的女孩，而不是每次都写一个新的类，所以他使用了一个叫做桥的设计模式。

首先，他为自己编写了一个带舞伴的类，看看他是否能和他们一起跳舞。

```
class LeadDancerBridge {
  constructor(dancePartner) {
    this.dancePartner = dancePartner;
  }

  canYouDance() {
    return this.dancePartner.canDanceWithPrince()
      ? "Shall we dance?"
      : "Please enjoy the party!";
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

每个舞伴在某些方面会有所不同，所以这个类充当了一个桥梁，因为它让舞伴的任何实例“过桥”到这个类，以查看它们是否兼容。

然后他为舞伴们自己写了一个类，它的实例将是过桥。它需要一些关于女人的基本信息，一旦她们“过桥”到`LeadDancerBridge`类实例，王子就会知道他是否能和她们跳舞。

```
class DancePartner {
  constructor(name, likesPrince, wantsToDance) {
    this.name = name;
    this.likesPrince = likesPrince;
    this.wantsToDance = wantsToDance;
  }

  canDanceWithPrince() {
    return this.likesPrince && this.wantsToDance;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在几次失败的跳舞尝试和一些半心半意的舞蹈之后，王子终于见到了灰姑娘。他创建了一个她作为舞伴的实例，它试图“过桥”到`LeadDancerBridge`类实例。

```
const Cinderella = new DancePartner("Cinderella", true, true),
      PrinceAskingCinderella = new LeadDancerBridge(Cinderella);

PrinceAskingCinderella.canYouDance();
// "Shall we dance?" 
```

Enter fullscreen mode Exit fullscreen mode

灰姑娘设法“过桥”，这表明她想跳舞。他松了口气，终于有了一个舞伴，所以他问，他们一起度过了一个神奇的夜晚！

## 躲过一记复合球

可悲的是，我们知道故事是如何发展的。正当灰姑娘和王子要接吻时，午夜的钟声敲响了。灰姑娘逃走了，丢下她的水晶鞋，跳上了她的马车。

马尽可能快地拉着马车逃跑。但是卫兵自己去追，同样数量的人拉着一辆有卫兵的马车。当他们被追赶时，灰姑娘担心护卫马会追赶他们。所以她读了一遍组成马的代码，检查它们是否跑得足够快。

首先，她注意到每匹马都是一个基础类，包括它们的速度。

```
class Horse {
  constructor(speed) {
    this.speed = speed;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在它下面，她看到了一个叫做复合类的东西，起初她并不理解。它似乎接受了一个马对象的数组，并将这组马实例视为一个单独的巨大对象。

```
class HorseComposite {
  constructor() {
    this.horses = [];
  }

  add(horse) {
    this.horses.push(horse);
  }

  averageSpeed() {
    const speeds = this.horses.map(horse => horse.speed),
      total = speeds.reduce((previous, current) => (current += previous)),
      average = total / this.horses.length;

    return average;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

马车夫看到她在读这段代码，意识到她想做什么，因为没有比一场大型的马追逐赛更好的时间来配对程序了。他说**复合类是以树状模式排列的对象模式，其中任何对象都可以有任意数量的其他子对象，等等。**这是一个相对简单的树，其中每个`HorseComposite`可以有任意数量的马子对象，仅此而已，但它仍然是一个复合模式。

灰姑娘意识到这些课程可以用来确保她的马比卫兵跑得更快。她很快为自己的马制作了几个实例，将它们作为“分支”添加到复合“树”中，并找到它们的平均速度。

```
const Bert = new Horse(24),
      Luke = new Horse(25),
      Jaq = new Horse(20),
      Gus = new Horse(29);

const goodHorses = new HorseComposite();

goodHorses.add(Bert);
goodHorses.add(Luke);
goodHorses.add(Jaq);
goodHorses.add(Gus);

goodHorses.averageSpeed();
// 25.5 
```

Enter fullscreen mode Exit fullscreen mode

她输入警卫的马的相同信息，查看它们的平均速度，并进行比较以确保它们更快。

```
const Blaze = new Horse(20),
      Lazer = new Horse(26),
      Blazer = new Horse(21),
      Joe = new Horse(30);

const guardHorses = new HorseComposite();

guardHorses.add(Blaze);
guardHorses.add(Lazer);
guardHorses.add(Blazer);
guardHorses.add(David);

guardHorses.averageSpeed();
// 25.25

goodHorses.averageSpeed() > guardHorses.averageSpeed();
// true
// Cinderella escapes! 
```

Enter fullscreen mode Exit fullscreen mode

很接近，但是灰姑娘的马足够快。就在咒语解除之前，他们逃脱了守卫，藏到了树林里。

## 穿着带适配器的玻璃鞋

灰姑娘回到家中，听说王子正在王国旅行，寻找水晶鞋的主人。然而，她的继母把她锁在阁楼上，这样王子就不会见到她了。在这个过程中，灰姑娘跌倒扭伤了脚，使它肿胀。她吓坏了，知道她的脚增加的尺寸会阻止玻璃鞋适合。

知道在王子到来之前她没有多少时间了，她检查了自己的脚，发现了引起肿胀的原因。

```
class sprainedFoot {
  constructor(size) {
    this.size = size * 1.25;
  }
}

const sprainedFoot = new sprainedFoot(8); 
```

Enter fullscreen mode Exit fullscreen mode

看到肿胀的程度，她意识到她可以用适配器逆转这种效果。**她扭伤的脚无法与玻璃鞋的“接口”相适应，因此用适配器包裹它将调整她的脚，直到它们能够配合在一起。因此，她编写了她的适配器，这样就可以减小她的脚的尺寸。然后，她创建了一个实例，将她的鞋号降了下来。** 

```
class sprainedFootAdapter {
  constructor(foot) {
    this.foot = foot;
    this.size = this.foot.size * 0.8;
  }
}

const wrappedFoot = new sprainedFootAdapter(sprainedFoot); 
```

Enter fullscreen mode Exit fullscreen mode

带着这个她扭伤的脚的改编版本(姑且称之为绷带)，灰姑娘被她的啮齿动物朋友从阁楼上解救出来，冲下楼去。她看到了穿不下她继姐妹的脚的班级。

```
class GlassSlipper {
  constructor(foot) {
    this.foot = foot;
  }

  fitsOnFoot() {
    return this.foot.size === 8;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后灰姑娘用“适应”的脚穿上玻璃鞋。适配器使它们的尺寸相同，所以她的脚非常合适。

```
const glassSlipper = new GlassSlipper(wrappedFoot);
glassSlipper.fitsOnFoot();
// true! true! Happily ever after! 
```

Enter fullscreen mode Exit fullscreen mode

在一次会面和跳舞后，王子知道他找到了他的真爱，他们跑去准备一个一点也不太快的婚礼！

## 设计图案龙已被灭门

至此，这些设计模式的童话故事结束了。把它们都写出来是一个挑战，但也是一个享受，我很高兴我能坚持到底。我特别希望人们发现它们对理解设计模式以及它们如何帮助解决常见的编程问题很有用。

我要在这里分享的最后一课是我一开始就分享的。我在学习设计模式时经历了一段非常艰难的时间。写这个系列的一部分是强迫自己充分理解它们，并把它们融入我已经熟知的故事中。这是一种屡试不爽的心理技巧，通过将一些你已经知道的意想不到的事情与困难的事情结合起来来学习。新视角鼓励学习，奇怪的联想让知识更难忘记。

因此，如果这个系列对你很有效，那么在将来使用这个技巧来学习更多的东西吧！通过将它与巧克力工厂或巨型战斗机器人进行比较来了解 Ember。了解与邪恶的人工智能或魔法衣柜里的一些孩子挂钩的反应。使用任何最有效的方法，甚至可以在事后写下来:)

就这样，我结束了这个故事。从那以后，他们都幸福地编码了。

*[封面图片由 SafeBooru.org](https://safebooru.org/index.php?page=post&s=view&id=2510905)提供*