# 用析构和映射选择多个 DOM 元素

> 原文：<https://dev.to/pablohoc/selecting-multiple-dom-elements-with-destructuring-map-310h>

如果你正在使用 vainilla JS 并操作 DOM 元素，我敢肯定你很早就会发现自己有这样的代码:

```
const sendButton = document.getElementById('send-btn');
const discountPopUp = document.getElementById('discount-popup');
const messageInput = document.getElementById('message');
const calculateButton = document.getElementBy('calculate-btn');
// dozens of elements more 
```

请不要把这个例子看得太严重。我不是说这是好的代码，甚至这些都是好的命名常量和 id。我只是想展示这段代码可以使用一些令人敬畏的 ES6 特性来简化:`map`和析构。我们可以这样做:

```
const [sendButton, discountPopUp, messaInput, calculateButton] = ['send-btn', 'discount-popup', 'message', 'calculate-btn'].map(id => document.getElementById(id)); 
```

为什么会这样？我们一步一步来。

首先，`map`是一个将函数作为回调的`Array`方法。然后，对于*数组的每个*元素，执行回调，将元素作为回调的参数传递。该回调的返回被添加到一个新数组中。所以，顾名思义，`map`T5 将一个数组的元素映射到一个新的数组，*通过一个函数传递*每个元素。

例如，

```
const numbers = [1, 2, 3, 4];
const doubles = numbers.map(number => number * 2);
console.log(doubles); // [2, 4, 6, 8]; 
```

`map`这里是*将*每个数字映射到它的 double，并存储在新数组`doubles`中。

回调被写成一个*箭头函数*。如果唯一要做的事情是返回某个值，那么 arrow 函数不需要声明保留字`return`。如果这还不太清楚，我们可以将箭头函数重写为:

```
const doubles = numbers.map((number) => { return number * 2}); 
```

但是你可以看到，第一个更简洁。甚至，我们不需要常量`numbers`，我们可以直接在数组上使用 map:

```
const doubles = [1, 2, 3, 4].map(number => number * 2); 
```

太棒了，对吧？，所以，回到我们的例子，我们有:

```
const elements = ['send-btn', 'discount-popup', 'message', 'calculate-btn'].map(id => document.getElementById(id)); 
```

这里我们有一个带有 id 的数组， *map* 将这些字符串中的每一个都映射到一个 HTML 元素，因为回调函数接受一个 id 并返回一个带有该 id 的 HTML 元素。这些元素存储在一个新的数组`elements`中。

但这并没有多大帮助。如果现在我们想使用 id 为*‘message’*的元素，我们需要像下面这样检索它:

```
elements[2] 
```

这个就不多说了。想象一下 100 行之后使用它。`elements[2]`到底是什么？

当然，我们可以避免把它存储在一个变量中，但是这又把我们带回了起点。

```
const elements = ['send-btn', 'discount-popup', 'message', 'calculate-btn'].map(id => document.getElementById(id));

const sendButton = elements[0]
const discountPopUp = elements[1];
const messageInput = elements[2];
const calculateButton = elements[3];
// dozens of elements more 
```

这不太实际。相反，我们可以*析构*返回的数组。您可以将数组析构为取其每个元素并将其存储在一个变量中。

例如:

```
let [a, b, c] = [0, 1, 2];
console.log(a); // 0
console.log(b); // 1
console.log(c); // 2 
```

右边数组的每个元素按顺序存储在各自的变量中。右边数组的第一个元素存储在左边的第一个变量中，依此类推。

所以，知道了这一点，我们终于可以结束我们开始的地方了。

```
const [sendButton, discountPopUp, messaInput, calculateButton] = ['send-btn', 'discount-popup', 'message', 'calculate-btn'].map(id => document.getElementById(id)); 
```