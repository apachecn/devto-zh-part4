# 通过委托实现多个接口

> 原文：<https://dev.to/lankydandev/implementing-multiple-interfaces-through-delegation-h3a>

在 Kotlin 中，一个类可以实现多个接口。这是常识。一个类还可以使用委托来实现许多接口，这些接口的实现来自传递给构造函数的任何委托对象。例如:

```
class GeneticExperiment(human: Human, animal: Animal) : Human by human, Animal by animal

interface Human {
  fun eat()
  fun sleep()
  fun poop()
}

interface Animal {
  fun bite()
} 
```

Enter fullscreen mode Exit fullscreen mode

这个奇怪的类继承了传递给构造函数的`Human`和`Animal`对象的功能。如果没有委托，任何一个接口上的函数都需要在类中手工写出。

需要注意的重要一点是，`Human`和`Animal`不共享一个公共的父接口。

如果您确实有从一个公共接口扩展的接口，那么由于来自委托对象的冲突实现，将会出现编译错误。例如，代码:

```
class ShapeShifter(human: Human, weightLifter: WeightLifter) : Human by human, WeightLifter by weightLifter

interface Human {
  fun eat()
  fun sleep()
  fun poop()
}

interface WeightLifter : Human {
  fun liftHeavyStuff()
  fun pose()
} 
```

Enter fullscreen mode Exit fullscreen mode

导致错误的原因:

```
Delegation.kt:80:1: error: class 'ShapeShifter' must override public open fun eat(): 
Unit defined in dev.lankydan.ShapeShifter because it inherits many implementations of it
class ShapeShifter(human: Human, weightLifter: WeightLifter) : Human by human, WeightLifter by weightLifter
^
Delegation.kt:80:1: error: class 'ShapeShifter' must override public open fun sleep(): 
Unit defined in dev.lankydan.ShapeShifter because it inherits many implementations of it
class ShapeShifter(human: Human, weightLifter: WeightLifter) : Human by human, WeightLifter by weightLifter
^
Delegation.kt:80:1: error: class 'ShapeShifter' must override public open fun poop(): 
Unit defined in dev.lankydan.ShapeShifter because it inherits many implementations of it
class ShapeShifter(human: Human, weightLifter: WeightLifter) : Human by human, WeightLifter by weightLifter
^ 
```

Enter fullscreen mode Exit fullscreen mode

正如您在错误中看到的，`ShapeShifter`类不知道采用哪个委托实现。编译错误必须通过显式重写冲突的函数来解决。通过覆盖这些函数，您可以决定希望使用哪个委托实现，或者您可以提供一个新的委托实现。这个类的一个固定版本可能是这样的:

```
class ShapeShifter(
  private val human: Human,
  private val weightLifter: WeightLifter
) : Human by human, WeightLifter by weightLifter {

  override fun eat() {
    human.eat()
  }

  override fun sleep() {
    weightLifter.sleep()
  }

  override fun poop() {
    println("💩")
  }
}

interface Human {
  fun eat()
  fun sleep()
  fun poop()
}

interface WeightLifter : Human {
  fun liftHeavyStuff()
  fun pose()
} 
```

Enter fullscreen mode Exit fullscreen mode

> 只有在`Human`界面中声明的功能需要手动覆盖

从这里稍微后退一步。只要实现了子接口的所有函数，实现子接口并将其功能委托给子接口的父接口的类就可以编译。难以理解的措辞。举个例子应该有助于澄清我想说的话

```
class ShapeShifter(copied: Human) : WeightLifter, Human by copied {

  override fun liftHeavyStuff() {
    println("🏋️‍♂️")
  }

  override fun pose() {
    println("💪")
  }
}

interface Human {
  fun eat()
  fun sleep()
  fun poop()
}

interface WeightLifter : Human {
  fun liftHeavyStuff()
  fun pose()
} 
```

Enter fullscreen mode Exit fullscreen mode

> 只有在`WeightLifter`界面中声明的功能需要手动添加

总结一下:

*   一个类可以实现多个接口，并将其功能委托给一个或多个对象
*   具有多个接口的类扩展了一个公共的父类，可以委托每个接口的实现，但必须重写父类中定义的函数
*   实现子接口的类可以委托给它的父接口，并且只需要添加在子接口中定义的函数。

如果你喜欢这篇文章或者觉得它有帮助(或者两者兼而有之),那么请随时在 Twitter 上关注我，地址是 [@LankyDanDev](https://twitter.com/LankyDanDev) ,并且记得与其他任何可能发现这篇文章有用的人分享！