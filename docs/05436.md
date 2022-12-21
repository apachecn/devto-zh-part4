# JavaScript:点点点点示例

> 原文：<https://dev.to/thomas_graf/javascript-dot-dot-dot-by-example-21gb>

你知道 JavaScript 中三个点的所有用例吗？

小历史:

首先，他们为 ES6 中的数组和参数添加了这个语法(spread 和 rest 元素)。
2018 年，他们为对象添加了它(传播和休息属性)。

并且所有特性都共享相同的语法

`...`

如果您第一次看到这种语法，可能会感到非常困惑。这篇文章收集了如何在你的代码中使用它们的例子。

## 先说:扩散元素

想要将一个数组中的所有元素复制到另一个数组中吗？
用`...`(扩散元素)来浅(！)复制一个数组。

```
const fruits = ["apple", "banana", "coconut"];
const fruitsCopied = [...fruits];
// fruitsCopied: ["apple", "banana", "coconut"] 
```

你可以随时添加另一个元素。

```
const fruits = ["apple", "banana", "coconut"];
const moreFruits = [...fruits, "pear"];
// moreFruits: ["apple", "banana", "coconut", "pear"] 
```

假设我们有这个愚蠢的`print`函数。
使用`...`，我们可以简单地将数组元素作为函数的参数传播。

```
const print = (first, second, third) => console.log(`first: "${first}" second: "${second}" third: "${third}"`);

const fruits = ["apple", "banana", "coconut"];
print(...fruits);
// first: "apple" second: "banana" third: "coconut" 
```

我们还可以将一个数字数组扩展到 Math.max 方法中。这不是很棒吗？

```
const numbers = [1, 5, 23, -5, 2, 42];
Math.max(...numbers); 
```

## 下一个:剩余元素

想把一个数组拆分成头和尾？
这里我们正在析构一个数组，并使用`...` (rest 元素)将所有没有被析构模式选取的元素收集到`tail`中。

```
const [head, ...tail] = [1, 2, 3];
// head: 1
// tail: [2, 3] 
```

之前，我们已经使用`...`将元素作为参数传播给调用点上的函数。现在我们使用`...` (rest 参数)将所有传递的参数收集到`args`中。

```
function getNumberOfPassedArguments(...args) {
    return args.length;
}

getNumberOfPassedArguments("apple", "banana", "coconut");
// 3 
```

你知道`compose`是 Ramda 还是下划线？看看这个实现有多短:

```
const compose = (...fns) => x => fns.reduceRight((acc, cur) => cur(acc), x);

const fivePercentDiscount = price => price * 0.95;
const germanTax = price => price * 1.19;

const getProductPrice = compose(germanTax, fivePercentDiscount);
getProductPrice(10); 
```

## 我们来说对象。这里我们可以做几乎相同的事情:传播属性

使用`...`(扩展属性)，我们可以浅层复制一个对象的所有(自己的可枚举)属性(键和值)。

```
const person = {name: "Thomas", age: 28};
const copiedPerson = {...person}; 
```

我们可以扩展属性，并在创建过程中立即覆盖属性。

```
const thomas = {name: "Thomas", age: 28};
const peter = {...thomas, name: "Peter"}; 
```

如果我们已经定义了一些属性，那么我们可以覆盖任意数量的属性，然后扩展另一个对象的属性。

```
function initConfig(overwrites) {
    const defaults = {useSSL: true /*,  ... */};
    return {...defaults, ...overwrites};
} 
```

想要在对象上有可选属性，但不想在创建后改变对象？
以下是要途径:

```
const shouldAddB = true;
let obj1 = {a:1, ...(shouldAddB && {b:2})};
// or
let obj2 = {a:1, ...(shouldAddB ? {b:2} : {})}; 
```

## 休息属性

我们还可以使用`...` (rest 属性)将属性的子集复制到对象中。在析构模式中，我们列出了不希望在新对象中出现的属性，并将剩余的属性和`...` rest 属性收集到`personWithoutAge`中。

```
 const {age, ...personWithoutAge} = {name: "Thomas", age: 28, country: "Germany"};
// personWithoutAge: {name: "Thomas", country: "Germany"} 
```

你在代码中使用哪一个，你有更多的用例吗？请告诉我们！