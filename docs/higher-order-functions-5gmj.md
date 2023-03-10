# 高阶函数

> 原文：<https://dev.to/critjen/higher-order-functions-5gmj>

在参加了一个函数式编程会议后，我决定探索 JavaScript 中的一些函数式编程概念。因此，对于许多我正在探索高阶函数的第一个。

## 那么什么是高阶函数呢？

高阶函数是这样一种函数，它要么接受另一个函数作为参数，要么返回一个函数。因此，在我们进行分解之前，需要注意的是:

### 函数是一级对象

[![](img/7857ab13066244369e6564c49bed6838.png)](https://i.giphy.com/media/l2Jecm1l0wnJ2kQDu/giphy.gif)

这意味着函数在 JavaScript 中被视为对象。这就是为什么你可以把它们存储在变量中，并在周围传递。正是语言的这一特性允许我们创建更高阶的函数！

**所以你可以把它们存储在变量**
中

```
const catSound = function meow() { console.log("Meow") } 
```

然后你可以传递这些变量。如果我们需要一个更个性化的功能给我们心爱的猫“络腮胡先生”,这很容易

```
const mrWhiskersSound = catSound 
```

所以现在如果我们调用 mrwhiskersound

```
mrWhiskersSound() 
```

“喵”将被记录到控制台，就像我们调用了我们的喵功能一样。

*你也可以在一个对象中存储一个函数*

```
let dogSound = { bark : function(){} } 
```

*你可以在一个数组中存储一个函数*

```
let fishSound.push(function blurp() {}) 
```

**函数可以作为参数传入**

您可以将一个函数作为参数传递给另一个函数。

```
function dangleString() {
    console.log("Dangles a piece of string high in the air");
}

function fetch() {
    console.log("Throws the stick");
}

function play(pet, dangleString, fetch){
   if(pet === "dog"){
      fetch();
   } else if (pet === "cat"){
      dangleString();
   } else {
      return "Back away slowly from unknown creature";
   }
} 
```

[![](img/e4a10f8c81db253a1dee140428bb344c.png)](https://i.giphy.com/media/k2eCjTHkZ6r4Y/giphy.gif)

这里我们定义了两个独立的函数，dangleString 和 fetch。然后我们将这两个函数作为参数传递给第三个函数 play。如果我们调用 play 函数并传入一只猫作为我们的宠物参数，那么 dangleString 函数将会执行。如果我们传入一只狗，取而代之的是 fetch 函数会执行，你会明白的。因为 play 接受函数作为参数，所以它是一个高阶函数。我们做到了！

高阶函数还有第二种情况，即函数返回函数。

```
function badExample() {

   function learn() {
      console.log("I'm leaning stuff");
   }

   return learn;
} 
```

所以这不是最有用的例子，但它很好地展示了这个概念。badExample 函数调用的返回值将是 learn 函数。所以您可以将返回值保存到一个变量中，并在以后调用它。

```
const learnFunc = badExample();
learnFunc(); 
```

这里，我们的 badExample 函数调用返回我们的 learn 函数，我们将这个返回值存储在一个名为 learnFunc 的变量中。当我们稍后调用 learnFunc 时，它将执行 learn 功能，并且“我正在学习”将被打印到控制台。又一个高阶函数！我们非常擅长这个。

所以这一切看起来很清楚，但并没有真正证明高阶函数是多么的有用。让我们至少看一个例子来说明为什么这是有用的。

JavaScript 有很多非常有用的内置高阶函数。Map、filter 和 reduce 是您以前可能使用过的一些非常常见的示例。所以不管你有没有意识到，你在用高阶函数。

### 地图

map 方法是数组原型上内置的高阶函数。那确实是一口。更直白地说，任何时候在 JavaScript map 中创建数组都是可以自动执行的操作之一。可爱的 JavaScript 人员已经为我们编写了逻辑，所以我们不需要这么做。值得注意的是，由于它是数组原型的一部分，这是我们与数组交互的一个选项，而不是其他数据类型。

当您使用 map 时，您传入一个数组和一个回调函数作为参数，map 通过遍历数组中的每个元素并执行您传入的任何函数来为您创建一个新数组。

**回调函数**:作为一个参数传递给一个高阶函数的函数，这个函数将在所有其他操作完成后执行。

假设我们面临着一个古老的问题，那就是给数组中的每个数字加 2(我经常遇到这种情况)。
[![](img/4e1bf1ca2efa6637d1a1c22e433813fa.png)](https://i.giphy.com/media/3owzW5c1tPq63MPmWk/giphy.gif) 
让我们省下所有的 for 循环废话和使用地图。

```
const nums = [1, 3, 4, 6]

const addTwo = num => num + 2

const newNums = nums.map(addTwo)

//newNums = [3, 5, 6, 8] 
```

所以我们从数字数组(num)开始。因为它是一个数组，所以我们可以对它使用 map，我们只需要传入回调函数。然后 Map 遍历并调用 nums 中每个元素的 addTwo 函数。因为我使用了一个没有花括号的箭头函数，所以新值会自动返回到 addTwo 中。我们也可以在行中定义回调函数。

```
const nums = [1, 3, 4, 6]
const newNums = nums.map(num => num + 2)
//newNums = [3, 5, 6, 8] 
```

这是一个非常有用的高阶函数的例子，也是传递函数作为参数的例子。本周我不会深入讨论 reduce 和 filter 的例子，因为这有点长。但是也许下周吧！

当我试图更好地掌握 JavaScript 中的函数式编程时，我还应该注意什么？我正在研究工厂功能，它们看起来很酷，但我不想太超前。