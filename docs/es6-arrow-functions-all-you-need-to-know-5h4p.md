# ES6 箭头功能:您需要知道的一切

> 原文：<https://dev.to/godcrampy/es6-arrow-functions-all-you-need-to-know-5h4p>

ES6 引入了箭头函数，它提供了一种编写更短更简洁代码的方法。

```
// Old way
function squareOld(number) {
    return number ** 2;
}

// ES6 way
const squareNew = number => number ** 2;

// Callbacks become cleaner
let array = [1, 2, 3, 4, 5, 6];

// Old
array.filter(function(value) {
    return value % 2 === 0;
});
// [2, 4, 6]

// ES6
array.filter(value => value % 2 === 0);
// [2, 4, 6] 
```

Enter fullscreen mode Exit fullscreen mode

请注意以下事项:

1.  箭头函数语法返回我们需要存储在变量中的函数
2.  我们不写`return`关键字。当函数定义只有一行时，返回该行的输出
3.  参数`number`没有括号。(当有多个参数时，情况就不同了)

### 箭头语法

```
// No arguments
const sayHi = () => console.log("Hi");

// One argument, One line
const addFive = number => number + 5;

// Multiple arguments, One line
const addNumbers = (a, b) => a + b;

// One argument, Multiple lines
const doStuffAndAddFive = number => {
    let flavour = "vanilla";
    makeIceCream(flavour);
    eatIceCream();
    return number; /* Note that here we have to use the return keyword */
};

// Multiple arguments, Multiple lines
const doStuffAndAddNumbers = (a, b) => {
    let flavour = "vanilla";
    makeIceCream(flavour);
    eatIceCream();
    return a + b;
}; 
```

Enter fullscreen mode Exit fullscreen mode

### 绑定在普通函数中

绑定是箭头函数与普通函数的区别。每个函数都可以访问`this`变量。

考虑以下情况:

```
function getThis() {
    return this;
}
getThis();
// returns the global object 
```

Enter fullscreen mode Exit fullscreen mode

上述函数在被调用时返回全局对象。

现在考虑这个:

```
let obj = {
    fruit: "Apple",
    calories: 100,
    getThis: function() {
        return this;
    }
};

obj.getThis();
// returns the obj object 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看一个 ES6 *类*的例子:

```
class Fruit {
    constructor(name, calories) {
        this.name = name;
        this.calories = calories;
    }
    getThis() {
        return this;
    }
}

let apple = new Fruit("Apple", 97);
apple.getThis();
// returns apple 
```

Enter fullscreen mode Exit fullscreen mode

一个奇特的案例:

```
let person = {
    name: "Sally",
    getName: function() {
        return this.name;
    }
};

let name = "Sergie";

person.getName();
// "Sally"

let foo = person.getName;

foo();
// "Sergie" 
```

Enter fullscreen mode Exit fullscreen mode

当我们称之为`person.getName()`，`this`指的是人对象。然后，当我们用与`person.getName`相同的定义来初始化`foo`时，`this`指的是全局对象。

如何搞清楚`this`关键词指向哪里？

一个在大多数情况下都有效的好方法是检查函数调用之前是否有点操作符。如果是，那么函数定义中的`this`将引用点运算符之前的对象。在上面的例子`person.getName()`中，导致`this`被引用到`person`。如果没有点符，`this`通常会指代全局对象。

注意，这只是一个技巧，而不是规则。

### 绑定箭头功能

与普通函数不同，`this`的绑定根据代码定义的位置*而不是定义本身而变化，arrow 函数中的绑定在任何地方都保持不变。*

我们来考虑一下前面带箭头函数的代码:

```
let person = {
  name: "Sally",
  getName: () => this.name;
}

let name = "Sergie";

person.getName();
// "Sergie"

let foo = person.getName;

foo();
// "Sergie" 
```

Enter fullscreen mode Exit fullscreen mode

因此，在这两种情况下，arrow 函数引用带有`this`的全局对象。在箭头功能的情况下，`this`绑定不变。另一个检查箭头函数的`this`对象指向哪里的方法是在声明箭头函数之前观察`this`的值。

```
let object = {
    whatsThis: this,
    getThisNew: () => this,
    getThisOld: function() {
        return this;
    }
};

object.whatsThis();
// global

object.getThisNew();
// global

object.getThisOld();
// object 
```

Enter fullscreen mode Exit fullscreen mode

让我们在 ES6 类的情况下测试一下我们目前所知道的。注意，与对象不同的是，`this`引用的不是对象本身，在类中它引用的是类的实例。

```
class Fruit {
    constructor(name) {
        this.name = name;
    }
    getNameOld() {
        return this.name;
    }
    getNameNew = () => this.name;
}

// global variable
let name = "Sally";

let apple = new Fruit("Apple");

apple.getNameNew();
// "Apple"

apple.getNameOld();
// "Apple"

// Now let's make two new functions:
let foo = apple.getNameOld;
let bar = apple.getNameNew;

foo();
// "Sally"

bar();
// "Apple" 
```

Enter fullscreen mode Exit fullscreen mode

注意`foo`是如何返回`"Sally"`的，因为*绑定到全局对象*，而`bar`绑定到`apple`对象并返回`"Apple"`。

因此，总的来说，普通函数遵循*点*操作符“hack ”,而箭头函数保持*绑定*到函数定义前的值`this`。即使函数被重新声明，这种绑定仍然存在，不像香草味。

> 这就是你需要知道的关于 ES6 箭头函数的全部内容。如果你发现了错误或者有疑问，请在评论中告诉我。感谢您的阅读，祝您有美好的一天😄