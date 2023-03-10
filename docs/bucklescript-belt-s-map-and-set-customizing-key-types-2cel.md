# 脚本的腰带地图和设置:自定义关键类型

> 原文：<https://dev.to/johnridesabike/bucklescript-belt-s-map-and-set-customizing-key-types-2cel>

*这篇文章原本是用 ReasonML 写的。我在 2021 年 5 月更新了它，使用 ReScript。*

[在我之前的文章](https://dev.to/johnridesabike/introduction-to-bucklescript-s-belt-containers-map-hashmap-set-3b5l)中，我们回顾了 Belt 的容器是如何工作的。大多数时候，尤其是来自 JavaScript 的时候，你只需要使用 Belt 的`String`或`Int`内部模块(例如`Belt.Map.String`)。随着您对 ReScript 越来越熟悉，您可能会发现这些类型并不理想。例如，如果您需要一组元组，或者一组记录，该怎么办？或者假设您想将记录映射到列表。Belt 允许您使用任何类型的钥匙，但需要一点设置。

## 举例:元组的一个`Belt.Set`

在[我的国际象棋锦标赛应用程序 Coronate，](https://github.com/johnridesabike/coronate)中，我需要跟踪哪些玩家应该避免被配对。我创建了一个名为`AvoidPairs`的模块，并制作了这个类型:

```
type t = (string, string) 
```

Enter fullscreen mode Exit fullscreen mode

每个`string`都是一个玩家的 ID。像`("dale_cooper", "audrey_horne")`这样的值意味着不应该匹配具有这些 ID 字符串的玩家。Belt 的`Set`数据结构非常适合存储它们，因为每个元组都应该是唯一的。(可能的重复会导致应用程序的其他地方出现错误。)然而，`Set`模块需要一种方法来辨别哪些是唯一的，哪些不是。考虑到元组(JS 数组对象)的内容并不相等。更复杂的是，`("dale_cooper", "audrey_horne")`应该和`("audrey_horne", "dale_cooper")`完全一样。我不在乎顺序，只要它们是成对的。

Belt 附带了一个名为`Id`的模块，它有针对这些情况的助手模块。在这种情况下，我们希望使用`Belt.Id.MakeComparable`仿函数。如果“functor”对你来说是一个新词，就把它想象成一个模块“function ”,它接受模块作为参数并返回一个新模块。 [*真实世界 OCaml* 有深度讲解](https://dev.realworldocaml.org/functors.html)。

`Id.MakeComparable`需要一个模块，该模块有一个`type t`和一个函数`cmp`，该函数接受两个`t`值并返回一个整数(返回 0 表示值相同)。

以下是我使用的解决方案。它的函数`cmp`比较 id 的四种组合并返回结果。

```
module T = Belt.Id.MakeComparable({
  type t = (string, string)
  let cmp = ((a, b), (c, d)) => {
    let w = compare(a, c)
    let x = compare(b, d)
    let y = compare(a, d)
    let z = compare(b, c)
    switch (w, x, y, z) {
    /* Sometimes adding them returns 0 even if they're not equivalent.
     There might be a better way to pattern-match this, but this works. */
    | (1, -1, 1, -1)
    | (1, -1, -1, 1) => 1
    | (-1, 1, 1, -1)
    | (-1, 1, -1, 1) => -1
    | (w, x, y, z) => w + x + y + z
    }
  }
}) 
```

Enter fullscreen mode Exit fullscreen mode

处理这个例子的一个更好的方法是始终确保配对是有序的。[你可以在这里](https://github.com/johnridesabike/coronate/blob/00d245c12039177d4f406ca78c707d4c40c50f78/src/Data/Data_Config.res#L27)看到 Coronate 目前是怎么做的。

然后返回的模块必须传递给`Belt.Set.make`。您可能已经了解到模块存在于核心语言之外的一个层上，所以您不能做像将模块存储在值中这样的事情。ReScript 用“[一级模块](https://rescript-lang.org/docs/gentype/latest/supported-types#first-class-modules)”提供了一种解决方法这个特性使我们能够将一个模块包装成一个可以传递给函数的值，所以我们可以这样做:

```
let empty = Belt.Set.make(~id=module(T)) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了一个空的元组集。这看起来好像有很多麻烦，但它让你以后的生活更容易。例如，我现在可以这样做:

```
/* I want these players to avoid each other: */
let avoidpairs = Belt.Set.add(empty, ("dale_cooper", "audrey_horne"))
/* Later, I need to check if they're in the set: */
Belt.Set.has(avoidpairs, ("audrey_horne", "dale_cooper"))
/* returns true */ 
```

Enter fullscreen mode Exit fullscreen mode

注意当我调用`Set.has`的时候，我用的 tuple 和原来的不一样？(即使这些值处于相同的顺序，它们也不会是等价的`===`。)然而它还是返回了`true`，这就是我想要的行为。所有复杂的逻辑都已经处理好了，因此我的代码的其余部分更容易使用。

## 举例:一条`Belt.Map`的记录

一旦你掌握了这一点，很多概念就变得简单了。例如，假设您有一系列书籍，并且您想将每本书映射到它们的用户评论列表。

```
type book = {
  title: string,
  author: string,
}

type star =
  | One
  | Two
  | Three
  | Four
  | Five

type review = {
  name: string,
  date: float,
  text: string,
  rating: star,
} 
```

Enter fullscreen mode Exit fullscreen mode

一种解决方案是给每本书一个惟一的 ID，然后使用`Map.String`。但是你也可以做一个以`book`记录本身为关键字的地图！简单地写一个比较书籍的`cmp`函数。

```
module BookCompare = Belt.Id.MakeComparable({
  type t = book
  let cmp = (a, b) =>
    switch compare(a.title, b.title) {
    | 0 => compare(a.author, b.author)
    | x => x
    }
})
let reviews = Belt.Map.make(~id=module(BookCompare))

let book = {title: "Incarnadine", author: "Szybist, Mary"}
let review = {
  name: "John",
  rating: Five,
  text: "Great collection! I liked the poem *Invitation*.",
  date: 1568898720165.0,
}
let reviews = Belt.Map.set(reviews, book, list{review}) 
```

Enter fullscreen mode Exit fullscreen mode

哒哒，我们用这本书作为地图的钥匙！(好吧，可能不是实体书本身。)因为我们比较的是书籍的数据，而不是任意的 ID 字符串，这消除了添加重复书籍等错误。如果您确实需要区分具有相同数据的书籍，您可以只添加一个新字段(如`publisher`或`edition`)。在我们的代码中，如果所有的字段都匹配，那么它就是同一个项目。

同样，这看起来像是额外的前期工作，但从长远来看，这将简化您的代码。在某种程度上，你无论如何都要让*让*在你的代码中进行这些比较。这确保了它们从一开始就构建到您的数据结构中。