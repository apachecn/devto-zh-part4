# ReScript 的 Belt 容器介绍:map、hashmap 和 set

> 原文：<https://dev.to/johnridesabike/introduction-to-bucklescript-s-belt-containers-map-hashmap-set-3b5l>

*这篇文章原本是用 ReasonML 写的。我在 2021 年 5 月更新了它，使用 ReScript。*

对于学习 JavaScript 的 JavaScript 开发人员来说，一个障碍是掌握容器类型。ReScript 的标准库 Belt 有文档，但对新用户不友好。它们没有清楚地解释容器是什么，它们是如何工作的，或者你为什么要使用它们。幸运的是，一旦你掌握了基本知识，它们也不难理解。

## 来自 JavaScript:被滥用的对象类型

在 JavaScript 中，我们几乎对所有事情都使用对象类型。在许多情况下，我们像使用一个脚本记录一样使用它:一个具有固定键和值的容器，每个键和值都有自己的类型。在其他情况下，我们使用它更像一个映射:一个包含动态数量的键的容器，每个键都“映射”到一个值。[重写文件详细解释了这一区别](https://rescript-lang.org/docs/manual/latest/object)。

在 ReScript 中，这两种用法是完全独立的结构，不能重叠。ReScript 是围绕管理这两种情况而设计的。对于静态对象有记录，对于“地图”对象 ReScript 提供了 [`Js.Dict`模块](https://rescript-lang.org/docs/manual/latest/api/js/dict)。

如果你经常使用它们，你会很快注意到它们带来了一些摩擦、笨拙的代码和缺失的特性。例如，您不能像在 JavaScript 中那样简单地进行不可变的更新，或者使用扩展语法合并对象。

与普通的旧 JavaScript 相比，这似乎是一种降级，但这并不是全部。ReScript 附带了一个标准库 Belt，它使用自己的数据容器。最有可能的是，你想在你的项目中使用这些而不是`Js.Dict.`

## OCaml 数据容器

在我们开始使用 Belt 之前，理解 Belt 容器类型都基于 OCaml 标准库容器是有帮助的(事实上，您可以在 ReScript 中使用 OCaml 标准库，但是 Belt 旨在编译为 JavaScript 时更高效)。因此，您可以找到的许多关于 OCaml 容器的文档也适用于 Belt。例如，【OCaml 官方网站有一个很好的不同容器的比较，你可以在这里看到。[这本书*现实世界 OCaml* 也有使用它们的深度指南](https://dev.realworldocaml.org/maps-and-hashtables.html)。虽然这些网站使用的代码与 Belt 并不完全相同(并且是用 OCaml 编写的，而不是用 ReScript 编写的)，但概念基本相同。

现在，让我们来看看这些容器到底是什么:

## [地图](https://rescript-lang.org/docs/manual/latest/api/belt/map)

为了简单起见，我们先来看它的一个内部模块:`Belt.Map.String`。

`Belt.Map.String`类似于用于“映射”值的 JavaScript 对象(或实际的 JavaScript `Map`对象)。从概念上讲，它是一系列的键值对。键是字符串，值可以是您想要的任何类型，只要它们都是相同的类型。

如果您想在代码中使用地图，您可能认为第一步是创建一个空地图。令人惊讶的是，`Belt.Map.String`没有`make`功能。相反，你只需使用`Belt.Map.String.empty`值(这不是一个函数！).因为映射是不可变的，所以每当您需要一个“新的”空映射时，您都可以重用该值。

如果您不熟悉不变性，请记住，当您使用类似于`Belt.Map.String.set`的函数赋值时，它会返回一个具有新值的*新*映射。原始地图将保持不变。(由于地图在幕后工作的方式，这实际上是非常高效的。)

在 JavaScript 中，对象键是字符串，所以一开始可能对`Belt.Map.String`最熟悉。但是你也可以用`Belt.Map.Int`，它用(你能猜到吗？)整数作为键。这使得它更类似于 JavaScript 数组。

(你可能会想，为什么我要用 int map 而不是 array？不变性可能是一个好处。同样，有了映射，您可以使用负整数作为键。您还可以给`0`赋值，然后给`99`赋值，您将得到一个只有这些值的地图。在一个数组中，如果你做同样的事情，你会得到一个有 100 个条目的数组，其中 98 个是`undefined`。)

## [散列表](https://rescript-lang.org/docs/manual/latest/api/belt/hash-map)

从表面上看，`Belt.HashMap.String`(或`Belt.HashMap.Int`)的工作原理与`Belt.Map.String`(或`Belt.Map.Int`)相似。一个关键的区别是散列表是可变的。当您用函数`Belt.HashMap.String.set`赋值时，它会就地修改散列表。

在函数式编程领域，不变性是首选，但 hashmaps 之类的结构也很有用。在某些情况下，hashmap 比常规 map 更具性能。两者都有优点和缺点，并且都有不同的用途。

你会注意到这里的*是* a `Belt.HashMap.String.make`(和`Belt.HashMap.Int.make`)。他们采取一种说法:`hintSize`。对于散列表将有多少个键，这只是你的最佳猜测。它越接近数据的实际大小，代码运行的性能就越高。你不必强调它是完全正确的。

## [设定](https://rescript-lang.org/docs/manual/latest/api/belt/set)

`Belt.Set.String`和`Belt.Set.Int`模块在概念上类似于一个 JavaScript `Set`对象。它们存储一系列必须是唯一的值。如果您尝试添加一个重复的值，那么该集合将保持不变。就像腰带`Map`一样，套是不可变的，所以你通常不会“制造”它们，只是用`Belt.Set.String.empty`(或者`Belt.Set.Int.empty`)来代替。更新将返回一个新的集合，而不修改原始集合。

## 刚刚开始

大多数时候，尤其是来自 JavaScript 的时候，这些容器类型会满足您的需求，但是还有其他类型。Belt 有“堆栈”和“队列”容器，以及类似“hashset”或“mutablemap”的变体。其他标准库通常也有自己的容器类型。

## 下一步:制作定制容器

你可能注意到我跳过了`Belt.Map`、`Belt.HashMap`和`Belt.Set`模块，只解释了它们各自的`String`和`Int`内部模块。外部模块允许您使用自己的自定义数据类型作为您的键。(`String`和`Int`内部模块的存在很大程度上是为了方便，因为使用字符串和整数作为键是如此普遍。)

用自定义键设置容器有点复杂，并且利用了一些高级的脚本特性。那就需要下次再发博文了。敬请期待！