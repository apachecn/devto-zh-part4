# 面向制陶者的 Scala:泛型

> 原文：<https://dev.to/shethiakamya/generics-scala-for-the-potterhead-30d8>

# 仿制药

在深入泛型之前，让我们先讨论一些更温和的东西...比如魂器。

魂器是一种用来储存一个人灵魂的魔法物品。创造一个魔法需要所有魔法中最黑暗的，所以伏地魔当然创造了 7 个。并不是所有的魔法物品都是邪恶的，比如记忆球，一个当主人忘记什么东西时会变红的小球。

让我们用 Scala 来表示一些神奇的对象:

```
class MagicalObject {
  val isDangerous: Boolean = false;
}

//When all 3 hallows are owned by one wizard, the owner is said to have mastery over death
class DeathlyHallows extends MagicalObject {
  override val isDangerous: Boolean = true;
}
//The smoke contained inside the remembrall turns red if it's owner has forgotten something
class Remembrall extends MagicalObject
//An object containing a piece of a human soul
class Horcrux extends MagicalObject {
  var isDestroyed = false
  override val isDangerous: Boolean = true;
}

//A magical map of Hogwarts
class MaraudersMap extends MagicalObject 
```

一个古老的巫师故事，三兄弟的故事讲述了死亡圣器。有无敌魔杖，有能起死回生的石头，还有隐身衣！传说中，如果一个巫师同时拥有这三样东西，他或她就能完全掌控死亡。

让我们用 Scala 来表示这个

```
class InvisibilityCloak extends DeathlyHallows
class ResurrectionStone extends DeathlyHallows
class ElderWand extends DeathlyHallows {
  var owner = "Antioch Peverell"
} 
```

你永远也不会听到霍格沃茨的教授提到魂器，任何关于魂器的参考资料都被图书馆禁止了。伏地魔将一些有意义的物品变成了他灵魂的宿主。

```
//Diadem (tiara) of one of the founders of Hogwarts: Rowena Ravenclaw
class RavenclawDiadem extends Horcrux

//The high school diary of Tom Riddle aka Lord Voldemort
class RiddleDiary extends Horcrux

//The ring of Voldemort's maternal grandfather
class MarvoloGauntRing extends Horcrux 
```

让我们创建一个著名的哈利波特拥有的魔法物品的列表。下面是一些这样做的 scala 代码:

```
val magicalObjectsOwnedByHarry: List[MagicalObject] = List(elderWand, maraudersMap, riddleDiary, invisibilityCloak) 
```

让我们放大变量`magicalObjectsOwnedByHarry`的类型。这是一个接受 MagicalObject 类型元素的列表。Scala 将 List 类型实现为一个泛型类，这允许我们这样做。

让我们确认 scala 编译器对我们的代码满意:

```
scala> val magicalObjectsOwnedByHarry: List[MagicalObject] = List(elderWand, maraudersMap, riddleDiary, invisibilityCloak)

magicalObjectsOwnedByHarry: List[MagicalObject] = List(ElderWand@66032b8d, MaraudersMap@1fb66050, RiddleDiary@28068327, InvisibilityCloak@65c2610f) 
```

泛型有时被称为参数多态性。它帮助我们编写更具表现力的代码，并在不损害类型安全的情况下促进代码的可重用性。当我们使用泛型时，scala 编译器将能够在编译时检测到任何意外的类型错误。

在我们的例子中，如果我们试图插入一个非语法项，scala 编译器会立即提醒我们代码中有错误。

```
scala> val magicalObjectsOwnedByHarry: List[MagicalObject] = List(elderWand, maraudersMap, riddleDiary, invisibilityCloak, "Non-magical kettle")
                                                                                                                           ^
       error: type mismatch;
        found   : String("Non-magical kettle")
        required: MagicalObject 
```

您可能会试图通过完全移除该类型来解决编译错误。虽然这可能会使我们的代码编译，但它可能会失败，并出现一个更难调试的错误。

考虑下面的片段，我们试图找到哈利拥有的所有危险物品。

```
scala> scala> val magicalObjectsOwnedByHarryWithoutGenericType = List(elderWand, maraudersMap, riddleDiary, invisibilityCloak, "Non-magical kettle")
magicalObjectsOwnedByHarryWithoutGenericType: List[Object] = List(ElderWand@772357e9, MaraudersMap@22ccd80f, RiddleDiary@45b7b254, InvisibilityCloak@3b47178c, Non-magical kettle)

scala> val dangerousObjects = magicalObjectsOwnedByHarryWithoutStrongTypes.filter(magicObject => magicObject.isDangerous)
                                                                                                             ^
       error: value isDangerous is not a member of Object

scala 
```

我们的代码由于一个更难理解的错误而失败，这与我们之前看到的简单类型的错误相反。

* * *

*   你可以点击查看完整的代码示例
*   你可以在官方的 [scaladoc](https://docs.scala-lang.org/tour/generic-classes.html) 中阅读更多关于泛型类的内容
*   这个奇妙的维基百科条目是一本有趣的关于魔法物品的读物