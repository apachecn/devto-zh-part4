# JavaScript 中的反应式编程

> 原文：<https://dev.to/samholmes/reactive-programming-in-javascript-141o>

反应式编程是一种关注变化传播的声明式编程范例。如果你想要一个完整的解释，我推荐你阅读保罗·斯托弗的文章《什么是反应式编程》？在本文中，我将向您展示如何用 JavaScript 实现一个反应式编程环境。

# 状态为图形

为了在 JavaScript 中完成反应式编程，我们需要自己管理我们的状态，并为我们的变量构造一个依赖图。这样，当一个变量的状态发生变化时，我们会将这种变化传播到依赖于该状态的所有其他变量。例如:

```
a = 10
b = a + 1
c = a + 2 
```

这段代码将构建如下图形:

```
 a
  / \
 b   c 
```

该图的目的是建立变量之间的依赖关系。这样，当`a`发生变化时，我们知道要重新评估(或更新)`b`和`c`。换句话说，`b`和`c`是*依赖*于`a`。

我们将使用一个对象来实现我们的图形，该对象将一个依赖变量的引用映射到一组依赖引用:

```
let depgraph = {}

depgraph[a] = {b: true, c: true} 
```

为了让我们的生活更容易，我们可以创建`addDependent`和`removeDependent`函数来添加和删除图中的依赖项。

```
// Adds a dependent to the depgraph
function addDependent(dependency, dependent) {
  depgraph[dependency] = depgraph[dependency] || {}
  depgraph[dependency][dependent] = true
}
// Removes a dependent from the depgraph
function removeDependent(dependency, dependent) {
  depgraph[dependency] = depgraph[dependency] || {}
  delete depgraph[dependency][dependent]
} 
```

## 状态

在我们的程序中，变量将保存对它们状态的引用，而不是它们状态的值。我们将使用`Symbol()`为我们的变量创建唯一的引用。这保证了我们的引用彼此之间没有任何冲突。

但是，我们需要一个地方来存储变量的实际值(缓存)。因此，我们将使用一个对象来映射对值的引用:

```
let state = {}

state[a] = 10
... 
```

_ 请注意，`a`不是字符串；这是因为它等于变量的唯一`Symbol`。

## [更新程式](#updaters)

现在我们需要一种方法来计算任意时刻的变量。因此，我们需要为每个变量维护一个“updater”函数，以便重新评估变量的状态。

```
let updaters = {}

updaters[a] = () => 10
updaters[b] = () => state[a] + 1
updaters[c] = () => state[c] + 2 
```

您可以将更新器视为存储每个变量赋值的右侧表达式。现在，我们可以随时调用变量的更新程序来检索它的状态。

让我们一起来。我们有一个绘制依赖关系的图表。我们将状态存储在一个位置。我们有一套更新功能。当一个变量的状态改变时，我们希望在我们的图中找到它的所有依赖项，并运行它们的每个更新函数，以便获得新的状态。当然，对于这些变量的任何从属变量，我们也递归地继续这个过程。为此，让我们构建一系列函数，这些函数将成为我们的反应式编程 API。

# API

我们的 API 将由一组函数组成:声明、绑定、更新、重组、检索和删除。为了理解我们将如何使用 API，让我们看看每个函数的本地 JavaScript 等价物:

```
// Variable declaration
let a = true
// becomes
let a = declare(true)

// Variable assignment
a = false
// becomes
update(a, false)

// Variable assignment (with dependencies)
a = b + 1
// becomes
bind(a, () => retrieve(b) + 1)

// Value retrieval
console.log(a)
// becomes
console.log(retrieve(a))

// Finally, memory deallocation
// JavaScript's GC takes care of this for us,
// but we need to manually release our variables.
remove(a) 
```

函数`reorg`将在内部使用，没有本地等效函数。

让我们进入每个功能的实现细节。

## 宣告

```
function declare(expr) {
  if (typeof expr === 'undefined') expr = () => undefined

  let ref = Symbol()

  return bind(ref, expr)
} 
```

这个函数将允许我们声明一个新变量，并使用`bind`将一个表达式绑定到它。这将用下面的内容替换我们的本地变量声明:

```
let a = declare(10)
let b = declare(() => retrieve(a) + 1)
let c = declare(() => retrieve(a) + c) 
```

## 绑定

```
function bind(ref, expr) {
  updaters[ref] = () => update(ref, expr)
  reorg(ref)
  return ref
} 
```

这个函数将用于将一个表达式绑定到一个引用。

我们为引用创建一个更新器，调用`reorg`，然后返回引用。随着时间的推移，一切都会变得更有意义。但是需要注意的最重要的一点是，updater 是一个用给定的表达式更新引用的函数。

## 重组

```
function reorg(ref) {
  // Remove ref as a dependent to other refs in the graph
  // Effectively detaching it it from the graph
  Object.getOwnPropertySymbols(depgraph).forEach(dependency =>
    removeDependent(dependency, ref)
  )

  // Run the updater and retrieve the dependencies during the update
  let dependencies = updaters[ref]()

  // Update the graph using dependencies
  // Effectively, re-attaching the updated ref to the graph
  if (dependencies) {
    dependencies.forEach(dependency => addDependent(dependency, ref))
  }
} 
```

该函数的目的是动态维护引用之间的依赖关系。换句话说，当定义一个变量时(使用 declare 或 bind ),我们必须将它建立为依赖于表达式中的任何变量。

该函数将在给定单个引用的情况下重新组织图形。首先，它将从图中分离引用，运行其 updater 函数，然后将其重新附加到图中。updater 函数总是返回引用的依赖项，所以我们知道应该如何将它重新连接到图中。

## 更新

```
function update(ref, expr) {
  let dependencies

  // Set to object to effectively gather all state retrievals
  stateRecentlyAccessed = {}

  // Execute expression and set actual state
  state[ref] = typeof expr === 'function' ? expr() : expr

  // If statement prevents error (not sure why stateRecentlyAccessed is null sometimes)
  if (stateRecentlyAccessed)
    dependencies = Object.getOwnPropertySymbols(stateRecentlyAccessed)

  // Set stateRecentlyAccessed to null to turn off listening
  stateRecentlyAccessed = null

  // This is where we invoke dependent updaters
  if (depgraph[ref]) {
    Object.getOwnPropertySymbols(depgraph[ref]).forEach(reorg)
  }

  return dependencies
} 
```

现在我们来看核心部分，即我们的实现。该函数将更新`state`并返回引用表达式的所有依赖项。

这就是你注意到`stateRecentlyAccessed`的地方。我承认我忘了提这个全局。它应该保存最近使用`retrieve`访问的引用。如果我们看看检索函数会更有意义。

## 检索

```
function retrieve(ref) {
  if (stateRecentlyAccessed) {
    stateRecentlyAccessed[ref] = true
  }
  return state[ref]
} 
```

这个函数只是检索引用的状态，但是它也有一个副作用。这里的副作用是修改`stateRecentlyAccessed`。每当访问一个引用的状态时，`stateRecentlyAccessed`就会被修改，以便它包含一个使用该引用作为其属性访问器的属性。`stateRecentlyAccessed`全局变量是`update`如何返回依赖列表以及系统如何动态维护变量关系。

## 删除

```
function remove(ref) {
  // Removes it from state and updaters
  delete state[ref]
  delete updaters[ref]

  // Removes it from depgraph
  Object.getOwnPropertySymbols(depgraph).forEach(dependency => {
    if (dependency === ref) {
      delete depgraph[dependency]
    } else {
      Object.getOwnPropertySymbols(depgraph[dependency]).forEach(dependent => {
        if (dependent === ref) {
          delete depgraph[dependency][dependent]
        }
      })
    }
  })
} 
```

最后，我们需要一种方法来移除一个引用，并在之后进行清理。不幸的是，我们不能充分利用 JavaScript 的垃圾收集器，因为引用总是在全局变量`state`、`updaters`等中使用。所以，我们必须用这个函数手动清理我们的引用变量。也许可以实现我们自己的垃圾收集器，但是为了简单起见，我选择不考虑这个想法。

# 使用我们的 API

让我们用我们的 API 构造一个例子。

```
let coordinates = declare('Move your mouse!')
let mouseX = declare()
let mouseY = declare()

bind(coordinates, `${retrieve(mouseX)},${retrieve(mouseY)}`)

document.addEventListener('mousemove', (ev) => {
  update(mouseX, ev.clientX)
  update(mouseY, ev.clientY)
})

declare(() => document.body.innerHTML = retrieve(coordinates)) 
```

在这个例子中，我们声明了一个`coordinates`变量以及另外两个变量:`mouseX`和`mouseY`。我们根据 mouseX 和 mouseY 将坐标绑定到一个表达式。

之后，我们在`mousemove`事件中更新 mouseX 和 mouseY。在这种情况下，我们不需要使用`bind`,因为我们知道它们不会有任何`retrieve()`函数调用。使用 update 会更有性能，因为它跳过了 reorg。

我们也声明`document.body.innerHTML`等于坐标。注意我们不需要这个声明返回的引用。但是，如果在程序的另一部分中使用了 innerHTML 状态，您可以使用该引用来访问它。例如，

```
let innerHTML = declare(() => document.body.innerHTML = retrieve(coordinates))

// Use innerHTML reference somewhere else... 
```

# 期末笔记

现在，您已经拥有了编写反应式程序所必需的工具。为改进实现需要考虑的一些事情:

1.  更好的垃圾收集。
2.  使用`Proxy()`使 API 更加简洁。
3.  编写一个完全抽象 API 的 transpiler。

总而言之，我希望这是对反应式编程的一个很好的介绍。