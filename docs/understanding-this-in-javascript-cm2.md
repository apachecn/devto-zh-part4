# 理解 JavaScript 中的“this”

> 原文：<https://dev.to/naimlatifi5/understanding-this-in-javascript-cm2>

JavaScript 中的`this`关键字很多时候如果理解不正确就会混淆。在本文中，我将通过一些代码示例向您展示`this`是如何工作的，以及它的值是如何在 JavaScript 的不同上下文中赋值的。在不同的情况下，`this`可以改变它的值:

```
 - `this` in a global context, function invocation

      - `this` in an object constructor

      - `this` in an object method.

      - `this` with arrow function (=>) 
```

Enter fullscreen mode Exit fullscreen mode

但是在跳到我们刚刚提到的例子之前先等等**上下文**那么它在 JavaScript 中是什么呢？JavaScript 中的**上下文**总是在执行代码时确定的`this`的值。为了清楚地理解，现在让我们用代码示例来看看它在每个场景中是如何工作的。

```
console.log("What is 'this' value?", this) // Window
function simpleFunction () {
   console.log("What is the value of this inside function?", this) //  Window
  }
simpleFunction() 
```

Enter fullscreen mode Exit fullscreen mode

从代码示例 1 来看，`this`指的是从执行上下文中确定的全局对象，其中浏览器是全局对象(*窗口*)。类似地，当调用函数`this`时，该函数内部引用全局对象。但是`this`在构造函数里面指的是什么呢？我们在下面的代码示例 2 中看到了这一点。

```
function Person(name, lastName) {
    this.name = name;
    this.lastName = lastName;
    this.displayName = function() {
       console.log(`Your name ${this.name} and lastName ${this.lastName}`)
  }
}
  let person = new Person('George', 'Superman')
      person.displayName() // George Superman 
```

Enter fullscreen mode Exit fullscreen mode

从上面的代码中我们可以看到，我们创建了一个构造函数 *Person* ，然后我们调用了一个新的实例对象 *george* 。`this`值，在这种情况下，指的是新创建的对象。

```
let greetings = {
     word: 'Hello',
     printGreeting: function(){
       console.log("What 'this' refers to inside object method? ", 
   this.word) // "this" in object refers to the object itself- greetings
     }
  }

  greetings.printGreeting() 
```

Enter fullscreen mode Exit fullscreen mode

根据上面的代码示例 3，我们创建了一个具有属性和方法 *printGreeting* 的对象。我们用`this`调用方法中的属性名，它的值指向对象本身。

我们从上面的代码示例中看到，函数中的`this`值引用全局对象，对象方法中的值引用对象本身，但是如果我们想调用另一个函数中的对象方法并引用`this`呢？在这种情况下，`this`的价值是什么？让我们看看代码示例 4，我们将在下面的
中用**严格模式**运行它

```
"use strict"
let person = {
    firstName: 'George',
 }

 function printName(greeting) {
  console.log(greeting + this.firstName)
 }

 printName('hello') // Uncaught TypeError: Cannot read property 'firstName' of undefined 
```

Enter fullscreen mode Exit fullscreen mode

运行代码后，我们看到出现了一个错误，因为 JavaScript 解释器不理解对象属性 *this.firstName* ，因为函数中的`this`值是在执行上下文中设置的。但是，如何克服这个问题呢？JavaScript 函数是一种特殊类型的对象，它包含方法 *call()* 、 *apply()* 和 *bind()* 。

#### 调用()，应用()和绑定()

这些方法由所有函数继承，以便在任何期望的上下文中获得`this`的值。这些函数方法之间的区别在于， *call()* 方法要求参数以逗号分隔，apply()方法以数组列表的形式，并且可以立即执行，而 bind()方法可以稍后在任何所需的上下文中执行。下面的代码示例 5 展示了我们如何在任何期望的上下文中使用这些方法和设置`this`。

```
 let person = {
    firstName: 'George',
  }

  function printName(greeting) {
    console.log(greeting + this.firstName);
  }

  printName.call(person, 'Hello') // Hello George
  printName.apply(person, ['Hello there']) // Hello there George
  let executeLater = printName.bind(person)
  executeLater() // George 
```

Enter fullscreen mode Exit fullscreen mode

#### `this`带箭头功能= >

在 ECMAScript 2015 (ES6)中，箭头函数作为 JavaScript 世界中的一项新功能被引入。使用箭头函数有很多好处，它可以缩短函数语法，不需要绑定`this`。通过使用 arrow 函数,`this`在封闭上下文中是词汇有界的。让我们通过代码示例来了解这一点。

```
let greetings = {
  word: 'Hello',
  printGreeting(){
      console.log(this.word)  // Hello
    })
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果我们回到前面的代码示例 2，我们看到`this` inside object 指的是对象本身，但是如果我们被要求在 1 秒钟后显示我们的问候呢？然后，为了得到所需的结果，我们需要修改如下代码

```
let greetings = {
  word: 'Hello',
  printGreeting(){
    window.setTimeout(function () {
      console.log(this.word) 

    }, 1000) // we can set bind(this)
  }
}

greetings.printGreeting() // undefined 
```

Enter fullscreen mode Exit fullscreen mode

我们看到，在执行方法 *printGreeting()* 之后，我们得到了未定义，因为 *setTimout()* 函数调用将`this`上下文设置为全局对象。然而，为了达到这个结果，我们可以使用前面提到的 *bind()* 方法，或者我们可以在 *setTimeout()* 函数之前用 *self=this* 创建一个闭包，但是我们不需要这样做，因为我们可以使用 arrow 函数
来达到预期的结果

```
 let greetings = {
  word: 'Hello',
  printGreeting(){
    window.setTimeout(() => {
      console.log(this.word)  
    }, 1000)
  }
}

greetings.printGreeting() // Hello 
```

Enter fullscreen mode Exit fullscreen mode

现在，作为控制台上的结果，我们将得到问候 **hello** ，因为使用箭头函数“*这个*被绑定到封闭上下文，因此我们的“*问候*对象。

就是这样！:)

希望你对这篇文章感兴趣，并帮助你理解 JavaScript 中的`this`及其在不同环境中的价值。