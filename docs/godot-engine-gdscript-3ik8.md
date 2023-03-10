# Godot 引擎:GDScript

> 原文：<https://dev.to/winstonyallow/godot-engine-gdscript-3ik8>

我想分享一些我喜欢的关于 [Godot 游戏引擎](https://godotengine.org/)的事情。今天我要写的是 gdscript。

这篇文章不打算作为一个教程。查看[文档](https://docs.godotengine.org/en/3.1/getting_started/scripting/gdscript/index.html)以了解更多关于 gdscript 的信息。在这篇文章中，我将指出我喜欢 gdscript 的地方。

## 整合

当我第一次开始使用 Godot 时，我对 gdscript 相当怀疑。既然有这么多其他现存的语言，为什么还要创造一种新的语言呢？对我来说，答案是令人敬畏的集成。它是在游戏引擎的基础上构建的。

有这么多很好的集成功能！下面是展示其中一些的一小段代码:

```
onready var detector = $DetectorArea
export var health = 10.0

remote func apply_damage(amount):
    health -= amount 
```

Enter fullscreen mode Exit fullscreen mode

关键字`onready`可用于延迟变量的初始化，直到节点成为场景树的一部分。如果需要从场景树中访问另一个节点进行初始化，这非常有用。

`$`是通过名称访问场景树中节点的语法糖。

`export`关键字用于使变量在编辑器中可编辑。*示例用例:*
*有许多行为相同但生命值不同的敌人。您可以在编辑器 UI 中简单地更改单个实例的导出值！*

Godot 支持高级多人联网。有像`remote`这样的关键字可以让 gdscript 通过网络调用函数。

## 语法

语法受 python 启发。因为我喜欢 python，所以适应 gdscript 对我来说很容易。像 python 一样，gdscript 也使用缩进。

## 可选类型提示

gdscript 中的(相当新)[类型提示](https://docs.godotengine.org/en/3.1/getting_started/scripting/gdscript/static_typing.html)易于使用，并且完全可选。我经常在没有类型提示的情况下快速原型化一些东西。除了原型之外，我尽可能使用类型提示。

编辑有一个“安全线”的概念。完全有可能只在少数地方使用类型。类型检查没有发现错误的行被标记为安全的。这由一个绿色的行号表示。无法执行类型检查的行(因为缺少类型提示)没有这个绿色行号。类型检查发现的错误直接显示在编辑器中。

通常可以推断出类型(使代码更短)。
*举例:`var foo := "bar"`代替`var foo: String = "bar"`*

## `match`声明

我喜欢把`match`想象成一个增强版的`switch`语句。让我用一个名为`bookshelf` :
的数组来展示一下它的神奇之处

```
match bookshelf:
    []:
        print("Empty bookshelf")
    ["The Hitchhiker's Guide to the Galaxy", ..]:
        print("The first book in your shelf is awesome!")
        print("Nothing else is important.")
    ["LOTR 1"], ["LOTR 2"], ["LOTR 3"]:
        print("You own exactly one book.")
        print("It is a book from the Lord of the Rings.")
    [var lonely_book, _ ]:
        print("There are two books.")
        print("The first book is named ", lonely_book) 
```

Enter fullscreen mode Exit fullscreen mode

在上面的`match`语句中，我使用了数组模式。`..`用于开放式阵列。在模式中使用`var`将对应的值绑定到新变量。`_`通配符匹配任何东西(并且可以是复杂模式的一部分)。

有许多类型的可用匹配模式，从简单的值到复杂的字典模式！

在我看来`match`是一个非常强大的结构。然而，这可能会导致代码难以阅读。小心使用。想想你怎么用，为什么用。我个人喜欢`match`，但通常可以使用更简单的结构。我只在两三个实际项目中使用它。

## 感谢阅读！✨

这是我在 DEV 上的第一篇博文。我的计划是创建一系列关于我喜欢戈多的事情的帖子。如果您有任何问题或建议，请告诉我！