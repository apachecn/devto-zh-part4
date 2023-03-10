# JavaScript 数据类型检查清单

> 原文：<https://dev.to/hugo__df/javascript-data-type-check-cheatsheet-5b7d>

> JavaScript (JS)是一种轻量级、解释型或即时编译的编程语言，具有一流的功能。
> 
> [MDN 网络文档- JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)

这篇文章详细介绍了常见的 JavaScript 数据类型检查、陷阱和惯用的解决方法。

## 经典 JavaScript 数据类型检查

这里是 JavaScript 中最常见的数据类型检查的集合。无论你想检查一个变量是否包含一个日期，一个承诺，一个普通的旧 JavaScript 对象还是一个数组，都在这里。

从数字、布尔、字符串等基本类型到检测函数，应有尽有。

### 检查 JavaScript 变量是否包含对象

`typeof`为对象输出`'object'`。

对于`null`和数组也是如此。

```
const object = {};

console.log(typeof object); // 'object'
console.log(typeof null); // 'object'
console.log(typeof []); // 'object'

console.log(object instanceof Object); // true
console.log(null instanceof Object); // false
console.log([] instanceof Object); // true 
```

更重要的是，很像数组的情况，如果有帧间通信，它倾向于共享对象和数组(参见 [JavaScript 数组类型检查-“is array”vs 对象深入](https://dev.to/hugo__df/detecting-object-vs-array-in-javascript-by-example-297d))。因此，检查某个东西是简单对象还是一个类的实例是很困难的。

在 JavaScript 中你会注意到一切都是一个对象，当你试图访问一个不存在的属性时，它会悄悄地失败(即。返回`undefined` ):

```
console.log('aaaaa'.noProperty); // undefined
console.log([].nothing); // undefined 
```

在惯用的 JavaScript 代码中，我们利用这个属性来进行防御，例如，如果我们期望一个对象有一个`growl`方法，但是可能会传入其他东西:

```
function safeGrowl(anything) {
  if (anything.growl) {
    anything.growl()
  }
}

safeGrowl('hello'); // nothing
safeGrowl({ growl() { console.log('Growl!') }}); // Growl! 
```

这个故事的寓意是:不要去检查某样东西是不是一个对象，检查它有没有你需要的属性(这就是所谓的[鸭子打字](https://en.wikipedia.org/wiki/Duck_typing))。

### 检查 JavaScript 中的值是否为字符串

对于字符串，我们可以使用`typeof`检查。

就像对象检查一样，当你试图使用不是字符串的东西作为字符串时，JavaScript 不会失败，它只会强迫它或对它调用`.toString`。

```
const string = 'Hello World';
console.log(typeof string); // 'string'

// Implicit coercion to string using templates
const helloCount = 2;
const newString = `Hello number ${helloCount} at ${new Date('2019-06-23T21:00:26.861Z')}`;

console.log(newString);
// 'Hello number 2 at Sun Jun 23 2019 22:00:26 GMT+0100 (British Summer Time)' 
```

这适用于日期和数字。对于数组和其他不直接实现 toString 方法的对象，我建议使用 JSON.stringify.

```
const myArray = ['a', 'b', 'c'];
const mySimpleObject = { key: 'value' };

console.log(`${myArray} ${mySimpleObject}`); // 'a,b,c [object Object]'
console.log(`${JSON.stringify(myArray)} ${JSON.stringify(mySimpleObject)}`)
// '["a","b","c"] {"key":"value"}' 
```

### 检查一个值是否是一个 JavaScript 数字/整数

JavaScript 数字非常有趣。他们有一个类似于`object`检查的陷阱，那就是`NaN`(不是一个数字)值。`NaN`通常是试图做算术的输出，其中一个操作数不是数字。

`NaN`的怪癖在于它不等于它本身，它实际上是一个数，就像`Infinity`和`- Infinity`T3 一样

```
console.log(NaN == NaN); // false
console.log(NaN === NaN); // false

console.log(typeof NaN); // 'number'
console.log(typeof Infinity); // 'number'
console.log(typeof -Infinity); // 'number'
console.log(typeof 123); // 'number' 
```

#### 检查数字是否不是`NaN`(不是数字)

一个`NaN`检查只是:

```
const a = NaN;

function isNotANumber(maybeNotANumber) {
  return maybeNotANumber === maybeNotANumber;
}

isNotANumber(a); // true 
```

推荐的方法如下，有一个内置的`Number.isNaN`函数:

```
console.log(Number.isNaN(NaN)); // true
console.log(Number.isNaN('abc')); // false
console.log(Number.isNaN(1234)); // false
console.log(Number.isNaN(123.11)); // false
console.log(Number.isNaN(true)); // false 
```

`Number.isNaN`和`isNaN`全局变量的区别在于`Number.isNaN`检查传入的值是一个数字*而*是`NaN`。

旧的全局`isNaN`函数只是字面上检查某个东西不是数字。

```
console.log(isNaN(NaN)); // true
console.log(isNaN('abc')); // true
console.log(isNaN(1234)); // false
console.log(isNaN(123.11)); // false
console.log(isNaN(true)); // false 
```

#### 检查 JavaScript 变量是否包含整数

要检查 JavaScript 变量(或值)是否为整数，我们可以使用`Number.isInteger` :

```
console.log(Number.isInteger(123)); // true
console.log(Number.isInteger(12.3)); // false
console.log(Number.isInteger(123.0)); // true 
```

#### 检查 JavaScript 变量是否包含可用的数值

为了检查我们是否得到了可用的输入值，我们应该检查类型是否是`number`并且值是否不是 NaN:

```
function isValidNumber(maybeNumber) {
  return typeof maybeNumber === 'number' && !Number.isNaN(maybeNumber);
}

console.log(isValidNumber('aaaaa')); // false
console.log(isValidNumber(NaN)); // false
console.log(isValidNumber(123)); // true
console.log(isValidNumber(1.23)); // true 
```

### 检查一个值是否为布尔值

与 JavaScript string 和 number 数据类型一样，JavaScript 中的模式是假设某物是布尔值(或者将其转换为布尔值)，而不是检查它是否是布尔值。这是因为在 JavaScript 中，由于松散的类型，我们可以使用带有非布尔值的逻辑运算符，这通常通过“真”和“假”的概念来解释。

> 当 JavaScript 需要一个布尔值并且它被给定了下面的一个值时，它将总是计算为“falsy”
> 
> [MDN 网络文档- Falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy)

有问题的值(falsy 值)是:`false`、`0`、`''`(或其他空字符串)、`null`和`undefined`。任何其他值都将被评估为 true。

在某些情况下，`false`表示除了`undefined`之外的其他值，在这种情况下，可以通过使用`typeof` :
来检查值是否为 false*以及*是否为布尔值

```
console.log(typeof true); // 'boolean'
console.log(typeof false); // 'boolean' 
```

### 检查变量是否包含数组

为了检查 JavaScript 变量是否是一个数组，有一个内置的`Array.isArray`。

JavaScript 数组的有趣之处在于它们只是对象。

```
console.log(([]) instanceof Object); // true
console.log(typeof []); // 'object' 
```

一种避免数组类型化的方法是使用一个`.length`属性的存在。然而，这可能相当脆弱，因为没有强制规定数组应该是唯一具有`.length`属性的类型。这个模式看起来像这样:

```
function processList(maybeArray) {
  if (!maybeArray.length) {
    return []
  }
  return maybeArray.map(i => i); // literally copy
} 
```

现在这段代码实际上并没有检查`maybeArray`是否是一个数组。它有点像，但在同一行代码中。`!maybeArray.length`，它还声明了`maybeArray`必须有一个非伪长度，即。如果*实际上是*数组，那么它的长度也不应该是 0(不能为空)。

欺骗上面的内容并使其在`.map`崩溃是很简单的，例如使用下面的数据:`{ length: 'aaaa' }`。这不是重点，如果这个功能的消费者是可信的，这种检查就可以了。

然而，使用 Array.isArray 的工作方式如下:

```
console.log(Array.isArray({})); // false
console.log(Array.isArray(new Map())); // false
console.log(Array.isArray(new Set())); // false

console.log(Array.isArray([])); // true
console.log(Array.isArray(new Array())); // true 
```

要进一步了解我们如何检查数组的内部机制，请参见 [JavaScript 数组类型检查——“是数组”与对象深度](https://dev.to/hugo__df/detecting-object-vs-array-in-javascript-by-example-297d)。JavaScript 中内置 JavaScript 数据类型(如数组、对象和日期)的一个大问题是，帧之间的通信意味着构造函数，因此`instanceof`检查不起作用。

### 检查一个对象是否是一个特定类/构造函数的实例

假设你有一个设置变量，你想检查它是一个反应组件，你可以做:

```
import React, { Component } from 'react';

const myComp = new Component();

function isReactComponent(maybeComponent) {
  return maybeComponent instanceof Component;
}

isReactComponent(myComp);
// true

isReactComponent({});
// false 
```

这也适用于构造函数:

```
function Dog (name) {
  this.name = name
}

const max = new Dog('Max');

console.log(max instanceof Dog); // true 
```

另一件有趣的事情是，它一直沿着原型链/类层次向上工作:

```
console.log(({}) instanceof Object); // true
console.log((new Dog) instanceof Object); // true 
```

### 检查一个对象是否有错误

`Error`只是一个构造函数/类。因此，我们可以用同样的方式检查`React.Component`或`Dog`类:

```
function isError(maybeError) {
  return maybeError instanceof Error;
}

isError(new Error('Something went wrong')); // true
isError(new EvalError()); // true
isError(new InternalError()); // true
isError(new RangeError()); // true
isError(new ReferenceError()); // true
isError(new SyntaxError()); // true
isError(new TypeError()); // true
isError(new URIError()); // true 
```

在 MDN 上查看关于[基础对象的更多信息。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference#Fundamental_objects)

### 检查有效的 JavaScript 日期字符串(可解析的日期字符串)

```
function isValidDateString(maybeDateString) {
  return !Number.isNaN(Number(new Date(maybeDateString)));
}

console.log(isValidDateString('abcd')); // false
console.log(isValidDateString(1234)); // true
console.log(isValidDateString('2019-06-23T22:00:26.861Z')); // true 
```

上面的函数实际上并不检查某个东西是否是有效的字符串，而是检查它是否可以转换成有效的日期。

对于大多数意图和目的，它*将*捕捉不可靠的日期字符串，并具有不可读的好处，代价是允许数字时间戳被传入。更恰当的名字可能是`isConvertibleToDate`。不允许数字只是增加一个`typeof maybeDateString === 'string'`的情况。

### 检查有效的 JavaScript 日期

为了检查某个东西是否有效，我们将采用与检查它是否可转换为日期相同的方法

```
function isValidDateObject(maybeDate) {
  return (
    typeof maybeDate === 'object' &&
    !Number.isNaN(Number(new Date(maybeDate))
  );
}

isValidDateObject('abc'); // false
isValidDateObject(1234); // false
isValidDateObject('2019-06-23T22:00:26.861Z'); // false
isValidDateObject(new Date('2019-06-23T22:00:26.861Z')); // true 
```

您也可以应用`instanceof`方法:

```
function isValidDateObject(maybeDate) {
  return maybeDate instanceof Date;
}

isValidDateObject('abc'); // false
isValidDateObject(1234); // false
isValidDateObject('2019-06-23T22:00:26.861Z'); // false
isValidDateObject(new Date('2019-06-23T22:00:26.861Z')); // true 
```

这有一些跨框架的问题，你永远不知道什么时候有人可能会搞乱全局`Date`来用他们自己的定制、非标准版本替换它。

### 检查 JavaScript 变量是否是一个承诺

使用`instanceof`和所有常见的跨框架或自定义实现警告来完成承诺检查:

```
console.log({} instanceof Promise); // false 
```

有了承诺，也就有了未来的问题。对于大多数意图和目的来说，这也可能是承诺，但不会通过我们的上述检查。

在支持异步/等待的环境中，您还会注意到，`await`-调用不返回承诺的函数不会导致任何意外的副作用，函数的返回值(即使它不是异步函数)可以像没有使用`await` -ed 一样存储。

### 检查 JavaScript 变量是否是函数

正如 MDN Web Docs 上提到的，JavaScript 是一种“具有一流功能的编程语言”。一级函数，只是意味着函数和其他变量一样被对待。

```
console.log(typeof (() => {})); // 'function'
console.log(typeof function () {}); // 'function'
function myFunc () {}
console.log(typeof myFunc); // 'function' 
```

请参考对象示例，看看惯用的“如果该函数存在，则运行该函数”是什么样子的。

## 调试 JavaScript 数据类型问题

以下各项在被`console.log`编辑时是什么样子的？

其中一个大问题是`console.log`倾向于使用它的`.toString()`方法传递任何对象。很多时候，模式匹配、注销`typeof`和注销对象的`JSON.stringify`版本的组合给出了很好的结果。

### 弄清楚某事是否是承诺

忘了`await`一个返回承诺的函数(包括一个`async`函数)。

您通常希望对承诺的输出做一些事情:

#### 一个理应回报的承诺清单

```
const fetchList = async () => ['first-item'];

async function doSomething() {
  const [firstItem] = fetchList();
}

doSomething()
// UnhandledPromiseRejectionWarning:
// TypeError: fetchList is not a function or its return value is not iterable 
```

#### 一个理应回报对象的承诺

```
const fetchObj = async () => ({ property: 'value' });

async function doSomething() {
  const obj = fetchObj();
  console.log(obj.property);
  console.log(obj);
  console.log('done')
}

doSomething()
// undefined
// Promise {
// { property: 'value' },
// and so on 
```

### 调试数组 vs 类数组

JavaScript 中有一些类似数组的对象，例如`arguments`、`NodeList`(T2 的输出)。

要做的第一件事就是注销它们:

```
const anchors = document.querySelectorAll('a');
console.log(anchors); // { "0": {}, "1": {} }

function multiVariateFn() {
  console.log(arguments)
}

multiVariateFn(1, 2, 3); // [Arguments] { '0': 1, '1': 2, '2': 3 } 
```

将这些输出与
进行比较

```
console.log([1, 2, 3]); // [1, 2, 3] 
```

下面是将它们转换成常规的`Array` -s 的老派方法(这意味着你可以使用 Array.forEach/.map 等。):

```
const anchors = Array.prototype.slice.call(
  document.querySelectorAll('a'),
  0
);

function multiVariateFn() {
  const args = Array.prototype.slice.call(arguments, 0);
  return args.reverse();
}

console.log(multiVariateFn(1, 2, 3, 4)); // [4, 3, 2, 1]
console.log(multiVariateFn('a', 'b', 'c')); // ['c', 'b', 'a'] 
```

ES6 方法看起来更接近于此，它们分别利用数组展开语法和 rest 参数语法。

```
const anchors = [...document.querySelectorAll('a')];

function multiVariateFn(...args) {
  return args.reverse();
}

console.log(multiVariateFn(1, 2, 3, 4)); // [4, 3, 2, 1]
console.log(multiVariateFn('a', 'b', 'c')); // ['c', 'b', 'a'] 
```

更保守的方法可以利用`Array.from`(参见 MDN 网络文档中的[数组):](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from) 

```
const anchors = Array.from(document.querySelectorAll('a'));

function multiVariateFn() {
  return Array.from(arguments).reverse();
}

console.log(multiVariateFn(1, 2, 3, 4)); // [4, 3, 2, 1]
console.log(multiVariateFn('a', 'b', 'c')); // ['c', 'b', 'a'] 
```

## 源材料-延伸阅读

在创建这个 JavaScript 数据类型检查指南时，我从一些热门的相关帖子中获得了灵感:

*   [http://tobyho.com/2011/01/28/checking-types-in-javascript/](http://tobyho.com/2011/01/28/checking-types-in-javascript/)
*   [https://webbjocke.com/javascript-check-data-types/](https://webbjocke.com/javascript-check-data-types/)
*   [https://ultimate courses . com/blog/understanding-JavaScript-types-and-reliable-type-checking](https://ultimatecourses.com/blog/understanding-javascript-types-and-reliable-type-checking)

以及 JavaScript 的[MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript)。

丹尼尔·法齐奥