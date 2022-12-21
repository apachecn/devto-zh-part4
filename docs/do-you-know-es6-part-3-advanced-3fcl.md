# 你知道 ES6 -第三部分-高级吗

> 原文：<https://dev.to/this_mkhy/do-you-know-es6-part-3-advanced-3fcl>

## 在开始阅读本高级部分之前，我们已经讨论了 ES6 的一些特性:

[你知道 ES6-Part 1](https://dev.to/mkhy19/do-you-know-es6---part-1-387m)
T3】你知道 ES6 - Part 2 吗

## 目录

*   [人生](#IIFE-1)
*   [关闭](#Closures-2)
*   [同步与异步](#Synchronous%20vs%20Asynchronous-3)
*   [承诺](#Promises-4)
*   [异步 vs 等待](#Async%20vs%20Await-5)

* * *

# 人生

[![image](img/c5f54f786919eefdf03bc7af0b61b37c.png)](https://i.giphy.com/media/3o7WTxQ7RFErPLfOqk/giphy.gif)

IIFE 是指立即调用的函数表达式。IIFE 是一个 JavaScript 函数，它一被定义就开始运行。 [**MDN 网络文档**](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)

生活不同于传统的功能，我们可以不止一次地调用它，但生活不是。生命只有一次。所以我们不会再用了。这意味着函数中的变量不能被访问，所以它们是不可变的。

IIFE 的一个好处是创建局部作用域，如果我有许多可能有相同变量名的 js 文件，这是非常重要的。所以生命避免覆盖并保护它的变量的范围。

有两种方法可以调用这个函数:

*   首先，我们定义和调用函数的传统方式

```
function printName(){
  let myName = "Mohamed"
  console.log(myName)
}

//Invoke
printName() 
```

Enter fullscreen mode Exit fullscreen mode

*   第二，利用生命。我们把函数放在括号里，然后在函数的末尾加上一对括号

(我们的函数)(Invoke)
(我们的函数)()

```
(function printName(){
  let myName = "Mohamed"
  console.log(myName)
})() 
```

Enter fullscreen mode Exit fullscreen mode

事实上，我们不需要给函数一个名字，因为它只被调用一次。所以生命通常是匿名函数

```
(function(){
  let myName = "Mohamed"
  console.log(myName)
})() 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 关闭

[![image](img/d43445cb60cc78e11abcc40ebb564127.png)](https://i.giphy.com/media/3o6Mb7Vqfuk0n2p8Xu/giphy.gif)

闭包是指一个函数记住了它的词法范围，即使它是在词法范围之外执行的。所以闭包就是当一个函数使用另一个函数或另一个作用域中定义变量时。所以它链接到这个变量来更新它值。

[![image](img/17a3f60dae49a01278bcc1d505750dbe.png)](https://i.giphy.com/media/Y8tdZ0MiJsuIM/giphy.gif)

在这个例子中，我们有一个包含变量的 printName 函数。然后我们有嵌套的打印函数，在这个作用域中使用这个变量。然后我们有闭包函数调用打印函数。最后，我们可以在另一个作用域中调用这个函数。换句话说，我们可以执行使用名称变量的打印功能。这个变量没有在闭包函数作用域中声明。但是这个变量在 printName 函数范围内。
默认逻辑是错误的。但事实上，这就是闭包及其工作方式。因此，如果我们改变或更新变量名的值，闭包会更新它。

```
function printName(){
   var name="Mohamed";
   //name="Mohamed Khaled";
   function print() {
      console.log(name);
   }

   closure(print);
}

function closure(func)
{    
    func();
}

printName(); 
```

Enter fullscreen mode Exit fullscreen mode

再比如，我们可以在内部函数
中获取并更新变量 x

```
function outer(){
  let x = 4
  function inner (){
    let y = x
    y = 16
    console.log(x)
    console.log(x*2)
    console.log(y)
  }

  closure(inner);  
}

function closure(inn)
{    
    inn();
}

outer() 
```

Enter fullscreen mode Exit fullscreen mode

这是创建前面的闭包函数的另一种方法。这里我们已经用匿名函数替换了内部函数，匿名函数在一个数组中返回多个值。然后我们执行外部函数。

```
function outer(){
  let x = 4
  return function (){
    let y = x
    y = 16
    return [x,x*2,y]
  }
}

//IIFE
console.log(outer()());

//let res = outer()
//console.log(res()); 
```

Enter fullscreen mode Exit fullscreen mode

让我们看另一个例子，它是一个使用闭包的简单计数器。反正我推荐你用++n，看看区别。

```
function counter(n){
  return function (){
    return n++
  }
}

let res = counter(1)
console.log(res());
console.log(res());
console.log(res());
console.log(res());
console.log(res()); 
```

Enter fullscreen mode Exit fullscreen mode

让我们深入更复杂的问题。您期望这段代码的输出是什么？想想吧！！

```
for(var i=0;i<10;i++){
    setTimeout(function(){
        console.log(i);
    },100);
} 
```

Enter fullscreen mode Exit fullscreen mode

经过思考。输出是计数器 I 的最后一个值，即 10。
因为 I 是定义在全局范围内的变量。所以这是因为关闭而发生的。同样，Clousure 使用在另一个作用域中定义的变量的最后一个值。

我想你想知道如何解决这个问题？好吧，不止一个解决方案。其中之一是使用 let 来创建计数器 I，因为 let 是局部作用域，而不是全局作用域。

```
for(let i=0;i<10;i++){
    setTimeout(function(){
        console.log(i);
    },100);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用立即执行的 IIFE 函数来解决它。所以关闭修复超时。

```
function closure (index){
    setTimeout(function(){
        console.log(index)
    },100)
}

for(var i=0;i<10;i++){
    (closure)(i)
} 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 同步 vs 异步

## 同步编程

[![image](img/919e365f103d611cc3fa26f9ac11704a.png)](https://i.giphy.com/media/pDWtwK7D2IlFu/giphy.gif) 
同步编程意味着你的代码一行一行地运行，一个函数一个函数地运行。所以不能同时运行两个功能。

## 异步编程

[![image](img/3e8449cf8ec15cb6cc5401fb4463a6a3.png)](https://i.giphy.com/media/l46Cv8ujmQBUv7Kq4/giphy.gif) 
异步功能用三个简单的词表示“它可以等”。换句话说，这意味着你的功能可以在另一个功能运行时运行。所以你可以在不冻结程序的情况下同时运行两个功能。

异步函数来自于[**Web API**](https://developer.mozilla.org/en-US/docs/Web/API)，其中有很多异步函数。JS 有许多内置的异步函数，比如 setTimeOut、setInterval、Promises、事件处理程序等等。

还有另一种类型的函数叫做**回调函数**，它在异步函数结束后执行

在下一个例子中，我们将定义一个函数 getStudent，它将一个回调函数作为参数。然后我们调用回调函数，返回学生的姓名和年龄，延迟 2 秒钟响应。

最后，我们调用 getStudent，并将回调函数作为参数传递，当经过 2 秒钟的延迟时，将调用该函数。

在输出中，首先执行最后一条 console.log 语句，因为回调函数的执行仍然延迟了 2 秒，所以输出也延迟了。

```
const getStudent = callback => {
    setTimeout(() => {
       callback ({ name: 'Mohamed', age: 23 })
    }, 2000)
}

getStudent(student => {
    console.log("This is executed second")
    console.log(student.name, student.age)
})

console.log("This is executed first") 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 承诺

[![image](img/4d387dc70b388f364bf9b612ab7f08f1.png)](https://i.giphy.com/media/MaP7o24dFPqvVAXFMy/giphy.gif)

## 什么是承诺？

Promise 是 JS 中的一个内置异步函数，可以更容易地处理异步代码。承诺是一个异步动作，可能在某个时间点完成并产生一个价值。所以有了承诺，我们试着运行一些操作。如果操作成功运行，我们为承诺做一个叫做 resolve 的东西。如果失败了，我们就拒绝。所以 promise 处理异步操作。

## 如何创造承诺？

我们使用一个名为 Promise 的构造函数，它接受一个 executor 函数。此函数尝试运行操作，并对承诺进行解决或拒绝。

这是第一个承诺

```
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('promise done')
    resolve('done')
  }, 2000)
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 如何知道承诺是否有效？

知道承诺的结果是决定还是拒绝。我们使用 then 和 catch 来获得结果。

*   然后，当出现 resolve for promise 时，函数成功运行。这意味着操作成功完成。也便回报另一个承诺。
*   当出现拒绝承诺或失败时，Catch takes 函数成功运行。

```
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('promise done')
    resolve('done')
    //reject('Is not done. Error')
  }, 2000)
})

p.then(() => console.log('promise resolved'))
 .catch(() => console.log('promise rejected')) 
```

Enter fullscreen mode Exit fullscreen mode

## 解析或拒绝的值

现在，无论结果是解决还是拒绝。如果我们需要这个决定或拒绝的值。这是我们的解决值“完成”,拒绝值“未完成”。错误。所以为了得到它，我们的 then 或 catch 函数需要一个参数。

```
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('promise done')
    resolve('Done')
    reject('Is not done. Error')
  }, 2000)
})

p.then((res) => console.log('promise resolved', res))
 .catch((err) => console.log('promise rejected', err)) 
```

Enter fullscreen mode Exit fullscreen mode

## 嵌套承诺

如果我们的承诺结束了，我们想运行另一个承诺。这就是所谓的嵌套承诺。

```
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('promise done')
    resolve('Done')
  }, 2000)
})

//Nested promise
p.then((res) => {
  p.then(res2 => console.log(res2))
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 连锁承诺

我想告诉你嵌套承诺不是好的实践。所以有连锁承诺。这是我们的函数，返回我们的承诺 p，然后函数的结果就是我们的承诺 p。最后，我们可以使用 then 来进行链接承诺。

```
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('promise done')
    resolve('Done')
  }, 2000)
})

//Chaining promise 
p.then((res) => {
  return p
}).then(res2 => console.log(res2))
//p.then((res) => p).then(res2 => console.log(res2)) 
```

Enter fullscreen mode Exit fullscreen mode

下面是最终代码

```
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('promise done')
    resolve('Done')
  }, 2000)
})

//Nested promise
p.then((res) => {
  p.then(res2 => console.log(res2))
})

//Chaining promise 
p.then((res) => {
  return p
}).then(res2 => console.log(res2))

//Chaining promise 
p.then((res) => p).then(res2 => console.log(res2))

//Chaining promise .. Best practice and more readable
p
  .then((res) => p)
  .then(res2 => console.log(res2)) 
```

Enter fullscreen mode Exit fullscreen mode

当我有连锁承诺。如果任何承诺被拒绝，它将运行第一个 catch 并忽略其余的。

```
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('promise done')
    reject('Is not done. Error')
  }, 2000)
})

//Chaining promise 
p
  .then((res) => p)
  .then(res2 => console.log(res2))
  .catch((err1) => console.log('promise rejected 1', err1))
  .catch((err2) => console.log('promise rejected 2', err2)) 
```

Enter fullscreen mode Exit fullscreen mode

最后，你还记得我们的回调例子吗？我要做出承诺，同样的产量。试着理解一下 LOL:)

`let p = new Promise((resolve, reject) => { setTimeout(() => { let error = false; if(error) { console.log("This is executed second, Done") resolve({ name: 'Mohamed', age: 23 }) } else { console.log("This is executed second, Error") reject("Error404") } }, 2000) }) const getStudent = () => { return p } getStudent() .then(student => { console.log(student.name, student.age)}) .catch(err => console.log('promise rejected', err)) console.log("This is executed first")`

* * *

# 异步 vs 等待

## 异步

Async 是关键字，Await 是运算符。它们已被添加到 ES8 中。异步 vs 等待让我们用比使用承诺链更好的方式处理承诺，所以我们的承诺变得更容易。

Async 与一个函数一起使用，这意味着这是一个异步函数，但它返回一个承诺。

*   Return = = Resolve
*   投掷==拒绝

在这个例子中，我们的承诺解决或返回我的名字'我是穆罕默德'

```
async function myName(){
   return 'I am Mohamed'
}

myName().then( msg => console.log(msg)) 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们的 promise reject 或 throw isName 'Is not Mohamed'

```
async function isName(){
   throw 'Is not Mohamed'
}
isName().catch( msg => console.log(msg)) 
```

Enter fullscreen mode Exit fullscreen mode

## 等待

await 意味着您必须等待，直到您执行了这一行。await 仅在异步函数中有效。

在下一个例子中，我们有 promise p 和名为 myName 的异步函数。我们会注意到“这是首先执行的”是第一行，但是我们必须等到我们的承诺 p 结束。最后，在承诺完成后，剩下的被执行，所以最后一行是'我是穆罕默德'。

```
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('promise done')
    resolve('Done')
  }, 2000)
})

async function myName(){
   console.log('This is executed first')
   await p
   //p
   console.log('I am Mohamed')
}

myName() 
```

Enter fullscreen mode Exit fullscreen mode

另一个例子

```
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('promise done')
    resolve('Done')
  }, 2000)
})

async function myName(){
   console.log('This is executed first')
   await p

   console.log('I am Mohamed')

   setTimeout(() => {
    console.log('Last line')
  }, 5000)

   console.log('I am Egyptian')
}

myName() 
```

Enter fullscreen mode Exit fullscreen mode

你知道承诺，决定或拒绝。现在，等待的结果是解决或拒绝的结果。

如果许下决心

```
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('promise done')
    resolve('Done')
  }, 2000)
})

async function myName(){
   let result = await p
   console.log('The result of await is : ' + result)
}
myName() 
```

Enter fullscreen mode Exit fullscreen mode

如果承诺被拒绝，它会自动抛出错误。所以我们要避开承诺链，用这种方式。

```
let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    console.log('promise done')
    //resolve('Done')
    reject('error 404')
  }, 2000)
})

async function myName(){
   let result = await p
   return result
}

myName()
  .then( res => console.log('The result of await is : ' + res))
  .catch( err => console.log('Error: ' + err)) 
```

Enter fullscreen mode Exit fullscreen mode

最后，你还记得我们的回调例子吗？我们用回调和承诺两种方式来实现。现在，我将使用 async 创建它，并等待相同的输出。自己试着理解一遍:)LOL :(

`let p = new Promise((resolve, reject) => { setTimeout(() => { resolve({ name: 'Mohamed', age: 23 }) //reject('error 404') }, 2000) }) const getStudent = () => { return p } async function fetchStudent () { let student = await getStudent() return student } fetchStudent() .then(student => console.log(student.name + " " + student.age)) .catch((err) => console.log("Error: " + err)) console.log("This is executed first")`

*   我们也可以使用 try 和 catch 来处理错误

```
let p = new Promise((resolve, reject) => {
    setTimeout(() => {
        let error = false;
        if(error)
        {
            console.log("This is executed second, Done")
            resolve({ name: 'Mohamed', age: 23 })
        }
        else
        {
            console.log("This is executed second, Error")
            reject()
        }
    }, 2000)
})

const getStudent = () => {
    return p
}

async function fetchStudent () {
    try { 
        const student = await getStudent()
        return student
    } catch (error) {
        console.log("Error")
    }
}

fetchStudent()
  .then(student => console.log(student.name + "  " + student.age))
  .catch(() => console.log("error 404"))

console.log("This is executed first") 
```

Enter fullscreen mode Exit fullscreen mode

* * *

## 结论

感谢您的阅读，希望您在这里找到了有价值的信息。
[这里的](https://github.com/mkhy19/Crash-courses/tree/master/JS/ES6)是回购，你可以找到源代码，随意叉一下。