# 用迭代器协议使对象可遍历

> 原文：<https://dev.to/aminnairi/make-an-object-traversable-with-the-iterator-protocol-3ilo>

## 简介

这篇文章是我在 Dev.to 上写的这篇文章的更详细的版本。

[![aminnairi](img/abffb83b8c2919200fc771b6293c3d1f.png)](/aminnairi) [## 让我们建一个车库吧！

### Amin Aug 16 ' 194min read

#javascript #iterator #symbol](/aminnairi/let-s-build-a-garage-2mbn)

这将是基于一个类似的例子，所以如果你遵循之前所说的，你不应该在阅读这篇文章时迷失。

假设我有一个对象描述了一辆摩托车的一些规格。

```
const motorcycle = {
  brand: "Triumph",
  model: "Street Triple",
  year: 2018
} 
```

Enter fullscreen mode Exit fullscreen mode

我想遍历那辆摩托车的所有规格。一种方法是从 [`Object`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) 对象中使用 [`getOwnPropertyNames`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames) 方法。它返回一个我们可以迭代的数组。

```
for (const key of Object.getOwnPropertyNames(motorcycle)) {
  console.log(key)
}

// brand
// model
// year 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经有了对象的键名，我们可以很容易地使用[括号符号](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Property_Accessors#Bracket_notation)获得该属性的值。

```
for (const key of Object.getOwnPropertyNames(motorcycle)) {
  console.log(`${key}: ${motorcycle[key]}`)
}

// brand: Triumph
// model: Street Triple
// year: 2018 
```

Enter fullscreen mode Exit fullscreen mode

我将要向您展示的是一种将对象转换为可迭代对象的方法。这将是相当冗长的，所以我们将使用一个函数来包装这个行为，以便有一些可重用的东西，并轻松地将 N 个对象转换成可迭代的对象。

## 迭代器协议

我们说过我们想要一个函数来把任何对象变成一个可迭代的对象。让我们创建这个函数。

```
function toIterable(target) {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

这个函数要做的是添加一个特殊的属性，JavaScript 运行时会将其检测为迭代器。这种特殊的属性叫做 [`Symbol.iterator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/iterator) 。它的值是一个函数，当我们想要迭代这个对象时，这个函数就会运行。通常情况下， [`for...of`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) 循环会检查对象是否确实是迭代器，并在后台为我们运行这个特殊的函数。其他函数和习惯用法也会这样做，如 [`from`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from) 方法的 [`Array`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array) 对象。

```
function toIterable(target) {
  Object.defineProperty(target, Symbol.iterator, {
    value: function() {
      // ...
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们要做的是实现[迭代器协议](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)。把它看作一个接口，在那里你必须提供一种方法来表示你的对象的所有迭代。

在 JavaScript 中实现迭代器协议意味着返回一个以独特方式格式化的对象。该对象将包含一个名为`next`的方法，该方法由所有接受 iterable 对象的函数和习惯用法在内部使用，并将调用该函数来一个接一个地获取迭代。表示该模式的一种方式是使用下面的代码。

```
myObject[Symbol.iterator].next() // First iteration
myObject[Symbol.iterator].next() // Second iteration
myObject[Symbol.iterator].next() // undefined, meaning this is the last iteration 
```

Enter fullscreen mode Exit fullscreen mode

当你试图迭代一个数组时，这就是在幕后发生的事情。`for`循环只是围绕这一行为的一个语法糖。但是没人有时间做这个...

让我们试着在函数中实现这个行为。

```
function toIterable(target) {
  Object.defineProperty(target, Symbol.iterator, {
    value: function() {
      // ...

      const iterator = {
        next() {
          // ...
        }
      }

      return iterator
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经有了我们的结构，我们必须告诉函数`next`当有东西请求迭代我们的对象时如何表现。这是事情变得特定于一个或另一个对象的地方。我在这里要做的是一个非常简单的例子，我们可以返回什么，但是当然你可能想要为特殊的对象添加一些特殊的行为。

```
function toIterable(target) {
  Object.defineProperty(target, Symbol.iterator, {
    value: function() {
      // ...

      const iterator = {
        next() {
          // ...

          return { done: true, value: undefined }
        }
      }

      return iterator
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

迭代器协议指定了`next`方法应该返回的值的格式。它是一个对象，包含两个属性:

*   一个`done`属性，它将告诉执行人我们是否完成了。这意味着当我们完成迭代时，我们返回`done: true`，当我们没有完成时，我们返回`done: false`。相当直接。
*   一处房产。当然，如果对象没有值要返回，那么循环就没有意义。在这里，您将有机会对循环收集的值进行格式化。发挥创造力，在这里做一些特别的事情，或者简单一点，只返回一个简单的值。这就是我要做的。

值得注意的是，当返回最后一次迭代时，我们可以简单地将`value`属性设置为`undefined`,因为这仅由循环内部使用，以了解我们是否正在完成迭代，并且不会用于其他目的。

现在，我们可以添加一些定制逻辑，从一个对象收集属性，并返回每个属性的迭代。

```
function toIterable(target) {
  Object.defineProperty(target, Symbol.iterator, {
    value: function() {
      const properties = Object.getOwnPropertyNames(target)
      const length = properties.length

      let current = 0

      const iterator = {
        next() {
          if (current < length) {
            const property = properties[current]
            const value = target[property]

            const iteration = {
              done: false,
              value: `${property}: ${value}`
            }

            current++

            return iteration
          }

          return { done: true, value: undefined }
        }
      }

      return iterator
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

这里，我定义了一个名为`current`的索引变量，以了解我在迭代过程中的位置。我还收集了所有命名的属性，并将它们存储在`properties`变量中。为了知道什么时候停止，我需要知道我有多少属性和`length`变量。现在我所做的就是返回一个带有属性名和值的迭代，并增加当前索引。

这是我迭代对象的方式，你可以用完全不同的方式格式化你的值。也许你可以有一个`files`对象，并使用 [`fs.readFile`](https://nodejs.org/api/fs.html#fs_fs_readfile_path_options_callback) 来读取文件的内容，然后在迭代中返回它。跳出框框思考，发挥创造力！实际上，我认为这将是一个很好的练习，让读者实现一个`fileReaderIterator`函数，如果你使用 [Node.js](https://nodejs.org/en/) 的话，这个函数就能做到。

当然，将所有这些放在一起会得到与之前相同的结果。

```
toIterable(motorcycle)

for (const characteristic of motorcycle) {
  console.log(characteristic)
}

// brand: Triumph
// model: Street Triple
// year: 2018 
```

Enter fullscreen mode Exit fullscreen mode

尽管我们写了很多代码，但是这些代码现在可以在所有我们想要成为 iterable 的对象中重用。这也有利于使我们的代码比以前更可读。

## 发电机

我们看到的是创建 iterable 的工作方式。但是这就像之前说的那样有点拗口。一旦理解了这个概念，我们就可以使用一个[生成器函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)为这种目的使用更高层次的抽象。

生成器函数是一个特殊的函数，它总是返回一个迭代。这是对我们之前看到的所有内容的抽象，帮助我们编写更简单的迭代器，为内部逻辑而不是迭代器协议实现留出更多空间。

让我们用这个新语法重写我们之前写的内容。

```
function toIterable(target) {
  Object.defineProperty(target, Symbol.iterator, {
    value: function*() {
      for (const property of Object.getOwnPropertyNames(target)) {
        const value = target[property]

        yield `${property}: ${value}`
      }
    }
  })
} 
```

Enter fullscreen mode Exit fullscreen mode

注意关键字`function`后面的星号。这就是 JavaScript 运行时从生成器函数中识别常规函数的方式。同样，我使用了 [`yield`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield) 关键字。这个特殊的关键字是对我们以前手工编写的迭代的抽象。它为我们返回一个迭代对象。很酷不是吗？

当然，这也将与我们之前的行为完全一样。

```
for (const characteristic of motorcycle) {
  console.log(characteristic)
}

// brand: Triumph
// model: Street Triple
// year: 2018 
```

Enter fullscreen mode Exit fullscreen mode

## 可迭代类

你曾经想要迭代一个对象吗？假设我们有一个处理车辆列表的类`Garage`。

```
class Garage {
  constructor() {
    this.vehicles = []
  }

  add(vehicle) {
    this.vehicles.push(vehicle)
  }
}

const myGarage = new Garage()

myGarage.add("Triumph Street Triple")
myGarage.add("Mazda 2")
myGarage.add("Nissan X-Trail") 
```

Enter fullscreen mode Exit fullscreen mode

像这样迭代我们的车库可能是有用的:

```
for (const vehicle of myGarage) {
  console.log(`There is currently a ${vehicle} in the garage`)
}

// TypeError: myGarage is not iterable 
```

Enter fullscreen mode Exit fullscreen mode

啊哦...真遗憾。如果这行得通，那该多好啊...但是等一下，我们可以让它工作！感谢迭代器协议和生成器。

```
class Garage {
  constructor() {
    this.vehicles = []
  }

  add(vehicle) {
    this.vehicles.push(vehicle)
  }

  *[Symbol.iterator]() {
    for (const vehicle of this.vehicles) {
      yield vehicle
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我在这里使用的只是我们上面所做的一个简写语法，并且具有完全相同的效果:它定义了一个名为`Symbol.iterator`的属性，这是一个从我们的对象返回迭代的生成器函数。简而言之，我们只是让我们的对象可迭代。

```
for (const vehicle of myGarage) {
  console.log(`There is currently a ${vehicle} in the garage`)
}

// There is currently a Triumph Street Triple in the garage
// There is currently a Mazda 2 in the garage
// There is currently a Nissan X-Trail in the garage 
```

Enter fullscreen mode Exit fullscreen mode

但这并不止于此。我们也可以使用每一个以 iterable 作为参数的方法。例如，我们可以过滤掉所有只乘坐凯旋摩托车的车辆。

```
Array.from(myGarage).filter(function(vehicle) {
  return vehicle.includes("Triumph")
}).forEach(function(triumph) {
  console.log(triumph)
})

// Triumph Street Triple 
```

Enter fullscreen mode Exit fullscreen mode

我们开始吧。我们的实例现在已经成为可迭代的东西。我们现在可以使用所有链接到`Array`对象的强大方法来轻松操作我们的对象。