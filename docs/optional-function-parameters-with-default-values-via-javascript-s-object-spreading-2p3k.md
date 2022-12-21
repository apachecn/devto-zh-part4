# 通过 JavaScript 的对象传播，可选函数参数具有默认值

> 原文：<https://dev.to/drewtownchi/optional-function-parameters-with-default-values-via-javascript-s-object-spreading-2p3k>

在 [drewtown.dev](https://www.drewtown.dev) 上看到这篇文章和更多类似的文章，或者在 [Twitter 上关注我](https://twitter.com/drewtown_chi)🐤

很可能在某些时候，你不得不处理一个有 4 个参数和 10 个可选参数的函数。大多数时候，你永远不需要接触可选参数，生活是伟大的。但是，有时，您需要更改第 7 个可选参数。所有这些灵活性都是有代价的，可选参数通常被用来引导您走向正确的方向。更改第 7 个可选参数意味着指定可选参数 1 到 6，即使您一直想使用缺省值！

我们将看看如何使用对象和 spread 操作符来提供良好的默认值，同时仍然允许以更简单的方式覆盖。

ES2018 增加的功能之一是使用对象文字进行对象传播。对象扩散是一个有用的新功能，可以帮助解决上述问题。扩散允许开发者复制一个对象，然后将它扩散到另一个对象中，从而创建一个新老对象的副本。

```
const newData = { b: 3, c: 4 }
const data = { a: 2, ...newData} // data === { a: 2, b: 3, c:4 } 
```

如何执行复制的一个关键方面是，当一个对象扩展到另一个对象时，在声明中后面出现的属性将被用在前面声明的属性之上。

```
const newData = { a: 10, b: 3, c: 4 }
const data = { a: 2, ...newData} // data === { a: 10, b: 3, c:4 } 
```

即使`a: 2`是直接在`data`对象中创建的，它也会被扩散到对象中的`newData`覆盖。这对于在函数中设置默认值，然后允许函数调用方用其他值覆盖它们非常有用。

首先，我们来看看老的做事方式。

## 古老的方式

这是我们可以用默认值设置函数参数的许多方法之一的一个例子。

在我们的示例应用程序中，我们需要创建一个猫的数组。我们的函数将为调用者提供一种提供名称和颜色的方法。可选地，呼叫者将能够设置猫吃的食物类型以及他们最喜欢的玩具。

```
function createCat(name, color, foodType = "dry", favoriteToy = "yarn") {
  return {
    name,
    color,
    foodType,
    favoriteToy
  }
} 

console.log(createCat("Tom", "grey")); // { name: 'Tom', color: 'grey', foodType: 'dry', favoriteToy: 'yarn' } 
```

现在，在这个例子中，如果我们想把 Tom 最喜欢的玩具改成“ball ”,我们就需要修改我们的函数调用来指定`foodType`和`favoriteToy`,即使我们想使用食物的默认值。这是因为没有办法通过名称来指定可选参数，顺序很重要并且是必需的。

## 更新功能

让我们更新函数以接受名为`options`的参数，而不是在函数签名中指定可选参数。

```
function createCat(name, color, options) {
  return {
    name,
    color,
    foodType: "dry",
    favoriteToy: "yarn",
    ...options
  }
} 

console.log(createCat("Tom", "grey")); // { name: 'Tom', color: 'grey', foodType: 'dry', favoriteToy: 'yarn' } 
```

现在，我们可以像以前一样通过传递可选参数一样传递“Tom”和“grey”来获得相同的结果。在这种情况下，我们接受一个选项参数，并保留它`undefined`。这很好，spread 操作符不会抛出错误。

现在，如果我们想将 Tom 最喜欢的玩具改为“ball ”,我们可以向函数`createCat`提供一个对象，该对象指定了属性`favoriteToy`以及我们更新后的值。一切都无需指定一个`foodType`！

```
createCat("Tom", "grey", { favoriteToy: "ball" }); 
```

## 真实世界的例子

不难想象，在现实世界的应用程序中，有许多默认参数可以这样更新。例如，在处理表格时，格式选项通常非常相似。大多数情况下，它们的大小都相同，排列也相同。但是，您可能希望所有数字单元格都右对齐，以便于扫描。

我们可以使用这些接受可选参数的函数。更好的是，我们可以将它们组合在一起，为用户创建一个更友好的 API。在我们的例子中，我们希望所有的数字单元格都相同，我们可以创建一个新的函数`createTableNumberCell`，它将把预定义的选项传递给一个`createTableCell`。

```
 createTableCell(value, options) {
  return {
    value,
    align: "left",
    ...options
  }
}

createTableNumberCell(value) {
  return createTableCell(value, {align: "right"});
} 
```

像这样将函数组合在一起通常更容易使用，并且不需要用户记住各种选项或者总是参考文档。

## 弊端

使用这种方法有一些我们应该解决的缺点。

### 没有深度克隆

在 JavaScript 中，对象是引用，spread 操作符只复制正在被展开的对象的第一层。这意味着，如果您想在覆盖中使用嵌套对象，可以在将覆盖传递到函数中后更改这些覆盖。这通常不是您的意图，应该避免或格外小心地处理。

```
const overrides = {
  prop: {
    itemA: 1
  },
  itemB: 2
}

function makeCell(title, overrides) {
  return {
    title,
    ...overrides
  }
}

const cell = makeCell("New Cell", overrides);
console.log(cell); // { title: 'New Cell', prop: { itemA: 1 }, itemB: 2 }

// Whoops
overrides.prop.itemA = "ABCD";
console.log(cell); // { title: 'New Cell', prop: { itemA: 'ABCD' }, itemB: 2 } 
```

### 需要知道可用的选项/函数内部

与显式使用函数参数相比，函数的用户可能很难知道可用选项是什么。用户将需要深入研究该功能，看看有哪些选项可用，以及它们应该如何设置，因为这不是立即显而易见的。

这可以通过使用前面讨论的可组合函数来部分弥补。这允许您在为更复杂的场景提供逃生出口的同时，使更简单的用例更加简化。