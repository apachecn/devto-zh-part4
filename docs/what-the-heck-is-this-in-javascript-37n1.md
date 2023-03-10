# Javascript 中的“this”是什么？

> 原文：<https://dev.to/vyasriday/what-the-heck-is-this-in-javascript-37n1>

学习 JavaScript 时有很多障碍，比如`closures`、`asynchronous programming`、`this`关键词等等。这些是 JavaScript 的一部分，对于一个 JavaScript 新手来说很难理解，但是一旦你学会了它们，你就可以利用 JavaScript 提供的一切。所以今天我将谈论一个这样的超级大国，那就是`this`关键词。

所以不要再拖延了，让我们开始吧。在我们进入`this`之前，让我们先讨论一个类比来理解 JavaScript 中的**范围**。

### 比喻理解 JavaScript 中的作用域

有一座 10 层的大楼，有一名警察正在这栋大楼里寻找一名罪犯。现在让我们看看他如何找到罪犯的两种方法，或者如果他根本找不到罪犯怎么办。😨😨

(I)警察进入大楼，开始在一楼寻找罪犯。如果他在一楼没有找到罪犯，他就去一楼，如果他在一楼找到罪犯，他就把他送回警察局，否则他就一次上一层楼，直到罪犯被抓住。如果他没有找到罪犯，他就向警察局报告说在大楼里找不到罪犯。

(二)**这个很有趣**😋有一种方法可以让警察直接被射到任何一层，他在那层发现的任何人现在都是罪犯，没有办法改变这一点。*这样一种寻找罪犯的动态方式，不是吗？*😂

#### 你认为哪种做法会产生不好的后果？？

显然，第二种方法和第一种方法一样，如果真正的罪犯在另一层楼，而警察只是抓住了一个平民，那该怎么办呢？😫

因此，现在让我们带着一种动态的心态来尝试理解 JavaScript 中的两个不同的作用域:`static or lexical scope`和`dynamic scope`。

### 词法范围

在我们的第一种方法中，寻找罪犯的范围是`lexical`。警察首先在底层寻找，如果他没有找到罪犯，那么他就上一层楼，以此类推。这正是 JavaScript 中词法范围的工作方式。

```
const outer = 12;
function lexical(){
  let inner = 21;
  console.log(inner, outer);
}

lexical(); // 21 12 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码片段中,`lexical`函数首先寻找`inner variable`,它在自己的楼层找到了它。但对`outer`来说，它发现自己在上面一层。这就是 JavaScript 中词法范围的工作方式。

### 动态范围

让我们在代码中看看动态范围是如何工作的。

[https://codepen.io/vytros_7/embed/zYORZMK?height=600&default-tab=result&embed-version=2](https://codepen.io/vytros_7/embed/zYORZMK?height=600&default-tab=result&embed-version=2)

因此，我们在这里看到，当我们单击该框时，我们得到的不是警告“Gotcha Joe”，而是“Gotcha undefined”。这是为什么呢？？为什么我们的警察(eventHandler 函数)得到的不是`Joe`，而是`undefined`。这是因为我们的 eventHandler 函数使用了`this`，并且根据我们的处理函数被调用的位置`this`可以取不同的值。有办法解决的，我会尽快解决的，我保证。

到目前为止，我们已经理解了 JavaScript 中的作用域，并且看到了使用`this`会产生意想不到的结果。

现在让我们来谈谈代码中的这一点。

#### 究竟什么是`this`？

在 JavaScript 中,`this`是函数内部或作用域中的一个`object`,它可以是任何值，这取决于函数是如何定义或如何调用的。

让我们在不同的背景下理解这一点

### 情况 1 -默认此绑定

``javascript
function printThis() {
console.log(this);
}
printThis(); // in browser it can be window object or in node global object`` 

 ``在上面的代码片段中，`this`的默认值是当我们试图访问它时函数内部可用的值。现在，这个默认值取决于我们调用函数的方式和位置。这里我们在全局环境中调用我们的函数。

### 案例二:隐式绑定

```
let obj = {
  name: 'Joe',
  handler: function() {
    console.log(this.name);
  }
}

obj.handler(); // Joe

```

这里我们隐式地为函数提供了`this`的上下文。`.`左边的值是我们的处理函数的`this`的值。在这种情况下，它就是对象`obj`本身。

### 案例三:`this`与`apply`绑定

```
function printThis() {
  console.log(this.name);
}

printThis.call({name: 'Joe'}); // Joe

```

`call`方法将第一个参数作为要传递给函数的`this`的值，并调用该函数。这里我们传递了一个带有属性名的对象。这个对象将在我们的`printThis`函数中被用作`this`。

### 案例四:`new`关键词

JavaScript 中的`new`关键字用于使用类语法或构造函数创建类的新实例。你可以在我的上一篇文章
[了解原型继承](https://dev.to/_hridaysharma/understanding-classes-es5-and-prototypal-inheritance-in-javascript-n8d)中读到更多。

这是 JavaScript 世界中不同的工作方式。在我们的 click handler 例子中，我承诺我将告诉你修复代码的不同方法，这样我们将能够捕获‘Joe ’,而不是捕获`undefined`。

以下是修改后的[代码栏](https://codepen.io/vytros_7/pen/wvwydEK)上的代码

所以我们引入了两种新的处理方式`this`

*   粗箭头函数
*   `bind`方法

让我们逐一了解它们

### 胖箭头功能

ES6 为编写函数引入了新的语法。虽然它看起来只是语法糖，但它不仅仅是新的语法。粗箭头函数处理`this`的方式与普通函数非常不同。在胖箭头函数中,`this`的作用域和其他变量一样。我们已经创建了之前定义的普通函数的胖箭头函数版本，现在在我们的`eventHandlerArrowFunction`中`this`的值是类本身的实例，不管函数在哪里被调用。

### `bind`法

就像`call`方法一样 ES5 也引入了`bind`方法。两者的区别在于，`bind`将`this`的值作为第一个参数，并返回一个绑定了`this`的新函数。其中`call`使用提供的值`this`和其他参数调用函数。
在我们的 click handler 函数中，我们希望将函数的引用传递给`addEventListener`，而不是调用函数。因此，我们在那里使用了`bind`方法。

至此，我希望你理解了 JavaScript 中强大的`this`是如何工作的。``