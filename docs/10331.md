# JavaScript 中关于“这个”的所有内容

> 原文：<https://dev.to/taesup/all-about-this-in-javascript-30j>

对于刚开始学习 JavaScript 的人来说，`this`关键字经常是困惑和头痛的来源，所以我决定写下我的 7 条“规则”,告诉他们如何弄清楚什么是`this`。

### 1。)全球背景

默认情况下，任何在全局范围内使用`function`关键字声明的函数，基本上任何没有在对象内声明的函数或者调用时前面有`new`的函数，都会有一个全局上下文的`this`。对于浏览器，全局上下文是`window`，对于节点，全局上下文是`global`。

```
function printThis() {
  console.log(this);
}

printThis(); // this will print window in your browser 
```

所以给定上面的代码，只要调用函数`printThis`就会一直打印出`window`。

### 2。)往左，往左

一旦`printThis`函数被放置在一个对象上并通过该对象被调用，上下文，或`this`，将改变到它所附加的对象。这里的关键概念是，函数需要放在一个对象**上，并且**需要通过该对象调用。如果这两种情况都不成立，那么这条规则就不适用。

```
function printThis() {
  console.log(this);
}

const obj = {
  print: printThis
};

obj.print(); // this will print obj
printThis(); // this will still print window 
```

找出任何函数的上下文的最简单的方法是看函数名的左边，看看它“附加”了什么。在上面的代码中，由于`print`函数被“附加”`obj`，调用`print`时的`this`将是`obj`。

当我们调用`printThis`时，`printThis`的左边没有任何东西，所以现在，我们可以有把握地假设`printThis`的上下文将是全局上下文。

### 3。)申请

使用`apply`方法，我们可以改变任何函数的`this`。通常这是用来取出一个已经附加到一个对象上的函数并调用它，就这一次，使用一个不同于正常情况的上下文。

换句话说，`apply`允许我们从任何地方借用一个函数，并且调用这个函数，就这一次，使用一个不同于正常情况的上下文。

```
function printThis() {
  console.log(this);
}

const obj = {
  print: printThis
};

const newContext = {
  name: "I'm the context now"
};

obj.print.apply(newContext); // this will print `newContext` 
```

`apply`方法的第一个参数总是我们希望该函数在其中起作用的新上下文。这只“适用于”那一行代码。在任何其他线路上调用`obj.print()`,就像正常情况一样。

Apply 可以接受第二个参数，它总是一个数组。这个数组应该按顺序保存您想要发送给`print`函数的任何参数。我不会再谈论这个话题，但我需要强调一点，这样我们就可以讨论...

### 4。)打电话

Call 基本上做了和`apply`完全一样的事情，但是主要的区别是，你可以传入和`print`通常所接受的一样多的参数，而不是为第二个参数传入一个数组。

```
function printThis() {
  console.log(this);
}

const obj = {
  print: printThis
};

const newContext = {
  name: "I'm the context now"
};

obj.print.call(newContext); // this will print `newContext` 
```

我喜欢用的助记符是`apply = array, call = commas`。不过，在目前为止的所有案例中，都有某种可视化的指示器，可以显示该行代码的上下文。如果没有，那么您可以放心地(暂时)假设该上下文将是全局上下文。至少，在你看到这个词之前...

### 5。)绑定

Bind 的工作方式很像 Call 和 Apply，但是最大的不同是 bind 不会立即触发这个函数。bind 将做的是返回一个原始函数的副本，但是使用新的上下文**永久地**“绑定”。

```
function printThis() {
  console.log(this);
}

const obj = {
  print: printThis
};

const newContext = {
  name: "I'm the context now"
};

// newPrint's this will always be 'newContext'
const newPrint = obj.print.bind(newContext);
// will print newContext. Visual indicators be damned.
newPrint();

// even if we put 'newPrint' on an object
// then call it like Rule 2, it will still print 'newContext'
obj.boundPrint = newPrint;
// this will still print 'newContext' instead of 'obj' like we would expect with Rule 2.
obj.boundPrint() 
```

原始功能保持不变。记住`bind`将创建一个原始函数的副本，这样原始函数就可以保持它在上下文中的位置。除非您使用创建了原始函数...

### 6。)箭头功能

唯一一次`bind`不会改变一个函数的上下文，是如果这个函数是使用[箭头函数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)声明的。我不会深入讨论箭头函数的所有细节，但箭头函数的核心部分之一是箭头函数的`this`是按字典顺序确定的。这只是一个有趣的说法，`this`是通过检查编写 arrow 函数时的上下文来确定的。

到目前为止，我们看到的所有函数都是在全局上下文中编写的，这意味着无论何时使用箭头函数，声明它的上下文都将是全局上下文或`window`。即使函数是在对象中声明的，上下文仍然是全局上下文。

```
// arrow function on the global scope
const arrowFunction = () => {
  console.log(this);
};

arrowFunction(); // will print 'window'

// arrow function in an object
const obj = {
  print: () => { console.log(this); }
};
obj.print() // will print 'window'

// even call/apply/bind can't change the context
const boundPrint = obj.print.bind(obj);
boundPrint(); // will still print window 
```

这看起来不错，但是箭头函数的上下文什么时候改变过呢？

### 7。)新的关键字

`new`关键字在 JavaScript 中很特殊，因为它可以将一个函数转换成一个类，并由此创建一个新的“函数上下文”这个很难解释，所以我尽量用代码展示。

```
// using new in front of Person will turn this function into a class
function Person(name) {
  // using `new` in front of Person it also creates a new context here
  // so all functions within here will have a `this` of person
  // where person is an instance of Person
  this.name = name;

  const print = () => {
    console.log(this.name); // this will always be an instance of Person
  };

  return {
    print: print
  };
}

// person === instance of the class Person
// Person === a class called Person
const person = new Person('Alice');

// somebody === instance of the class Person
// Person === still the same class Person
const somebody = new Person('Bob');

person.print()   // will print 'Alice'
somebody.print() // will print 'Bob'

// bind can't change the print's context since it's an arrow function
const calvin = { name: 'Calvin' };
const boundPrint = person.print.bind(calvin);
boundPrint(); // this will still print 'Alice' 
```

我不会过多地进入 OOP，但是`new`关键字对于创建一个新的“上下文”是至关重要的，任何在类中声明的函数都将继承这个新的“上下文”。这适用于箭头函数和使用关键字`function`创建的函数。这不同于在任何常规函数中声明函数。两者的上下文将取决于规则 1 - 5。但是一旦我们在外层函数前面有了新的关键字，那么规则 7 就生效了。

我知道`this`的规则可能还是有点混乱，但我希望我已经澄清了一点。此外，如果你不确定什么是`this`，你可以一次一条地浏览这些规则。或者更好的是，仅仅控制台. log`this`是什么。然后参考规则，找出刚刚发生了什么。

仅此而已。JavaScript 中可以设置的 7 种方式`this`！请让我知道我是否犯了什么错误，或者我是否错过了设置`this`的方法。

**编辑** :
浏览器中有一些功能可以改变你的上下文(`this`)，比如`.addEventListener`。在这些情况下，上下文将永远是`.addEventListener`所依附的对象。所以，最好不要在这里使用箭头函数，因为所述箭头函数的上下文将被绑定到它被编写的地方。在大多数情况下，它的写入位置与您附加此事件侦听器的对象不匹配。我认为这是规则 2 的一个特例。