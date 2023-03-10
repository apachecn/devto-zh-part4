# 也许打电话给我？初学者的复试

> 原文：<https://dev.to/shimphillip/call-me-maybe-callbacks-for-beginners-k60>

在 JavaScript 中，函数被称为一等公民。这意味着它们可以存储在变量中，作为参数传递给函数，并从函数返回。回调充分利用了这一特性，因为它是一个作为参数传递给另一个函数并在那里被调用的函数。

* * *

## 只是一个函数

让我们来看看控制台日志的这个简单代码片段。

```
console.log("Hello There"); // Hello There
console.log("I am a tomato!"); // I am a tomato! 
```

很直白。如您所料，上面的代码将逐行执行。让我们把这个稍微调一下。因为我们总想输出“我是番茄！”在“Hello There”之后，让我们将其自动化，以便当“Hello There”被打印时，“我是一个番茄！”默认情况下也会打印。第一步是将我们的每个字符串包装在它们自己的函数中。

```
function greet() {
  console.log("Hello There");
}

function introduce() {
  console.log("I am a tomato!");
} 
```

现在我们将传入' introduce '函数作为对' greet '函数的回调，并在内部调用它。确保您只将函数定义作为不带()的回调函数传入，如果您在传入函数时追加()，则“introduce”将会立即被调用，而不是等待在“greet”函数中执行。

```
function greet(callback) {
  console.log("Hello There"); // Hello There!
  callback(); // I am a tomato!
}

function introduce() {
  console.log("I am a tomato!");
}

greet(introduce); 
```

按照惯例，作为参数的回调函数字面上称为“回调”，通常你会简称为“cb”。

当我们在执行回调的函数中添加参数时，回调变得更加强大。让我们通过赋予函数更改名称的能力来使我们的“引入”函数动态化。

```
function greet(callback) {
  console.log("Hello There"); // Hello There!
  callback("cucumber"); // I am a cucumber!
}

// Remember, it's possible to store a function inside a variable
var introduce = function(name = "tomato") {
  console.log(`I am a ${name}!`);
}

greet(introduce); 
```

* * *

## 复用性

回调非常方便地重用，因为它们只是 JavaScript 函数。我们将添加另一个名为“callPhone”的函数，然后依次运行原来的“greet”函数和“callPhone”函数。

```
function greet(callback) {
  console.log("Hello There"); // Hello There!
  callback("cucumber"); // I am a cucumber!
}

function callPhone(callback) {
  console.log("Hello"); // Hello!
  callback(); // I am a tomato!
}

var introduce = function(name = "tomato") {
  console.log(`I am a ${name}!`);
}

greet(introduce);
callPhone(introduce); 
```

* * *

## 事件

回调在事件监听器中是必须的。我们再举一个场景。我们想最初说'你好，世界'，然后只要用户点击按钮，渲染'我是一个西红柿！'在控制台上。我们该怎么做？

我们需要使用`addEventListner`方法并添加到按钮 DOM (HTML 元素)中。`addEventListener`接受两个参数。第一个参数是我们想要监听的事件类型，第二个参数是在指定事件被触发后执行的回调函数。

```
<button id="button">Who am I?</button> 
```

```
const button = document.getElementById("button");
function introduce() {
  console.log("I am a tomato!");
}

button.addEventListener("click", introduce); // I am a tomato! 
```

或者，您可以直接插入一个匿名函数作为回调函数。

```
const button = document.getElementById("button");

button.addEventListener("click", function() {
  console.log("I am a tomato!");
}); // I am a tomato! 
```

`addEventListener`是一个特殊的方法，它会自动为我们调用回调操作。

* * *

## 网络请求

当我们向一个 API 发出 AJAX HTTP 网络请求(称为异步操作)时，我们的请求需要一些时间来处理并返回响应。为了检索响应数据，使用了回调。其中一个流行的实现是使用 jQuery 的`get`方法。API 服务器将执行并向回调函数提供响应数据(通常带有状态),通知我们请求是否成功。

```
// Grabs an filler array of objects 
const url = "https://jsonplaceholder.typicode.com/posts"; 

$.get(url, function(data, status) {
  console.log(data);
}) 
```

并不是每个 API 都使用回调函数来补充数据，它们可以使用允许函数链接的承诺来处理响应。这个主题超出了范围，不会在本文中讨论。

* * *

## 高阶函数

ES6 高阶函数的新特性也利用了回调函数。这些是内置的数组方法，它们将根据回调函数传递的逻辑生成一个新数组。这些方法包括 forEach、map、filter、reduce 等...这些方法将接受一个回调函数，并为其提供当前值、索引和整个数组。

```
const numbers = [1,2,3];

numbers.forEach((number, index, array) => {
  console.log(number); // 1, 2, 3
  console.log(index); // 0, 1, 2
  console.log(array); // [1,2,3], [1,2,3], [1,2,3]
}) 
```

* * *

## 回调地狱

回调的一个缺点是它有可能被深度嵌套，使得可读性和可维护性变得非常困难。当它发生时，它被称为回调地狱。我们将以第一个例子为例，用更多的逻辑展开它。

```
function greet(distractMe, introduce) {
  console.log("Hello There"); // Hello There!
  distractMe(name => {
    introduce(name, hangUp => {
      console.log("Good Bye")
    })
  });
}

function distractMe(callback) {
  console.log("I am distracting you!!!");
  callback("cucumber");
}

var introduce = function(name = "tomato", callback) {
  console.log(`I am a ${name}!`);
  callback();
}

greet(distractMe, introduce); // =>
// Hello There
// I am distracting you!!!
// I am a cucumber!
// Good Bye 
```

老实说，“the”greet 函数的例子没有那么糟糕，只有几个花括号和圆括号，但是想象一下它更复杂，函数开始相互依赖！

* * *

## 总结

感谢您的阅读！回调是 JavaScript 的重要组成部分，因为它在以下方面具有多功能性:

*   事件
*   复用性
*   异步性质
*   高阶函数。