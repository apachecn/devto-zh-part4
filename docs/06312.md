# Scala for the Potterhead:为了理解

> 原文：<https://dev.to/shethiakamya/scala-for-the-potterhead-for-comprehensions-2lma>

我们将在本教程中讨论理解、生成器表达式、守卫和让步。在我们开始之前，让我们先来谈谈魔杖。

每个人都知道好的魔杖是用最好的木头制作的，并且充满了一种魔法生物的标记。下面是一个用 Scala 表示的 wand 类:

```
class wand(typeOfWood: String, lengthOfWand: Long, magicalMaterial:String) {
  val wood = typeOfWood
  val length = lengthOfWand
  val magic = magicalMaterial

  override def toString(): String = {
    wood + " wand of length " + length + " inches with " + magic
  }
} 
```

注意，我们覆盖了`toString`方法。每当我们在一个对象上调用 print 函数时，Scala 都会询问对象的`toString`方法它应该如何显示到控制台上。让我们看看实际情况:

```
scala> val wandA = new wand("english wood", 6, "wampus cat hair")
wandA: wand = english wood wand of length 6 inches with wampus cat hair 
```

看起来不错！现在，让我们为 Olivander 商店中的更多魔杖创建一个目录。我们将它们存储在一个`List`中。

```
 val wandA = new wand("english wood", 6, "wampus cat hair")
val wandB = new wand("hazel", 9, "veela hair")
val wandC = new wand("Walnut", 11, "troll whisker")
val wandD = new wand("Cypress", 9, "kelpie hair")
val wandE = new wand("ash", 7, "phoenix feather")
val wandF = new wand("birch", 5, "unicorn tail hair")

val wands = List(wandA, wandB, wandC, wandD, wandE, wandF) 
```

现在，我们想为一个特别高的巫师找一根魔杖。我们知道他需要一根长度超过 7 英寸的魔杖。让我们看一些这样做的代码:

```
//For comprehension that prints all wands with length > 7 inches
val wandsLongerThan7Inches: List[wand] = for {
//Generator expression
wand <- wands
//Guard
  if wand.length > 7
//Yielding
} yield wand 
```

让我们来谈谈每一件作品:

1.  生成器表达式(`wand <- wands` ):
    这使我们能够访问`wands`列表中的各个元素。在 python 中，我们将把它写成`for wand in wands`。`<-`操作符被称为左箭头操作符，这是 Scala 在`List`中迭代元素的方式

2.  守卫(`if wand.length > 7` ):
    守卫只是一个`if`表达式，帮助我们过滤 foor 循环中的元素。在这种情况下，我们只想保留大于 7 英寸的魔杖。

3.  屈服(`yield wand` ):

    屈服是一种为了理解而从返回值的方式。在这种情况下，每次魔杖通过守卫语句，我们就把它放到 T2 列表中。

4.  为了理解:
    这一整块叫做 a，为了理解。它类似于其他编程语言中的 for 循环，但我们称之为理解，因为它可能有副作用。在这种情况下，副作用是填充`wandsLongerThan7Inches`列表。

让我们看看`wandsLongerThan7Inches`列表是什么样子的:

```
 scala> wandsLongerThan7Inches
res30: List[wand] = List(hazelwood wand of length 9 inches with veela hair, Walnutwood wand of length 11 inches with troll whisker, Cypresswood wand of length 9 inches with kelpie hair) 
```

耶！我们的新清单只包含长度超过 7 英寸的魔杖。但是我们还没完呢！我们认为巫师用魔法生物的毛发制成的魔杖会做得很好。让我们把它添加到我们的守卫条件:

```
scala> val wandsLongerThan7InchesWithMagicalCreatureHair: List[wand] = for {
  wand <- wands
  if wand.length > 7
  if wand.magic.contains("hair")
} yield wand 
```

如果我们愿意的话，我们可以在保护中添加多个 if 语句。在这种情况下，我们还可以选择简化代码并对条件进行分组:

```
scala> val wandsLongerThan7InchesWithMagicalCreatureHair: List[wand] = for {
  wand <- wands
  if wand.length > 7 && wand.magic.contains("hair")
} yield wand 
```

这个看起来不错，我们来看看结果:

```
scala> wandsLongerThan7InchesWithMagicalCreatureHair
res1: List[wand] = List(hazel wand of length 9 inches with veela hair, Cypress wand of length 9 inches with kelpie hair) 
```

不错！巫师只需要“嗖嗖嗖”挥动两根魔杖，然后等其中一根来抓他！

你可以在下面找到链接来了解更多关于理解和魔法棒的知识。

感谢阅读！

* * *

1.  github 上的所有示例代码
2.  这是来自波特莫尔的整洁的魔杖
3.  关于[的官方文档，用于理解](https://docs.scala-lang.org/tour/for-comprehensions.html)
4.  这些来自 scala cookbook 作者的 for 循环的例子