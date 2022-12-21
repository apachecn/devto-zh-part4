# 传播和解构:JavaScripters 开发者指南

> 原文：<https://dev.to/deleteman123/spread-and-destructuring-a-how-to-guide-for-javascripters-4poi>

### 更好地理解 JS 传播和解构

大多数常见的与 web 相关的编程语言都有所有的基本操作符，到了有工作机会的时候，每个开发人员都知道如何使用它们。但是有一些操作符并不常见，也不是每种语言都有，或者即使有，它们也可能没有相同的语法。对于一门语言的新手来说，这可能是一个问题，他们试图阅读他人的代码，却没有在技术之间映射概念所需的工具。在本文中，我将讨论其中的两个运算符:

*   部分原因是复杂文档，对于没有太多其他经验的开发人员来说，这可能是一个难以理解的概念。我指的不是别人，正是臭名昭著的传播运营商。

*   另一个操作符是一个非常符合逻辑的操作符，但是由于其他语言中没有这个操作符(Python 有这个操作符，这也是该语言的一个重要特性)，没有多少开发人员知道它。当然，我说的是解构。

让我们开始吃吧！

### 传符

spread 操作符(也就是你可以放在变量名前的 3 个点)可能是 JavaScript 中最容易被误解的操作符，这是一个真正的遗憾，因为一旦你在大脑中解开了它的机制，你就为你的武器库中添加了一个非常强大的工具。

和其他东西一样，让我们快速浏览一下来自 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) 的实际文档，这是 JavaScript 文档和快速示例的最佳来源之一:

> **扩展语法**允许在需要零个或多个参数(用于函数调用)或元素(用于数组文字)的地方扩展可迭代对象，例如数组表达式或字符串，或者在需要零个或多个键值对(用于对象文字)的地方扩展对象表达式。

那…那真的没什么帮助，所以让我重新措辞一下:

> **Spread 语法**允许您扩展当前分组在特定容器中的内容，并将其分配给不同的容器。兼容的容器包括:数组、字符串、对象和任何可迭代的对象(如映射、集合、类型数组等),它们的元素可以扩展成函数参数、数组元素和键值对

这样好些了吗？现在记住了第二个定义，让我给你看一些快速的例子，并希望在你现在扩展的编程头脑中巩固这些新的机制。

```
 let myArray1 = [1,2,3]
let myString = "Hey planet!"
let myObject = {
    name: "Fernando Doglio",
    age: 35,
    country: "Uruguay",
    [Symbol.iterator]: function* () { //we're making the object iterable so we can spread it
        yield myObject.name
        yield myObject.age
        yield myObject.country
    }
}

function test() {
    console.log(arguments)
}

let splitLetters = [...myString] //no longer need for myString.split()
console.log(splitLetters)
//[ 'H', 'e', 'y', ' ', 'p', 'l', 'a', 'n', 'e', 't', '!' ]

let objLetters = {...myString}
console.log(objLetters)
/*
{ '0': 'H',
  '1': 'e',
  '2': 'y',
  '3': ' ',
  '4': 'p',
  '5': 'l',
  '6': 'a',
  '7': 'n',
  '8': 'e',
  '9': 't',
  '10': '!' }
*/
test(...myString)
/*
[Arguments] {
  '0': 'H',
  '1': 'e',
  '2': 'y',
  '3': ' ',
  '4': 'p',
  '5': 'l',
  '6': 'a',
  '7': 'n',
  '8': 'e',
  '9': 't',
  '10': '!' }
*/
//the same thing
test.call(null, ...myArray1)
//[Arguments] { '0': 1, '1': 2, '2': 3 }
test.apply(null, myArray1)
//[Arguments] { '0': 1, '1': 2, '2': 3 }

let objValues = [...myObject] //if your object is iterable, this can substitute Object.values(myObject)
console.log(objValues)
//[ 'Fernando Doglio', 35, 'Uruguay' ]

let {name, age} = {...myObject} //spread properties into individual variables
console.log("Name::", name, " age::", age)
//Name:: Fernando Doglio  age:: 35

test(...myObject) //we've turned our object into 3 different arguments
//[Arguments] { '0': 'Fernando Doglio', '1': 35, '2': 'Uruguay' } 
```

花点时间通读示例及其各自的输出，我会在这里。

完成了吗？好，那我们复习一下。虽然这些例子中的一些只不过是操作员允许我们执行的花哨的技巧，但我们可以从这样一组基本的例子中获得一些有趣的信息:

*   通过用{}、[]或()包围我们的容器，我们指定了想要的目标(也就是说，我们要么扩展到一个新的对象、一个新的数组或一个参数列表)。

*   扩展一个字符串允许我们按字符分割它，这是我们一直用 string.split()做的事情。额外的好处是，我们可以决定是否需要数组、对象或参数格式的分割结果。

*   将数组作为 Function.call 方法调用的一部分展开，就不再需要 Function.apply 方法。** *额外提示* *:简单地将数组展开作为普通函数调用的一部分，就不再需要这两者了。

*   为了使用我的自定义对象，我必须让它*iterable。*虽然这不是一个大问题，但您需要记住这一点，否则操作员在大多数情况下都不会工作。

现在，让我快速向您展示一组更高级的、可以说是有用的东西，我们可以使用 spread 操作符实现这些东西:

```
 let array1 = [1,2,3,4]

//Copying an array
let copyArray = [...array1]
copyArray.push(4)
console.log(array1)
console.log(copyArray)
/*
[ 1, 2, 3, 4 ]
[ 1, 2, 3, 4, 4 ]
*/

//**WARNING*/
let otherArray = [[1], [2], [3]]
copyArray = [...otherArray]
copyArray[0][0] = 3
console.log(otherArray)
console.log(copyArray)
/*
Spread does a shallow copy
[ [ 3 ], [ 2 ], [ 3 ] ]
[ [ 3 ], [ 2 ], [ 3 ] ]
*/

//Array concats
let array2 = ['a', 'b', 'c']

let result = [...array1, ...array2]
console.log(result)
//[ 1, 2, 3, 4, 'a', 'b', 'c' ]

//**WARNING */
let myString = "hello world"
let result2 = [...array1, ...myString] //totally valid but...
console.log(result2)
//[ 1, 2, 3, 4, 'h', 'e', 'l', 'l', 'o', ' ', 'w', 'o', 'r', 'l', 'd' ]

result2 = array1.concat(myString) //maybe this is what you really want
console.log(result2)
//[ 1, 2, 3, 4, 'hello world' ]

result2 = [...array1, ...array2, myString] //or this is valid too
console.log(result2)
//[ 1, 2, 3, 4, 'a', 'b', 'c', 'hello world' ]

//Merging objects
let myObj1 = {
    name: "Fernando Doglio",
    age: 34
}

let myObj2 = {
    name: "Fernando Doglio",
    age: 35,
    country: "Uruguay"
}

let mergedObj = {...myObj1, ...myObj2}
console.log(mergedObj)
// { name: 'Fernando Doglio', age: 35, country: 'Uruguay' }

//Cleaning up repeated elements from an array
let myArray3 = [1,2,3,4,4,4,4,2,3,3,4,6]
let mySet = new Set(myArray3)
myArray3 = [...mySet]
console.log(myArray3)
//[ 1, 2, 3, 4, 6 ]

view rawadvanced-spread-examples.js hosted with ❤ by GitHub 
```

一些亮点:

*   克隆一个数组很简单，但是它是一个浅层拷贝，你可以在上面的例子中看到，多维数组是如何没有被完全克隆的。所以使用这种快捷方式时要小心。

*   合并数组也非常强大。尽管有一个警告，但是尽量不要用 spread 操作符直接替换 concat 方法调用，因为它们的行为与它们的值不同。也就是说，数组串联的扩展版本(如果操作正确)比方法调用版本更具声明性。

*   现在合并对象很简单。而在此之前，你必须执行某种循环，一边考虑键，另一边考虑值。不再需要，现在只需一行代码，就可以将几个对象合并成一个。考虑到当有一个键碰撞时，最右边的对象将覆盖先前的值。

*   最后，从数组中清除重复的元素终于变得像一开始应该做的那样简单了。当 Set 被添加到语言中时，我们都喜极而泣(好吧，至少我知道我做到了！).但是当意识到 Set.values 方法没有返回一个平面数组的时候，我又想哭了，但是原因完全不同。现在，我不再需要迭代结果，我可以简单地将集合分散到一个数组中，然后忘记它。

对于 spread operator 来说就是这样了，我希望上面的例子已经给了你足够多的肉来开始咀嚼。现在我将试着用析构来拓展你的思维，以及它对你的语法和代码意味着什么。

### 解构

JavaScript 的另一个有趣的新特性(我可以说，这是我第一次遇到 Python 时喜欢的一个特性)是*析构。*该语法允许我们将对象和数组中的值解包到单独的属性中。析构本身就很神奇，但是我们也可以把它和 spread 操作符混合起来，我们会得到一些有趣的结果。

诸如 Perl 或 Python 之类的语言非常重视它们基于列表的特性，因为面对现实吧，它们非常强大。谁没有在做以下事情时感到惊讶:

```
 a = 1
    b = 2
    a, b = b, a 
```

多少次你希望用 JavaScript 也能做到这一点？那么从一个函数中返回多个值呢？这总是意味着你必须返回一个数组或者一个对象，其中的值被打包，当然，之后要对它们进行相应的处理。

对于您来说，要编写一个返回多个值的通用函数而不在语法或语义级别做出一些妥协，基本上是不容易的(要么添加额外的代码来处理它，要么让您的开发人员知道您正在返回一堆数据，并让他们按照他们喜欢的方式来处理它)。

* Destructuring *为所有这些添加了一个解决方案，语法非常简单，让我向您展示:

```
 //swapping values
    let a = 1
    let b = 2
    [a, b] = [b, a]

    //multiple returned values
    function fn() {
      return [1,2,4]
    }
    [a,b,c] = fn()
    /*
    a = 1
    b = 2
    c = 4
    */ 
```

基本上，使用数组符号，我们可以在右边解包任何值，然后在左边赋值。更重要的是，如果您想从数组中获取前两个值，并将其余的值添加到另一个列表中，该怎么办呢？轻松点。

```
 let myList = [1,2,3,4,5,6,7]

    [first, second, ...tail] = myList
    /*
    first = 1
    second = 2
    tail = [3,4,5,6,7]
    */ 
```

正如你所看到的，执行多重赋值是非常简单的。这在处理多组正则表达式时尤其有用，例如:

```
 function parseURL(url) { 
      var parsedURL = /^(\w+)\:\/\/([^\/]+)\/(.*)$/.exec(url);
      if (!parsedURL) {
        return [];
      }
      [, ...parsedValues] =  parsedURL // we're ignoring the first element
        return parsedValues.map( v => v.length ? v : undefined) //We're making sure empty matches are set to undefined
    }

    [protocol, host, path] = parseURL("[https://www.fdoglio.com/blog](https://www.fdoglio.com/blog)")
    console.log(`The host is -${host}-, the protocol -${protocol}- and you're accessing the path -${path}-`); 
```

上面的例子在两个地方使用了析构:

1.  最初在函数内部，删除 matches 数组的第一个元素。这也可以用`parsedURL.shift()`来完成，但是同样，我们在这里使用声明性方法。

2.  将返回值赋给多个单独的变量，以便您可以随意处理它们。在我们的例子中，我们只是在模板字符串中单独使用它们。

如果右边是`undefined`，您甚至可以设置默认值。

```
 [protocol, host, path="none"] = parseURL("[https://www.fdoglio.com/](https://www.fdoglio.com/)");
    console.log(`The host is -${host}-, the protocol -${protocol}- and you're accessing the path -${path}-`);

    //The host is -[www.fdoglio.com-](http://www.fdoglio.com-), the protocol -https- and you're accessing the path -none- 
```

**注意**这是可行的，因为我们在解析函数中手动将空匹配改为 undefined，否则，默认值将被忽略。

按照同样的标准，我们可以将命名属性传递给函数，甚至在函数调用期间传递默认值，如下所示:

```
 let myObject = {
        name: "Fernando Doglio",
        country: "Uruguay",
        age: 35
    }

    //destructuring
    function wishHappyBirthday({name, age, numberOfKids=2}) {
        console.log(`Hello ${name} happy ${age}th birthday, have a great day with your wife and ${numberOfKids} kids`)
    }

    wishHappyBirthday(myObject) //expands my object into the actual function parameters 
```

在这个例子中，我们做了我们对数组所做的一切，但是对对象只包括提取我们想要的属性和设置默认值以防它们不存在。

确保在函数声明中使用正确的名称来匹配属性名称，因为赋值不是通过顺序匹配(像普通函数一样)来完成的，而是通过名称匹配来完成的。

您也可以执行上述操作，但是将一组特定的键放入单独的变量中，如下所示:

```
 const student = {
        firstname: 'Fernando',
        lastname: 'Doglio',
        country: 'Uruguay'
    };

    //pull properties by name
    let { firstname, lastname } = student
    console.log(`Nice to meet you ${firstname}  ${lastname}!`)

    //assign properties to particular variable names
    let { firstname: primerNombre, lastname: apellido} = student
    console.log(primerNombre, apellido); 
```

第一个例子非常简单，只需从对象中提取两个特定的属性，不考虑`country`。但是在第二个例子中，我还将向您展示如何将属性的内容重新分配到一个特定的新变量中(以防名称已经被占用或者您需要对其进行更多的控制)。

### 最后的想法

析构和扩展操作符成为语言的一部分已经有一段时间了，但是采用程度还没有那么高。我的看法是，对于开始并一直从事 JavaScript 的开发人员来说，这些都是陌生的概念，我希望您能够开始将您的编码风格转变为更具声明性的方法，并接受该语言为您提供的这些新工具。

如果你是从第一天起就一直在玩这些的疯狂的人之一，也许可以在下面留下评论，分享你对析构的最疯狂的使用或者你对 spread 操作符的想法！我很想知道你们对这两个人做了什么！

下一次再见！