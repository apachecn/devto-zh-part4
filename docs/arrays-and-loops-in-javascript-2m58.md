# JavaScript 中的数组和循环

> 原文：<https://dev.to/codetips/arrays-and-loops-in-javascript-2m58>

[![Arrays and Loops in JavaScript](img/4ab8dd78760e41ad537845c10ebd512e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dGo5YnNJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codetips.co.uk/conteimg/2019/07/arrays-and-loops-in-javascript.jpg)

在阅读本文之前，你应该对[数组](https://www.codetips.co.uk/what-are-arrays/)、[类型](https://www.codetips.co.uk/what-is-a-data-type/)和[循环](https://www.codetips.co.uk/what-are-loops/)有所了解。

在[“编写你的第一个 JavaScript 程序”](https://www.codetips.co.uk/writing-your-first-javascript-programme/)中，我们编写了一个输出**我的**姓名、年龄和职业的程序。

在“编写您的第一个 JavaScript 程序”文章的末尾，您被要求完成两个挑战(见下文)。

*   改变变量，让程序打印出你的名字、年龄和职业，而不是我的。
*   添加一个新变量`favoriteAnimal`，并将其合并到您输出的句子中。

要完成第二个挑战，您应该已经完成了如下内容:

```
const name = "Simon"
const age = 29
const profession = "Developer"
const favouriteAnimal = "dog"

console.log(`
  Welcome to my first JavaScript programme. 
  My name is ${name}. 
  I am ${age} years old. 
  I work as a ${profession}.
  My favourite animal is a ${favouriteAnimal}`
) 
```

在本文中，我们将扩展我们的程序，让它打印出我们所有的爱好。

我有三个主要爱好，所以显而易见的是创建三个新变量，就像我们创建`name`、`age`等变量一样。

```
const name = "Simon"
const age = 29
const profession = "Developer"
const favouriteAnimal = "dog"

const hobby1 = "Coding"
const hobby2 = "Walking"
const hobby3 = "Watching TV"

console.log(`
  Welcome to my first JavaScript programme. 
  My name is ${name}. 
  I am ${age} years old. 
  I work as a ${profession}.
  My favourite animal is a ${favouriteAnimal}`
)

console.log(`
  My hobbies are: ${hobby1}, ${hobby2}, ${hobby3}
`) 
```

现在运行我们的程序，使用我们之前讨论过的`node index.js`命令，将输出以下内容:

```
$ node index.js

Welcome to my first JavaScript programme. 
My name is Simon. 
I am 29 years old. 
I work as a Developer.
My favourite animal is a dog

My hobbies are: Coding, Walking, Watching TV 
```

我们增加了第二个`console.log`声明，它记录了我们的爱好。

这就是我们在这篇文章中想要做的，所以我们完成了，对不对？没有。虽然这对于三个爱好来说是可以接受的，但是如果我们有十个爱好呢？

```
const name = "Simon"
const age = 29
const profession = "Developer"
const favouriteAnimal = "dog"

const hobby1 = "Coding"
const hobby2 = "Walking"
const hobby3 = "Watching TV"
const hobby4 = "example4"
const hobby5 = "example5"
const hobby6 = "example6"
const hobby7 = "example7"
const hobby8 = "example8"
const hobby9 = "example9"
const hobby10 = "example10"

console.log(`
  Welcome to my first JavaScript programme. 
  My name is ${name}. 
  I am ${age} years old. 
  I work as a ${profession}.
  My favourite animal is a ${favouriteAnimal}`
)

console.log(`
  My hobbies are: ${hobby1}, ${hobby2}, ${hobby3}, ${hobby4}, ${hobby5}, ${hobby6}, ${hobby7}, ${hobby8}, ${hobby9}, ${hobby10}
`) 
```

现在看起来没那么干净了吧？你能想象如果我们有一百个爱好会是什么样子吗？

这种方法也很容易出错。想象一下，输入 100 个变量，并将它们添加到我们的`console.log`语句中。你确信你不会犯错误吗？即使是一个简单的拼写错误也可能在将来导致不可预见的情况。

那么肯定有更好的办法！？谢天谢地，有！[数组](https://www.codetips.co.uk/what-are-arrays/)！

在 JavaScript 中，数组可以包含[类型](https://www.codetips.co.uk/what-is-a-data-type/)的任意组合，并使用方括号(`[]`)声明。

**注意:**多类型数组，就像下面例子中的`mixedArr`，如果你认为你在处理一种类型，但实际上是在处理另一种类型，可能会导致不可预见的情况，所以如果你需要使用它们，要格外小心。

```
const stringArr = ['string1', 'string2', 'string3']
const intArr = [1, 2, 3]
const boolArr = [true, false, false]

const mixedArr = ['string1', 2, false, 'string2', 5, true] 
```

在我们继续阅读本文之前，请尝试完成以下挑战:

*   取十个爱好变量(`hobby1`、`hobby2`等)并将它们转换成一个`hobbies`数组。
*   修改`console.log`语句，打印出新的数组，而不是十个变量。

完成了吗？很好，做得好。您应该得到与此非常相似的结果:

```
const name = "Simon"
const age = 29
const profession = "Developer"
const favouriteAnimal = "dog"

const hobbies = ["Coding", "Walking", "Watching TV", "example4", "example5", "example6", "example7", "example8", "example9", "example10"]

console.log(`
  Welcome to my first JavaScript programme. 
  My name is ${name}. 
  I am ${age} years old. 
  I work as a ${profession}.
  My favourite animal is a ${favouriteAnimal}`
)

console.log(`
  My hobbies are: ${hobbies}
`) 
```

现在运行应用程序(`node index.js`)应该会得到以下输出:

```
$ node index.js

Welcome to my first JavaScript programme. 
My name is Simon. 
I am 29 years old. 
I work as a Developer.
My favourite animal is a dog

My hobbies are: Coding,Walking,Watching TV,example4,example5,example6,example7,example8,example9,example10 
```

我们现在有了一个数组，可以添加或删除爱好，而不必创建新的变量或修改`console.log`语句。

最好是在不同的行中记录每个爱好。

还记得“[什么是数组](https://www.codetips.co.uk/what-are-arrays/)”这篇文章中，数组元素有一个对应的数字，称为`index`。

JavaScript 数组索引从 0 开始，所以我们的爱好数组可以表示为:

```
index = [0 , 1 , 2 , ...]
array = ["Coding", "Walking", "Watching TV", ...] 
```

因此，我们可以通过定位各自的索引，在不同的行上记录我们的每个爱好。

```
const name = "Simon"
const age = 29
const profession = "Developer"
const favouriteAnimal = "dog"

const hobbies = ["Coding", "Walking", "Watching TV", "example4", "example5", "example6", "example7", "example8", "example9", "example10"]

console.log(`
  Welcome to my first JavaScript programme. 
  My name is ${name}. 
  I am ${age} years old. 
  I work as a ${profession}.
  My favourite animal is a ${favouriteAnimal}`
)

console.log(`
  My hobbies are:

  ${hobbies[0]}
  ${hobbies[1]}
  ${hobbies[2]}
  ${hobbies[3]}
  ${hobbies[4]}
  ${hobbies[5]}
  ${hobbies[6]}
  ${hobbies[7]}
  ${hobbies[8]}
  ${hobbies[9]}
`) 
```

但是现在，如果我们从`hobbies`数组中添加或删除一个项目，我们就必须更新`console.log`语句。我们倒退了！

但是，不用担心；一切都没有失去！我们可以用[循环](https://www.codetips.co.uk/what-are-loops/)来解决这个问题。

我们将把我们所有的爱好打印在不同的行上，一切都将通过`hobbies`数组来控制。这意味着不再需要为每次更改手动编辑`console.log`语句！

在我们开始在程序中创建循环之前，让我们举一个简单的循环例子来理解这个概念。

如果我们想要记录数字 1-10，我们可以编写 10 个不同的`console.log`语句，这非常繁琐，或者我们可以编写一个循环来完成这项工作。在以下示例中:

1.  `initialization`声明。在循环开始之前计算一次。我们定义了一个新变量(`i`)，并给它赋值 1(`let i = 1`)。
2.  `condition`的表情。每次循环迭代前要计算的表达式。它检查表达式(`i <= 10`-`i`是否小于或等于 10？)评估为真，如果是，则执行`statement`(见下文)。
3.  `final-expression`。每次循环迭代结束时要计算的表达式。这发生在下一次评估条件之前。我们简单地增加`i`的值(例如，如果`i`的值是 5，运行`i++`将增加它，现在它将是 6)。
4.  `statement`。只要`condition`表达式的计算结果为真，就会执行的语句。我们用它将`i`的值记录到屏幕上。

```
for (let i = 1; i <= 10; i++) {
  console.log(i)
} 
```

我们现在可以使用这种新技术来打印我们的爱好。在以下示例中:

1.  我们增加了一个新的`console.log`声明，打印出我们有多少爱好。我们通过使用我们的`hobbies`数组的`length`属性来实现这一点，顾名思义，该属性返回数组中元素的长度或数量。
2.  我们定义了我们的`initialization`语句，其中我们将变量`i`的值设置为`0`。我们将它设置为`0`，而不是`1`，因为 JavaScript 数组从零开始，正如我们之前讨论的那样。
3.  我们定义了我们的`condition`表达式，其中我们声明当`i`小于我们的`hobbies`数组(`i < hobbies.length`)的长度时，循环应该继续。
4.  我们定义了我们的`final-expression`，其中我们增加了`i` ( `i++`)的值，就像我们在前面的例子中所做的那样。
5.  然后，我们使用元素索引打印我们的爱好数组的每个元素。

```
const name = "Simon"
const age = 29
const profession = "Developer"
const favouriteAnimal = "dog"

const hobbies = ["Coding", "Walking", "Watching TV", "example4", "example5", "example6", "example7", "example8", "example9", "example10"]

console.log(`
Welcome to my first JavaScript programme. 
My name is ${name}. 
I am ${age} years old. 
I work as a ${profession}.
My favourite animal is a ${favouriteAnimal}
`)

console.log(`I have ${hobbies.length} hobbies, and they are as follows...`)

for (let i = 0; i < hobbies.length; i++) {
  console.log(hobbies[i])
} 
```

在解释每一步时，我说过,`condition`表达式表示当`i`小于我们的爱好数组的长度时，循环应该继续。

但为什么要小于长度(`i < hobbies.length`)，又不能小于等于长度(`i <= hobbies.length`)？因为 JavaScript 数组索引从零开始。

让我们回到前面的数组表示:

```
index = [0 , 1 , 2]
array = ["Coding", "Walking", "Watching TV"] 
```

当循环遍历这个数组时，我们只想访问索引元素`0`、`1`和`2`，这就是为什么我们的`initialization`语句将`i`设置为`0`。

但是，数组(`array.length`)的`length`是 3(即 3 个元素)。

因此，如果我们将我们的`condition`表达式声明为小于或等于长度(`i <= hobbies.length`)，我们将尝试访问索引位置`0`、`1`、`2`和`3`的元素。从数组表示中可以看出，没有第三个索引！

如果我们现在运行我们的程序，我们将看到以下输出:

```
$ node index.js

Welcome to my first JavaScript programme. 
My name is Simon. 
I am 29 years old. 
I work as a Developer.
My favourite animal is a dog

I have 10 hobbies, and they are as follows...
Coding
Walking
Watching TV
example4
example5
example6
example7
example8
example9
example10 
```

我们可以在我们的`hobbies`数组中添加和删除元素，我们的程序会解决剩下的问题！

我们知道这是一个很大的信息量，所以如果有任何不明白的地方，请随时联系我们。

* * *

[CodeTips](https://www.codetips.co.uk) 致力于帮助零经验或很少经验的初学者学习编码。

我们确实会交叉发布到其他网站以获得更广泛的受众，但是为什么不订阅我们的时事通讯并把最新的文章直接发到你的邮箱里呢？

此内容的原始来源是[代码提示](https://www.codetips.co.uk)。原始内容保持最新，但其他来源可能不是最新版本。