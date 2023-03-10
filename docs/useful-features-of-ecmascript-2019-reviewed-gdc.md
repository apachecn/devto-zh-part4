# ECMAScript 2019 的有用功能已审核

> 原文：<https://dev.to/yashints/useful-features-of-ecmascript-2019-reviewed-gdc>

首先，让我为没有像我希望的那样经常写信道歉。这是疯狂忙碌的几周，我在 2019 年 DDD 珀斯大会上的演讲充满了乐趣。

好了，这一次我想让我们看看添加到 [ECMAScript 2019(又名 ES2019 或 ES10)](https://www.ecma-international.org/ecma-262/10.0/index.html) 中的新功能，因为它们超级令人兴奋，让我们的生活变得更加轻松😎。

## TLDR；

一目了然，这个版本在`Array.prototype`、`flat`和`flatMap`上增加了几个内置函数。然后我们用`Object.fromEntries`直接把`Object.entries`的返回值变成一个新的*对象*。

在广泛使用的非标准版本`String.prototype.trimLeft`和`trimRight`之上，我们还有`String.prototype`上的`trimStart`和`trimEnd`。

另一个令人兴奋的特性是可选的`catch`绑定参数。除此之外，还有一些 JSON 的改进，增加了`Symbol.prototype.description`，允许你为你的符号指定一个描述，`JSON.stringify`返回格式良好的 **UTF-8** ，不管输入是什么，最后澄清`Function.prototype.toString`，要求它返回相应的原始源文本或标准占位符。

如果你准备好了，让我们开始吧。

### `Array.prototype.{flat, flatMap}`

是一个新的方法，让你从一个多维数组创建一个一维数组。

假设我们有一个如下所示的数组:

```
const myArray = [1, 2, 3, [4, 5, 6, [7, 8, 9, [10, 11, 12]]]]; 
```

Enter fullscreen mode Exit fullscreen mode

在`flat`之前，如果你想做到这一点，你必须这样做:

```
const myNewArray = [].concat.apply([], myArray)
// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] 
```

Enter fullscreen mode Exit fullscreen mode

或者:

```
var myNewArray = myArray.reduce(
  function(prev,curr) {
    return prev.concat(curr)
  }
)
// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] 
```

Enter fullscreen mode Exit fullscreen mode

有了这个新的附加功能，事情就简单了:

```
var myNewArray = myArray.flat(4);
// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] 
```

Enter fullscreen mode Exit fullscreen mode

您也可以连锁多个呼叫:

```
var myNewArray = myArray.flat().flat().flat().flat();
// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] 
```

Enter fullscreen mode Exit fullscreen mode

`flat`函数的参数只是指定它应该在数组中查看多深。如果你不确定数组有多深，只需使用`Infinity`作为输入:

```
var myNewArray = myArray.flat(Infinity);
// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] 
```

Enter fullscreen mode Exit fullscreen mode

如果您不提供任何输入，默认情况下，它只会进入下一级:

```
var myNewArray = myArray.flat(); 
// [1, 2, 3, 4, 5, 6, Array(4)]; 
```

Enter fullscreen mode Exit fullscreen mode

* * *

`flatMap`另一方面，允许您使用映射函数映射每个元素，然后将结果平铺到一个新的数组中。可以把它想象成用一个`flat`链接一个`map`函数。然而，它在使用和执行效率方面确实很有帮助。

```
let myArray = [1, 2, 3, 4, 5];

let mappedArray = myArray.map(x => [x, x * 2]);
// [Array(2), Array(2), Array(2), Array(2), Array(2)]
// 0: (2)[1, 2]
// 1: (2)[2, 4]
// 2: (2)[3, 6]
// 3: (2)[4, 8]
// 4: (2)[5, 10]

let mappedFlattenedArr = mappedArray.flat();

// [1, 2, 2, 4, 3, 6, 4, 8, 5, 10]

let myNewArray = myArray.flatMap(v => [v, v * 2]);
// [1, 2, 2, 4, 3, 6, 4, 8, 5, 10] 
```

Enter fullscreen mode Exit fullscreen mode

### `String.prototype.{trimStart, .trimEnd}`

这些方法对我们的作用是显而易见的。请记住，我们的`trimLeft`将被`trimStart`取代，而`trimRight`将被`trimEnd`取代。

```
let message = ' This is a string with white space around ';

message = message.trimStart();
// 'This is a string with white space around '

message = message.trimEnd();
// 'This is a string with white space around' 
```

Enter fullscreen mode Exit fullscreen mode

### `Object.fromEntries`

这个方法获取一个`Iterable`并将键值对转换成一个对象。但是让我们看看什么是`Iterable`:

> JavaScript 支持一种协议，通过这种协议，诸如数组之类的对象可以被诸如`for` … `of`和扩展操作符`...`之类的控制结构用来顺序地遍历数据。这被称为可迭代的，支持这种功能的数据结构被称为可迭代的。虽然 JavaScript 从一开始就为映射、数组和集合提供了 iterable 属性，但普通对象在默认情况下没有这种属性。

要了解这一点:

```
let entries = new Map([["name", "john"], ["age", 22]]);

let newObj = Object.fromEntries(entries);
// { name: 'john', age: 22 } 
```

Enter fullscreen mode Exit fullscreen mode

一个真实的用例是解析查询字符串:

```
let query = Object.fromEntries(new URLSearchParams('foo=bar&baz=qux'));

// { foo: 'bar', baz: 'qux' } 
```

Enter fullscreen mode Exit fullscreen mode

### 可选捕捉绑定

可选的 catch 绑定允许我们在 catch 块中使用不带`error`参数的`try/catch`。

以前，不管你是否关心`err`，你都必须使用这种语法，比如当你不得不回退到支持旧浏览器的特性:

```
try {
  // try to use a web feature which may not be implemented
} catch (unused) {
  // fall back to a less desirable web feature with broader support
} 
```

Enter fullscreen mode Exit fullscreen mode

用`ES2019`你可以做:

```
try {
  // ...
} catch {
  // ...
} 
```

Enter fullscreen mode Exit fullscreen mode

### `Symbol.description`

这个新的只读描述属性是一个字符串，返回对`Symbol`对象的可选描述。鼓励使用这种方法，而不要使用`Symbol.prototype.toString`
，因为返回的内容并不明显。

```
let description = 'This symbol represents an emoji 😁';

let mySym = Symbol(description);
// Symbol('This symbol represents an emoji 😁')

console.log(mySym.description);
'This symbol represents an emoji 😁' 
```

Enter fullscreen mode Exit fullscreen mode

### `Function.toString`

这个方法非常有用，它返回一个函数的源代码。想象一下，对使用第三方函数的代码块进行故障排除。这可能有助于您看到实现(假定它有源代码图)。

```
function myFn(emoji) {
  let msg = `${emoji} is hellav an emoji`;
  console.log(msg);
}

console.log(myFn.toString());

// "function myFn(emoji) {
//   let msg = `${emoji} is hellav an emoji`;
//   console.log(msg);
// }" 
```

Enter fullscreen mode Exit fullscreen mode

当然这并不适用于所有情况。如果我们对数组
上的`map`函数进行尝试

```
Array.prototype.map.toString();

// "function map() { [native code] }" 
```

Enter fullscreen mode Exit fullscreen mode

因为实现不是用 JavaScript 编写的，所以它只会向您展示这个函数是用本机代码编写的。

### `JSON.stringify`

团队已经对 Unicode 字符做了改进，现在这个方法不能返回格式错误的数据。

```
// Non-BMP characters still serialize to surrogate pairs.
JSON.stringify('𝌆')
// → '"𝌆"'
JSON.stringify('\uD834\uDF06')
// → '"𝌆"'

// Unpaired surrogate code units will serialize to escape sequences.
JSON.stringify('\uDF06\uD834')
// → '"\\udf06\\ud834"'
JSON.stringify('\uDEAD')
// → '"\\udead"' 
```

Enter fullscreen mode Exit fullscreen mode

### `Array.sort`稳定

团队已经决定改变排序实现，使其保持稳定(也就是说，比较相等的元素必须保持其原始顺序)。

```
const grades = [
  { topic: 'math', grade: 10 },
  { topic: 'literacy', grade: 10 },
  { topic: 'chemical', grade: 13 },
  { topic: 'geography', grade: 12 },
  { topic: 'modern history', grade: 12 },
  { topic: 'art', grade: 13 },
  { topic: 'computer basics', grade: 14 },
  { topic: 'algebra', grade: 14 },
];

grades.sort(a, b => a.grade - b.grade);

// 0: {topic: "math", grade: 10}
// 1: {topic: "literacy", grade: 10}
// 2: {topic: "geography", grade: 12}
// 3: {topic: "modern history", grade: 12}
// 4: {topic: "chemical", grade: 13}
// 5: {topic: "art", grade: 13}
// 6: {topic: "computer basics", grade: 14}
// 7: {topic: "algebra", grade: 14} 
```

Enter fullscreen mode Exit fullscreen mode

### 总结

总的来说，JavaScript 正朝着帮助开发人员编写更稳定、可靠和一致的代码的正确方向前进。你可以在这里找到更多关于 GitHub 回购的信息。

希望在 JavaScript 中看到更多令人敬畏的特性，并在下一篇文章中与您见面。