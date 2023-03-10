# 4 JavaScript 中的“this”规则

> 原文：<https://dev.to/_blank/4-rule-of-this-in-javascript-47ak>

一篇文章似乎太短，无法涵盖`this`的精彩，所以我们将分两部分讨论`this`。

尽管 JavaScript 中的`this`可能令人困惑，但它是 JavaScript 中的一个重要概念，每个 JS 开发人员都应该知道。
`this`为 JavaScript 中的面向对象提供了基础。
我不是专业人士，但我会尽力解释我对`this`的理解。

## 目标

*   定义 JavaScript 中的关键字`this`是什么
*   理解四个规则，算出`this`的值

## 那么什么是`this`？

`this`是 JavaScript 中的保留关键字，仅指它当前所属的对象。`this`的值是在执行上下文中设置的，通常由函数的调用方式决定，因此`this`的值会在执行过程中改变。

## 四大法则`this`

可以使用以下规则之一来确定`this`的值。
(这些不是官方规则，只是我从我们那里的优秀开发者那里学到的一些东西)

1.  全局上下文规则
2.  对象或隐含规则
3.  显式规则(调用、绑定和应用)
4.  `new`规则(JavaScript 中的`new`关键字)

### 1。全局上下文规则

*提醒* : `this`指向其当前所属的对象。

当`this`不在任何声明的对象中时，那么`this`指向`window`对象。
在全局范围内声明的每个变量都被附加到`window`对象上。

基本上，在浏览器中，

```
let name = 'blank'; 
```

与
相同

```
window.name = 'blank'; 
```

让我们看看浏览器中`this`的值。

```
console.log(this); // Window{...}, window object 
```

这里`this`指向`window`对象，因为`this`在`window`对象内部。

我们可以验证`this`是否真的指向了`window`对象。

```
this === window; // true 
```

我们可以看到，`this`等于`window`对象。

现在我们来看看函数内部`this`的值。

```
function whatIsThis() {
    return this;
}

whatIsThis(); // Window { ... }, Window Object 
```

即使我们在函数内部调用了`this`，但是`this`仍然在`window`对象内部，所以它仍然指向`window`对象。

让我们看另一个例子。

```
function variableInThis() {
   this.person = 'blank';
}

// calling our function

variableInThis();

// let's check the value of `person` variable
console.log(person); // 'blank'
console.log(Window.person); // 'blank' 
```

总而言之，`this`指向了`window`对象，因为`this`属于`window`对象，我们全局创建的任何变量都是附属于`window`对象的属性。

### 2。对象或隐含规则

*提醒* : `this`指向其当前所属的对象。或者我们也可以说它指向它最近的对象。

当我们隐式声明一个对象时，那么该对象内部的`this`指向该对象，而不是`window`对象。

```
let person = {
   firstName: 'blank',
   sayHi: function() {
      return `Hi ${this.firstName}!`;
   },
   determineContext: function() {
      return this === person;
   },
   whatIsThis: function() {
      return this;
   }

}; 
```

```
person.sayHi() // 'Hi blank' 
```

这里我们隐式声明了一个对象，所以`person`对象中的`this`指向了`person`对象本身，而不是`window`对象。

意思是，在`person`对象内部，做

```
...
   return `Hi ${this.firstName}`;
... 
```

与做
一样

```
...
   return `Hi ${person.firstName}`;
... 
```

让我们看看`person`对象
中`this`的值

```
whatIsThis() // {firstName: "blank" ... }
person.determineContext(); // true 
```

我们可以看到`person` === `this`为真，因此我们可以自信地说`this`正指向`person`对象。(而且不是`window`的对象。)

现在让我们看一个嵌套对象，并在内部对象中找到`this`的值。
*再次提醒* : `this`指向其当前所属的对象。

```
var person = {
   firstName: 'blank',
   sayHi: function() {
      return 'Hi ' + this.firstName;
   },
   determineContext: function() {
      return this === person;
   },
   language: {
      javaScript: function() {
         return `${this.firstName} likes JavaScript!`;
      },
      determineContext: function() {
         return this === person;
      },
      thisInLanguageObject: function() {
          return this;
      }
   }
}; 
```

```
person.determineContext(); // true 
```

在`person`对象内部，`this`的值指向`Person`对象，因为这是`this`的工作。

现在让我们在`language`对象中寻找`this`
的值

```
person.language.javaScript(); // "undefined likes JavaScript!" 
```

在`language`对象中，没有`firstName`属性，所以`this.firstName`是未定义的。

问:`language`里面的`this.firstName` === `language.firstName`是物体吗？

继续，让我们看看`language`对象中`this`的值。

```
person.language.determineContext(); // false 
```

我们可以确定`language`对象中的`this`没有指向`person`对象。

那么我们来看看`this`
的值是多少

```
person.language.thisInLanguageObject();
// {javaScript: ƒ, determineContext: ƒ, thisInLanguageObject: ƒ} 
```

### 让我们总结一下

*   `this`点当前它所属的对象
*   如果`this`在全局上下文中，那么`this`指向`window`对象
*   如果`this`在声明的对象中，那么`this`指向那个声明的对象

并且`this`总结了我们的前两条规则。在下一部分中，我们将看到`call`、`apply`和`bind`如何与`this`协同工作，还将讨论 JavaScript 中的`new`关键字。