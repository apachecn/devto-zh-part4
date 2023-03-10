# ES6 析构初学者指南

> 原文：<https://dev.to/themarcba/beginner-s-guide-to-es6-destructuring-ein>

交叉发布自 [developer.blog](https://developer.blog/es6-destructuring) 🥳

ES6 有很多新特性，其中之一就是析构。这是一种从对象和数组中提取项目的非常方便的方法，一旦理解了，可以使你的代码更加整洁，可读性更好。

我们开始吧！

## 先事先☝️

在这篇文章中，我们会看到很多类似如下的代码:

```
const { property1, property2 } = object 
```

Enter fullscreen mode Exit fullscreen mode

*   左边的是变量**被赋值**
*   右边的**是信息来源的**源****

## 破坏物体📦

假设我们有一个对象`movie`，它包含`id`、`title`、`year`作为属性:

```
const movie = {
    id: 99,
    title: 'The Matrix',
    year: 1999
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们要用传统的方法提取这个物体的属性，我们必须做这样的事情:

```
const id = movie.id
const title = movie.title
const year = movie.year 
```

Enter fullscreen mode Exit fullscreen mode

为了让你的代码更具可读性，你可以使用**ES6 方式** :

```
const { id, title, year } = movie
console.log(id, title, year); // 99 The Matrix 1999 
```

Enter fullscreen mode Exit fullscreen mode

您将获得与示例中 3 行代码完全相同的结果。结果，你有三个变量`id`、`title`、`year`，每个变量包含来自对象`movie`的各自的值。

> 重要的是要知道**变量名和对象属性必须是相同的**！

### 使用不同的变量名

如果您不能或不想使用与属性相同的变量名(例如，如果您已经有一个同名变量在使用中)，您可以**添加一个冒号并指明所需的变量名** :

```
const { originalPropertyName:newPropertyName } = object

// Example 👇
const { title:movieTitle, year:releaseYear } = movie 
```

Enter fullscreen mode Exit fullscreen mode

### 定义默认值

如果你想在一个析构属性未定义的情况下填充一个默认值，你可以在默认值:
后加上`=`

```
const { title, rating = 3 } = movie 
```

Enter fullscreen mode Exit fullscreen mode

我们没有在我们的`movie`对象中定义一个`rating`属性，所以它通常是`undefined`。但是当我们使用缺省值语法时，`rating`变量应该有 3，以防它还没有。

### 在函数参数中使用析构

```
const printMovie = ({ title, year, rating }) => {
    // Work directly with the destructed properties
    console.log(`The movie ${title} (${title}) has a ${rating} rating`)
    console.log(`⭐️`.repeat(Math.floor(rating)))
} 
```

Enter fullscreen mode Exit fullscreen mode

### 从嵌套对象中提取

如果您有嵌套的对象，您可以应用相同的原则，只是...井*嵌套*。

```
const character = {
    movie: 'The Matrix',
    name: 'Thomas A. Anderson',
    alias: 'Neo',
    actor: {
        firstname: 'Keanu',
        lastname: 'Reeves'
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你只对这个电影角色的演员感兴趣，你可以应用嵌套析构:

```
const { actor: { firstname, lastname } } = character
console.log(firstname, lastname) // Keanu Reeves 
```

Enter fullscreen mode Exit fullscreen mode

## 破坏阵列⛓

ES6 还定义了一些巧妙的数组析构方式。

先来看看老办法:

```
const food = ['🍕', '🌭', '🍔', '🍍', '🍓']
const pizza = food[0]
const hotdog = food[1]
const burger = food[2]
console.log(pizza, hotdog, burger) // 🍕 🌭 🍔 
```

Enter fullscreen mode Exit fullscreen mode

在 ES6 中，您可以获得如下值:

```
const food = ['🍕', '🌭', '🍔', '🍍', '🍓']
const [pineapple, strawberry] = food
console.log(pineapple, strawberry) // 🍍 🍓 
```

Enter fullscreen mode Exit fullscreen mode

可能有意思的是:

> 你可以析构任何 *[可迭代](https://javascript.info/iterable)* 的东西。这包括字符串。

```
const fruitSalad = '🍏🍌🍐🍒🍇'
const [s, a, l, a, d] = fruitSalad
console.log(d,a,l,a,s) // 🍇🍒🍐🍌🍏 
```

Enter fullscreen mode Exit fullscreen mode

### 忽略数组中的项目

当析构数组时，可以忽略您可能不感兴趣的值。另外:你可以跳过任意多的项目。

```
const breakfast = ['🥐','🍳', '🧀','🥓', '🥖']

const [croissant, eggs,, bacon] = breakfast
console.log(croissant, eggs, bacon) // 🥐🍳🥓

const [,,cheese,,baguette] = breakfast
console.log(cheese, baguette) // 🧀🥖 
```

Enter fullscreen mode Exit fullscreen mode

### 使用*休息*操作符

如果你有一个数组，你想得到变量中的某些项，而其他的在一个单独的数组中，你可以使用[展开语法【T4(`...rest`):](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) 

```
const food = ['🥐', '🥞', '🥦', '🍆', '🍅']
const [croissant, pancakes, ...veggies] = food
console.log(croissant, pancakes) // 🥐🥞
console.log(veggies) // ["🥦", "🍆", "🍅"] 
```

Enter fullscreen mode Exit fullscreen mode

### 使用析构交换变量

一个方便的交换变量的技巧是，使用析构来这样做。假设你有变量 x 和 y，每个都有一个值:

```
let x = 4711
let y = 1337 
```

Enter fullscreen mode Exit fullscreen mode

要交换它们，你可以使用一个临时变量:

```
let temp = x
x = y
y = temp 
```

Enter fullscreen mode Exit fullscreen mode

但那不干净也不可读。ES6 析构提供了一个交换这些数字的好方法:

```
[x, y] = [y, x]
console.log(x, y) // 1337 4711 
```

Enter fullscreen mode Exit fullscreen mode

## 总结🙌

正如您在示例中看到的，析构是使代码更短、可读性更好的好方法。所以每当你发现自己在重复类似
的事情时

```
this.value1 = anotherObject.value1
this.value2 = anotherObject.value2
this.value3 = anotherObject.value3
this.value4 = anotherObject.value4 
```

Enter fullscreen mode Exit fullscreen mode

你有机会应用析构。

所以，开始你最近的宠物项目，看看是否有你可以应用这些知识的地方😊