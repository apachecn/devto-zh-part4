# JavaScript 中 For 循环的 3 种风格以及何时使用它们

> 原文：<https://dev.to/deleteman123/3-flavors-of-the-for-loop-in-javascript-and-when-to-use-them-2jbl>

每个人都至少知道循环的**的一个版本，这是一个经典，可能几乎每种语言都有它的一个版本。然而，JavaScript 有三个(或者四个，如果你真的很挑剔的话)，并且**它们不完全相同**，我当然指的是:**

*   经典的 For 循环

*   成对的 For…和 For…的

*   还有花哨的功能版:。为每一个

所有版本之间都有差异，所以在本文中，我想涵盖所有这三个版本，以及如何或何时使用它们来获得最佳结果。让我们开始吧。

## 经典 For 循环

我们都很清楚，这是典型的 For 循环，在这里定义内部计数器，设置中断条件和步长变化(通常递增或递减计数器)。

语法是:

```
 for([counter definition];[breaking condition definition];[step definition]){
       //... your repeating code goes here

    } 
```

Enter fullscreen mode Exit fullscreen mode

现在，我确信你以前写过这样的代码，最常见的形式是:

```
 for(let counter = 0; counter < 10; counter++) {
      console.log(counter)
    } 
```

Enter fullscreen mode Exit fullscreen mode

虽然这段代码运行得非常好，但是 For 循环的各个部分比它更灵活。事实上，你应该把它们想象成

```
 for(
    [EXPRESSION EXECUTED ONLY ONCE AT THE START OF THE LOOP];
    [BOOLEAN CONDITION CHECKED ON EVERY STEP];
    [EXPRESSION EXECUTED ON EVERY STEP OF THE LOOP]
    ) 
```

Enter fullscreen mode Exit fullscreen mode

也就是说，你可以用不止一个计数器来执行 For 循环，或者让代码在不一定影响你的计数器的每一步上执行。仅举几个例子。

例如，这是一个完全有效的循环:

```
for(let a = 0, b = 0; a < 10 && b < 100; a++, b+=10) {
   console.log(a, b)
}
/*
0 0
1 10
2 20
3 30
4 40
5 50
6 60
7 70
8 80
9 90
*/ 
```

Enter fullscreen mode Exit fullscreen mode

你甚至可以把它推得更远，从上面移出正常的用例:

```
for(let a = 0, b = 0; a < 10 && b < 100; console.log("Your counters are at:", ++a, b+=2)){}
/*
Your counters are at: 1 2
Your counters are at: 2 4
Your counters are at: 3 6
Your counters are at: 4 8
Your counters are at: 5 10
Your counters are at: 6 12
Your counters are at: 7 14
Your counters are at: 8 16
Your counters are at: 9 18
Your counters are at: 10 20
*/ 
```

Enter fullscreen mode Exit fullscreen mode

您甚至可以用中间表达式替换函数调用，只要您记住函数的返回值将被转换为布尔值。

```
 function isItDone(a) {
 console.log("fn called!")
 return a < 10
}

for(let a = 0; isItDone(a); a++) {
 console.log(a)
}
/*
fn called!
0
fn called!
1
fn called!
2
fn called!
3
fn called!
4
fn called!
5
fn called!
6
fn called!
7
fn called!
8
fn called!
9
fn called!
*/ 
```

Enter fullscreen mode Exit fullscreen mode

如何处理经典* * For * *循环中的**异步代码**？感谢我们的新朋友 async/await，这很容易做到:

```
const fs = require("fs")

async function read(fname) {
    return new Promise( (resolve, reject) => {
        fs.readFile(fname, (err, content) => {
            if(err) return reject(err)
            resolve(content.toString())
        })
    })
}

(async () => {
    let files = ['file1.json', 'file2.json']

    for(let i = 0; i < files.length; i++) {
        let fcontent = await read(files[i])
        console.log(fcontent)
        console.log("-------")
    }
})() 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我们可以简单地使用循环，就像没有异步机制在后面工作一样。这都是 async/await，但是多亏了它，我们又回到了依赖一个基本的构造，比如循环的**来迭代一组异步指令。**

在过去，如果你想通过回叫或承诺来达到同样的目的，逻辑会复杂得多。这就是为什么像 [async.js 这样的库诞生了。](https://caolan.github.io/async/v3/)

顺便提一下，一个小注意:我的例子中的 for 循环在一个[life](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)中，因为你可能已经知道，await 指令需要在一个 *async* 函数中，否则 Node 不允许它。

## 此为...一对中的……

是的，它们与之前的版本非常相似，但同时，它们是不同的循环。

让我快速定义一下:

的* *为..在* *循环中，处理来自对象的非符号、[可枚举属性](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Enumerability_and_ownership_of_properties)(关键字是“object ”,因为 JavaScript 中的几乎所有内容都是对象)。这对于将自定义对象用作哈希映射或字典的情况非常有用(这是一种非常常见的做法)。

** *注意，* *然而，迭代是按任意顺序进行的，所以不要依赖循环来选择你需要的正确顺序，并确保你控制了有意义的部分。*

```
let myMap {
  uno: 1,
  dos: 2,
  tres: 3
}
for(let key in myMap) {
  console.log(key, "=", myMap[key]);
}
/*
uno = 1
dos = 2
tres = 3
*/ 
```

Enter fullscreen mode Exit fullscreen mode

很简单，不是吗？但是要注意，因为就像我说的，JavaScript 中的几乎所有东西都是对象，所以当你实际上想要为的……做一个**时，你可能会在**中为……做一个**。例如，如果你想迭代一个字符串(一个对象)中的每个字符，如果你在** :
中使用**来表示…会发生什么**

```
for(let k in "Hello World!") {
   console.log(k)
}
/*
0
1
2
3
4
5
6
7
8
9
10
11
*/ 
```

Enter fullscreen mode Exit fullscreen mode

我们没有遍历字符串的每个字母，而是遍历了每个属性，正如你所看到的，我们实际上是在处理一个非常类似于数组的结构(对于字符串类型)。这毕竟是有意义的，因为做了“你好，世界！”[1]不仅起作用，而且还返回该位置的实际字符(即字母“e”)。

相反，如果你想迭代每个字符，你需要使用另一个变量: **For…of**

```
for(let char of "Hello World!") {
  console.log(char)
}
/*
H
e
l
l
o

W
o
r
l
d
!
*/ 
```

Enter fullscreen mode Exit fullscreen mode

现在，这更有意义了，不是吗？相同的用例，但是使用它，您可以访问可迭代的值(字符串是可迭代的，数组、映射、集合和类似数组的结构，如参数或节点列表也是可迭代的)。当然，还有你自己的对象，如果你把它们定义为可迭代的。

按照上面的例子，没有直接的方法来获得循环的当前索引，当然，除非你在循环之外定义它并在每一步更新它，或者你可以同时获得索引和值，如果你对数组使用 entries 方法，就像这样:

```
let myArr = ["hello", "world"]
for([idx, value] of myArr.entries()) {
    console.log(idx, '=', value)
}
/*
0 '=' 'hello'
1 '=' 'world'
*/ 
```

Enter fullscreen mode Exit fullscreen mode

最后，为了便于比较，异步代码怎么样？一模一样！

```
 const fs = require("fs")

async function read(fname) {
    return new Promise( (resolve, reject) => {
        fs.readFile(fname, (err, content) => {
            if(err) return reject(err)
            resolve(content.toString())
        })
    })
}

(async () => {
    let files = ['file2.json', 'file2.json']

    for(fname of files) {
        let fcontent = await read(fname)
        console.log(fcontent)
        console.log("-------")
    }

    for(idx in files) {
        let fcontent = await read(files[idx])
        console.log(fcontent)
        console.log("-------")
    }
})() 
```

Enter fullscreen mode Exit fullscreen mode

这两个循环对 await 构造的反应完全相同，这允许您编写更简单、更整洁的代码。

## 花哨而实用。forEach 循环

这可能是我最喜欢的一个，这仅仅是因为我非常喜欢声明式语法或者用声明式方法编写命令式代码。虽然上述版本的循环工作得很好，并有自己非常好的用例，但它们也是非常必要的，因为我们需要用我们的数据编写*需要发生什么* *，而不是简单地*编写我们希望发生什么。**

不管怎样，撇开哲学辩论不谈，**。forEach * *方法是 For 循环的另一个版本，但是它是 Array 对象的一部分，用于接收一个函数和一个额外的可选参数，以便在执行该函数时重新定义它的上下文。

对于数组中的每个元素，我们的函数将被执行，它将接收**三个参数**(是的，你没看错**三个**，而不是你习惯使用的一个)。它们是:

1.  正在处理的当前元素。

2.  元素的索引，这已经简化了我们试图用循环的**For…实现的任务**

3.  正在处理的实际数组。以防你需要用它做些什么。

为了继续这个例子，我们来看一个简单的例子:

```
a = ["hello", "world"]

a.forEach ( (elem, idx, arr) => {
   console.log(elem, "at: ", idx, "inside: ", arr)
})
/*
hello at:  0 inside:  [ 'hello', 'world' ]
world at:  1 inside:  [ 'hello', 'world' ]
*/ 
```

Enter fullscreen mode Exit fullscreen mode

快速而简单，但是你可以看到我们如何在函数中轻松地使用所有属性。这里有一个例子，当你想在`forEach`方法中使用第二个可选参数:

```
class Person {
    constructor(name)  {
        this.name = name
    }
}

function greet(person) {
    console.log(this.greeting.replace("$", person.name))
}

let english = {
    greeting: "Hello there, $"
}
let spanish = {
    greeting: "Hola $, ¿cómo estás?"
}

let people = [new Person("Fernando"), new Person("Federico"), new Person("Felipe")]

people.forEach( greet, english)
people.forEach( greet, spanish) 
```

Enter fullscreen mode Exit fullscreen mode

通过覆盖我们调用的函数 greet 的上下文，我能够在不影响代码的情况下改变它的行为。

并最终确定，表明这个方法也可以用于异步代码，下面是例子:

```
const fs = require("fs")

async function read(fname) {
    return new Promise( (resolve, reject) => {
        fs.readFile(fname, (err, content) => {
            if(err) return reject(err)
            resolve(content.toString())
        })
    })
}

let files = ['file1.json', 'file2.json']

files.forEach( async fname => {
    let fcontent = await read(fname)
    console.log(fcontent)
    console.log("-------")
}) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，我不再需要生命，因为我将回调声明为 *async。*

## 结论

这就是我想分享的关于 JavaScript 中 For 循环的全部内容，我希望现在你对它们有了更清晰的理解，并可以根据这些知识和我们当前的编码需求来选择你更喜欢的循环。

我错过了他们有趣的事情吗？在下面留下评论，分享给大家！

否则，我们下一次再见！