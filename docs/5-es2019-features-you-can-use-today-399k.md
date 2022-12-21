# 您现在可以使用的 5 项 ES2019 功能

> 原文：<https://dev.to/bnevilleoneill/5-es2019-features-you-can-use-today-399k>

[![](img/3013e96e9748ab00d7e6d0a6e7915fce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--90U0JP4x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/puo867luzzwz9rxezvr7.jpg)

ECMAScript 2015，也称为 ES6，是一个花了六年时间才完成的主要版本。从那以后，负责开发 ECMAScript 标准的技术委员会 39 (TC39)每年都会发布新的标准版本。这一年度发布周期简化了流程，并使新特性快速可用，这受到了 JavaScript 社区的欢迎。

今年将发布 ECMAScript 2019(简称 ES2019)。新特性包括`Object.fromEntries()`、`trimStart()`、`trimEnd()`、`flat()`、`flatMap()`，符号对象的描述属性，可选的捕捉绑定等等。

好消息是，这些功能已经在最新版本的 Firefox 和 Chrome 中实现了，它们也可以被传输，以便旧版本的浏览器能够处理它们。在这篇文章中，我们将好好看看这些特性，看看它们是如何升级语言的。

[![LogRocket Free Trial Banner](img/4aa67f42a82d61c79b61acb13eae9479.png)](https://logrocket.com/signup/)

## 1。Object.fromEntries()

在 JavaScript 中，将数据从一种格式转换成另一种格式是很常见的。为了便于将对象转换成数组，ES2017 引入了`Object.entries()`方法。该方法将一个对象作为参数，并以`[key, value]`的形式返回该对象自己的可枚举字符串键属性对的数组。比如:

```
const obj = {one: 1, two: 2, three: 3};

console.log(Object.entries(obj));    
// => [["one", 1], ["two", 2], ["three", 3]] 
```

Enter fullscreen mode Exit fullscreen mode

但是，如果我们想做相反的事情，将一个键值对列表转换成一个对象呢？一些编程语言，比如 Python，为此提供了`dict()`函数。下划线. js 和 Lodash 中还有`_.fromPairs`函数。

ES2019 旨在通过引入`Object.fromEntries() method`为 JavaScript 带来类似的功能。这个静态方法允许您轻松地将一个键值对列表转换成一个对象:

```
const myArray = [['one', 1], ['two', 2], ['three', 3]];
const obj = Object.fromEntries(myArray);

console.log(obj);    // => {one: 1, two: 2, three: 3} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，`Object.fromEntries()`只是`Object.entries()`的反义词。虽然以前有可能达到同样的结果，但这并不简单:

```
const myArray = [['one', 1], ['two', 2], ['three', 3]];
const obj = Array.from(myArray).reduce((acc, [key, val]) => Object.assign(acc, {[key]: val}), {});

console.log(obj);    // => {one: 1, two: 2, three: 3} 
```

Enter fullscreen mode Exit fullscreen mode

请记住，传递给`Object.fromEntries()`的参数可以是实现 iterable 协议的任何对象，只要它返回一个两元素、类似数组的对象。

例如，在下面的代码中，`Object.fromEntries()`将一个 Map 对象作为参数，并创建一个新对象，其键和相应的值由 Map 中的对给出:

```
const map = new Map();
map.set('one', 1);
map.set('two', 2);

const obj = Object.fromEntries(map);

console.log(obj);    // => {one: 1, two: 2} 
```

Enter fullscreen mode Exit fullscreen mode

`Object.fromEntries()`方法对于变换对象也非常有用。考虑下面的代码:

```
const obj = {a: 4, b: 9, c: 16};

// convert the object into an array
const arr = Object.entries(obj);

// get the square root of the numbers
const map = arr.map(([key, val]) => [key, Math.sqrt(val)]);

// convert the array back to an object
const obj2 = Object.fromEntries(map);

console.log(obj2);  // => {a: 2, b: 3, c: 4} 
```

Enter fullscreen mode Exit fullscreen mode

这段代码将对象中的值转换成它们的平方根。为此，它首先将对象转换成一个数组，然后使用`map()`方法获得数组中值的平方根。结果是可以转换回对象的数组的数组。

`Object.fromEntries()`派上用场的另一种情况是处理 URL 的查询字符串，如下例所示:

```
const paramsString = 'param1=foo&param2=baz';
const searchParams = new URLSearchParams(paramsString);

Object.fromEntries(searchParams);    // => {param1: "foo", param2: "baz"} 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中，一个查询字符串被传递给`URLSearchParams()`构造函数。然后返回值，也就是一个`URLSearchParams`对象实例，被传递给`Object.fromEntries()`方法。结果是一个包含每个参数作为属性的对象。

`Object.fromEntries()`方法目前是第 4 阶段提案，这意味着它已经准备好纳入 ES2019 标准。

## 2。trimStart()和 trimEnd()

`trimStart()`和`trimEnd()`方法在技术上与`trimLeft()`和`trimRight()`相同。这些方法目前是第 4 阶段的建议，为了与`padStart()`和`padEnd()`保持一致，将被添加到规范中。我们来看一些例子:

```
const str = " string ";

// es2019
console.log(str.trimStart());    // => "string   "
console.log(str.trimEnd());      // => "   string"

// the same as
console.log(str.trimLeft());     // => "string   "
console.log(str.trimRight());    // => "   string" 
```

Enter fullscreen mode Exit fullscreen mode

为了网页兼容性，`trimLeft()`和`trimRight()`将保留为`trimStart()`和`trimEnd()`的别名。

## 3。flat()和 flatMap()

`flat()`方法使您能够轻松地连接一个数组的所有子数组元素。考虑下面的例子:

```
const arr = ['a', 'b', ['c', 'd']];
const flattened = arr.flat();

console.log(flattened);    // => ["a", "b", "c", "d"] 
```

Enter fullscreen mode Exit fullscreen mode

以前，你必须使用`reduce()`或`concat()`来获得一个平面数组:

```
const arr = ['a', 'b', ['c', 'd']];
const flattened = [].concat.apply([], arr);

// or
// const flattened =  [].concat(...arr);

console.log(flattened);    // => ["a", "b", "c", "d"] 
```

Enter fullscreen mode Exit fullscreen mode

注意，如果在提供的数组中有任何空槽，它们将被丢弃:

```
const arr = ['a', , , 'b', ['c', 'd']];
const flattened = arr.flat();

console.log(flattened);    // => ["a", "b", "c", "d"] 
```

Enter fullscreen mode Exit fullscreen mode

`flat()`还接受一个可选参数，该参数指定嵌套数组应展平的级数。如果没有提供参数，将使用默认值 1:

```
const arr = [10, [20, [30]]];

console.log(arr.flat());     // => [10, 20, [30]]
console.log(arr.flat(1));    // => [10, 20, [30]]
console.log(arr.flat(2));    // => [10, 20, 30] 
```

Enter fullscreen mode Exit fullscreen mode

`flatMap()`方法将`map()`和`flat()`组合成一个方法。它首先用提供的函数的返回值创建一个新数组，然后连接该数组的所有子数组元素。一个例子应该能使这一点更清楚:

```
const arr = [4.25, 19.99, 25.5];

console.log(arr.map(value => [Math.round(value)]));    
// => [[4], [20], [26]]

console.log(arr.flatMap(value => [Math.round(value)]));    
// => [4, 20, 26] 
```

Enter fullscreen mode Exit fullscreen mode

数组将被展平的深度级别是 1。如果你想从结果中删除一项，只需返回一个空数组:

```
const arr = [[7.1], [8.1], [9.1], [10.1], [11.1]];

// do not include items bigger than 9
arr.flatMap(value => {
  if (value >= 10) {
    return [];
  } else {
    return Math.round(value);
  }
});  

// returns:
// => [7, 8, 9] 
```

Enter fullscreen mode Exit fullscreen mode

除了正在处理的当前元素之外，回调函数还将接收元素的索引和对数组本身的引用。`flat()`和`flatMap()`方法目前是第 4 阶段提案。

## 4。符号对象的描述属性

创建符号时，可以出于调试目的向其添加说明。有时候，能够直接访问代码中的描述是很有用的。

该 ES2019 提案向符号对象添加了一个只读描述属性，该属性返回一个包含符号描述的字符串。下面是一些例子:

```
let sym = Symbol('foo');
console.log(sym.description);    // => foo

sym = Symbol();
console.log(sym.description);    // => undefined

// create a global symbol
sym = Symbol.for('bar');
console.log(sym.description);    // => bar 
```

Enter fullscreen mode Exit fullscreen mode

## 5。可选 catch 绑定

`try … catch`语句中的 catch 绑定并不总是被使用。考虑下面的代码:

```
try {
  // use a feature that the browser might not have implemented
} catch (unused) {
  // fall back to an already implemented feature 
} 
```

Enter fullscreen mode Exit fullscreen mode

在这段代码中没有使用 catch 绑定。然而，它仍然应该用来避免一个`SyntaxError`。这个提议对 ECMAScript 规范做了一个小小的修改，允许您省略 catch 绑定及其括号:

```
try {
  // use a feature that the browser might not have implemented
} catch {
  // do something that doesn’t care about the value thrown
} 
```

Enter fullscreen mode Exit fullscreen mode

## 奖励:es 2020 string . prototype . match all

`matchAll()`方法是第 4 阶段 ES2020 提案，针对正则表达式返回所有匹配的迭代器对象——包括捕获组。

为了与`match()`方法保持一致，TC39 选择了“matchAll”而不是其他建议的名称，比如“matches”或 Ruby 的“scan”。我们来看一个简单的例子:

```
const re = /(Dr\.  )\w+/g;
const str = 'Dr. Smith and Dr. Anderson';
const matches = str.matchAll(re);

for (const match of matches) {
  console.log(match);
}

// logs:
// => ["Dr. Smith", "Dr. ", index: 0, input: "Dr. Smith and Dr. Anderson", groups: undefined]
// => ["Dr. Anderson", "Dr. ", index: 14, input: "Dr. Smith and Dr. Anderson", groups: undefined] 
```

Enter fullscreen mode Exit fullscreen mode

这个正则表达式中的捕获组匹配字符“Dr ”,后跟一个点和一个空格。`\w+`匹配任意单词字符一次或多次。并且`g`标志指示引擎在整个字符串中搜索模式。

以前，您必须在一个循环中使用`exec()`方法来获得相同的结果，这不是很有效:

```
const re = /(Dr\.)  \w+/g;
const str = 'Dr. Smith and Dr. Anderson';
let matches;

while ((matches = re.exec(str)) !== null) {
  console.log(matches);
}

// logs:
// => ["Dr. Smith", "Dr.", index: 0, input: "Dr. Smith and Dr. Anderson", groups: undefined]
// => ["Dr. Anderson", "Dr.", index: 14, input: "Dr. Smith and Dr. Anderson", groups: undefined] 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，尽管可以使用全局标志`g`来使用`match()`方法来访问所有匹配，但它不提供匹配的捕获组或索引位置。比较:

```
const re = /page (\d+)/g;
const str = 'page 2 and page 10';

console.log(str.match(re));    
// => ["page 2", "page 10"]

console.log(...str.matchAll(re)); 
// => ["page 2", "2", index: 0, input: "page 2 and page 10", groups: undefined] 
// => ["page 10", "10", index: 11, input: "page 2 and page 10", groups: undefined] 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

在这篇文章中，我们仔细研究了 ES2019 中引入的几个关键功能，包括`Object.fromEntries()`、`trimStart()`、`trimEnd()`、`flat()`、`flatMap()`、符号对象的描述属性以及可选的 catch 绑定。

尽管一些浏览器供应商还没有完全实现这些特性，但是由于 Babel 和其他 JavaScript transpilers，您仍然可以在您的项目中使用它们。

ECMAScript 的开发速度在最近几年加快了，新的特性不时地被引入和实现，所以一定要查看一下[完成提案的列表](https://github.com/tc39/proposals/blob/master/finished-proposals.md)以了解最新的新特性。你有什么建议吗？在评论中分享吧！

* * *

## Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![LogRocket Dashboard Free Trial Banner](img/0abf868fe5ccbed99d71cb8d9e81ed98.png)](https://logrocket.com/signup/)

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

[免费试用](https://logrocket.com/signup/)。

* * *

你今天可以使用的 5 个 ES2019 功能的帖子[首先出现在](https://blog.logrocket.com/5-es2019-features-you-can-use-today/)[的日志博客](https://blog.logrocket.com)上。