# 副作用到底是什么？-# 30 日休假第 2 周

> 原文：<https://dev.to/dslemay/what-is-a-side-effect-anyway-30daysoffp-week-2-j9b>

管理副作用是函数式编程的核心原则。副作用的存在会使程序更不稳定，更容易出错，更难推理。在本帖中，我们将深入探讨什么是副作用(和副作用原因)，如何管理它们，以及功能纯度。

## 什么是副作用

当一个函数改变了自身以外的东西时，就会产生副作用。这可能是变异一个全局变量，将数据推入一个数组，跟踪应用程序状态，等等。应用程序必须有副作用才能运行，否则一开始就不会有运行的反馈。写入 DOM、发出 console.log 和进行 API 调用都是副作用。因此，它们不可能被完全消除。函数式编程试图尽可能地减少它们，控制它们发生的时间和方式。

改变应用程序状态的副作用使得函数*不确定*。换句话说，按顺序多次调用具有相同输入的函数不会产生相同的输出或效果。这是有问题的，因为它增加了 bug 的表面积，并且使得推理一个函数在做什么变得更加困难。举个例子:

```
const results = []

const addToResults = x => results.push(x)

addToResults(2)
console.log(results); // [2]

addToResults(2)
console.log(results); // [2, 2] 
```

这里，我们的函数`addToResults`直接推送到我们的结果数组上。这超出了函数的范围，是一个副作用。这个函数也没有直接返回。虽然在这个例子中这似乎没有问题，但是随着函数的增长、调用其他函数或驻留在不同的模块中，复杂性会增加。调试或理解这种编程方法需要开发人员跟踪整个函数调用堆栈，查看哪些状态发生了变化。这不太理想，而且还会将功能与具体的实现细节联系起来。

这个函数中副作用的存在使得*不纯*。一个函数的纯度是由它没有副作用或副作用决定的。纯函数是可取的，因为它们确保在给定相同输入的情况下，我们将总是得到相同的输出。这允许更大的重用、组合和确定性。不纯的函数也会影响其他函数的纯度。如果一个函数调用或依赖一个不纯的函数，它也是不纯的。

## 如何控制副作用

这些副作用可以通过直接输出结果数组的新实例来管理。这消除了副作用，使函数调用*确定性*。我们可以确信，如果我们用相同的参数调用函数，我们将得到相同的结果。

```
const addToResults = (arr, x) => arr.concat(x)

const results = []

console.log(addToResults(results, 2)) // [2]

const newResults = addToResults(results, 2)
console.log(newResults) // [2] 
```

现在，我们直接传入当前的结果数组，并将新值连接到它上面。这将直接返回一个新数组，而不是在函数范围之外改变变量。我们还用方法`push`替换了方法`concat`,以支持不可变的数据结构，稍后会对此进行更多介绍。我们可以将这个操作的结果存储到一个新的变量中，或者从结果中调用额外的函数，而不需要担心这些变化会影响全局状态的依赖项。

## 那身边的原因呢

副作用是功能杂质的另一个方面。当一个函数直接*依赖于*一个它不直接作为参数接收的值时，它们就存在了。副因的存在也使一个函数不纯。

```
let y = 5

const add = x => x + y

console.log(add(2)) // 7

y = 10

console.log(add(2)) // 12 
```

这里我们有一个影响 sum 函数输出值的变量，但不是显式输出。该功能依赖于此端导致处于应用程序的状态。然而，即使在这个简单的例子中，改变副因的值也会影响函数的输出。这使得我们的功能不纯。为了解决这个问题，我们需要将`y`作为一个参数进行显式输入。

```
const add = (x, y) => x + y

console.log(add(2, 5)) // 7

console.log(add(2, 10)) // 12 
```

现在我们的功能是纯粹的，不依赖于应用程序中的外部状态。纯函数也更容易测试。因为它们不依赖于应用程序的状态，所以可以独立地导出和测试。这是纯函数如何产生更多可重用代码的另一个例子。

## 青睐价值不变性

函数式编程也喜欢不可变的值。当一个参数被传递给一个函数时，通常 JavaScript 会传递它的值。然而，在对象和数组的情况下，该函数接收一个对原始对象的引用。如果值发生变化，这会导致一些棘手的情况。

```
const recipe = {
  name: 'Risotto',
  ingredients: ['rice', 'stock'],
}

const recipe2 = recipe

const addIngredients = (recipe, ...ingredients) =>
  recipe.ingredients.push(...ingredients)

addIngredients(recipe2, 'onions', 'butter')
console.log(recipe) // { name: 'Risotto', ingredients: ['rice', 'stock', 'onions', 'butter'] }
console.log(recipe2) // { name: 'Risotto', ingredients: ['rice', 'stock', 'onions', 'butter'] }
console.log(recipe === recipe2) // true 
```

在本例中，`recipe2`引用了原始的`recipe`对象。它们不是独立的对象。变异一个对象会影响另一个对象，因为它们都指向同一个引用。这是不可取的，并使我们的代码更难推理。如果我们需要使用对象以前的副本，这种变异使它不可能。为了解决这个问题，我们需要在对对象进行变异之前制作一个副本。

```
const recipe = {
  name: 'Risotto',
  ingredients: ['rice', 'stock'],
}

const addIngredients = (recipe, ...ingredients) => ({
  ...recipe,
  ingredients: recipe.ingredients.concat(ingredients)
})

const recipe2 = addIngredients(recipe, 'onions', 'butter')
console.log(recipe) // { name: 'Risotto', ingredients: ['rice', 'stock'] }
console.log(recipe2) // { name: 'Risotto', ingredients: ['rice', 'stock', 'onions', 'butter'] }
console.log(recipe === recipe2) // false 
```

现在，在更新配料数组之前，我们的`addIngredients`函数复制了 recipe 对象。现在我们有了对象的副本，我们可以在不影响原始对象的情况下更新它。值得注意的是，spread 操作符只复制一级深度的**。如果您的数据结构有嵌套多层的对象，较低的级别仍将引用原始数据结构。**

许多数组方法会改变现有的数组，而不是返回一个新的数组。在改变数组之前复制数组的辅助工具有助于保持值的不变性。直接改变数组实例的数组方法包括:

*   `pop`
*   `push`
*   `reverse`
*   `shift`
*   `sort`
*   `splice`
*   `unshift`
*   直接设定一个值，如`arr[0] = 'foo'`

## TLDR

*   当一个函数影响到它自身之外的应用程序状态时，就会产生副作用(比如输出到 DOM，更新一个全局变量，等等)
*   当一个函数引用其作用域之外的数据来完成它的操作时，就会产生一个副作用。如果外部数据发生变化，这将导致函数输出不同的结果
*   副作用和副作用都会使函数变得不纯。任何调用不纯函数的函数都是不纯的。
*   纯函数没有副作用或副因。它们在应用程序的可预测性、测试和推理方面更受青睐。
*   赞成价值不变性。请注意，函数参数中的数组和对象是通过引用而不是通过值来引用的。制作数据结构的新副本，以避免改变引用。

管理副作用和函数的纯度是函数式编程的一个重要部分。它们允许更健壮和可测试的代码。下周我们将深入讨论函子和单子。您可以在 [Twitter](https://twitter.com/search?q=%2330DaysOfFP+from%3adslemay+since%3a2019-08-01) 上加入学习讨论。