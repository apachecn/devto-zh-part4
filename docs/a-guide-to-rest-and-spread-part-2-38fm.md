# 休息和伸展指南:第二部分

> 原文：<https://dev.to/guin/a-guide-to-rest-and-spread-part-2-38fm>

在第 1 部分中，我们简要解释了 rest 参数和 spread 操作符之间的区别，并深入研究了 rest 参数。这篇文章将继续深入展开操作符。下面是第 1 部分的快速解释，以便快速复习概念:

**Rest 参数:**将所有剩余的元素或参数收集到一个数组中。

**Spread operator:** 允许将数组、字符串等可迭代对象展开成单个参数或元素。对象表达式将被扩展成另一个对象。

## 潜入传播算子

在上一篇文章中，我们看到 rest 操作符获取一些单独的元素或参数，并将它们收集到一个数组中。扩展运算符本质上具有相反的功能。它接受一个 iterable 并将其扩展成多个单独的元素或参数。让我们看看它在代码中是什么样子，以及在什么情况下使用它会有帮助。

### 使用 spread 运算符配合:

*   **一个字符串**会给你这个字符串的单个字符

```
let aString = "spread string"
console.log(...aString) // s p r e a d  s t r i n g 
```

*   **一个数组**会给你数组中的单个元素

```
let anArray = ["puppies", "kittens", 5, null]
console.log(...anArray) // puppies kittens 5 null 
```

*   **一个物体**会给你

```
let anObject = {"name": "Angelika",
                "hairColor": "purple"}
console.log(...anObject) // Whoops we get a TypeError because anObject is not an iterable
console.log({...anObject}) // but we can spread it into another object and get
// {"name": "Angelika", "hairColor": "purple"} 
```

现在我们已经了解了 spread 操作符的基本功能，让我们看看如何在代码中使用它。

### 传播算子用例:

#### 复制数组

如果我们想复制一个数组，我们可以把这个数组扩展到另一个数组中，就像这样:

```
let anArray = ["puppies", "kittens", 5, null]
let copyOfAnArray = [...anArray]
console.log(copyOfAnArray) // ["puppies", "kittens", 5, null] 
```

#### 组合数组

我们可以使用 spread 操作符将数组组合成一个包含所有单个元素的新数组，如下:

```
let dogs = ["beagle", "corgi", "labrador", "poodle"]
let cats = ["persian", "ragdoll", "munchkin", "siamese"]
let animals = [...dogs, ...cats] 
console.log(animals) // ["beagle", "corgi", "labrador", "poodle", "persian", "ragdoll", "munchkin", "siamese"] 
```

如果我们在这里不使用 spread 操作符，我们将得到一个包含两个数组的数组。

```
animals = [dogs, cats]
console.log(animals)
// [["beagle", "corgi", "labrador", "poodle"], ["persian", "ragdoll", "munchkin", "siamese"]] 
```

#### 将数组元素作为参数传递给函数

如果我们有一个元素数组，并且我们想使用这些元素作为函数的参数，我们可以使用 spread 语法，而不是使用`apply`方法。在下面的水果函数中，我们有 3 个描述水果类型的参数。我们有一个 args 数组，我们想用它的元素作为函数的参数。通过在 args 数组上使用 spread 运算符，元素将作为参数传递给函数，而不是将整个数组作为一个参数传递。

```
function fruits(citrusFruit, stoneFruit, berry) {}
var args = ["lemon", "plum", "strawberry"]
fruits(...args) 
// this is equivalent to calling fruits like so: 
// fruits("lemon", "plum", "strawberry")
fruits(args)
// this is equivalent to calling fruits like so:
// fruits(["lemon", "plum", "strawberry"])
// which is not what we want 
```

利用以上知识，我们可以看到这是如何对数学函数
有用的

```
let numbers = [10, 99, 5, 23, 4]
Math.min(...numbers) // 4
Math.max(...numbers) // 99 
```

#### 物体扩散

我们可以使用 spread 在另一个对象中创建一个对象的浅层副本，并在不改变原始对象的情况下向其添加属性。在下面的例子中，我们有一个对象`primitiveTypes`，它有一些原始类型的例子作为键值对。然后我们创建一个新对象`dataTypes`,它使用 spread 操作符将`primitiveTypes`中的所有属性扩展到数据类型中，我们还添加了一个属性。

```
const primitiveTypes = { "number" : 4, 
                         "string": "pizza",
                         "boolean" : true,
                         "null": null,
                         "undefined" : undefined, 
                        };              

const dataTypes = {...primitiveTypes, 
                   "array": ["stuff", "things"]
                   };

/* dataTypes object will be:
 { "number" : 4, 
   "string": "pizza",
   "boolean" : true,
   "null": null,
   "undefined" : undefined, 
   "array": ["stuff", "things"]
}
*/ 
```

我们也可以使用 spread 将两个对象合并在一起，就像这样:

```
const obj1 = {"one": 1, "two" : 2}
const obj2 = {"three": 3, "four": 4}
const mergedObj = {...obj1, ...obj2}
// mergedObj will be
/* {"one": 1, "two" : 2, "three": 3, "four": 4} */ 
```

然而，我们必须小心使用对象的传播，因为我们可以覆盖一些我们不想要的东西，如果我们得到了错误的顺序。注意在下面的例子中，`otherFruits`和`differentFruits`中的`citrus`键的值发生了什么变化，这取决于我们使用 spread 操作符来扩展原始水果对象的位置。

```
const fruits = {
                "citrus": "lemon",
                "stone": "plum"
                } 
```

顺序决定了一个键是否被覆盖！

```
const otherFruits = {
                     "citrus" : "lime",
                     "berry": "strawberry",
                     ...fruits
                    }
console.log(otherFruits) // {citrus: "lemon", berry: "strawberry", stone: "plum"}

const differentFruits = {
                         ...fruits, 
                         "citrus": "lime",
                         "berry": "strawberry"
                         }
console.log(differentFruits) // {citrus: "lime", stone: "plum", berry: "strawberry"} 
```

希望您已经对 spread 运算符在 JavaScript 中的作用及其一些用例有了更好的理解。和往常一样，更好地理解这些概念的最好方法之一就是练习使用它们！

如果您有任何问题、意见或反馈，请告诉我。关注每周关于 JavaScript、React、Python 和 Django 的新帖子！