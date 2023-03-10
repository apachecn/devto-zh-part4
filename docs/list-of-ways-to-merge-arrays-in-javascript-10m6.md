# javascript 中合并数组的方法列表！

> 原文：<https://dev.to/varit05/list-of-ways-to-merge-arrays-in-javascript-10m6>

嗯，想象一下面试官问你用 JavaScript 合并两个数组的方法？有各种方法可以实现它。所以在这篇文章中，我们将介绍一些合并数组的方法。

让我们看看一些方法！

```
 const fruits1 = ["🍏", "🍎", "🍐"];

const fruits2 = ["🍊", "🍋", "🍌"];

/**
 * 1\. Using concat method
 */

const concatFruits = fruits1.concat(fruits2);
console.log("concat:",concatFruits);

/**
 * Output
 * concat: ["🍏", "🍎", "🍐", "🍊", "🍋", "🍌"]
 */

/**
 * 2\. Using ES6 spread syntax
 */

const spreadFruits = [...fruits1, ...fruits2];
console.log("spread:",spreadFruits);

/**
 * Output
 * spread: ["🍏", "🍎", "🍐", "🍊", "🍋", "🍌"]
 */

/**
 * 3\. Using Push method
 * This method will mutate original array [fruits3]
 */

const fruits3 = ["🍏", "🍎", "🍐"];

const fruits4 = ["🍊", "🍋", "🍌"];

const pushFruits = fruits3.push(...fruits4);
console.log("push:",fruits3);

/**
 * Output
 * push: ["🍏", "🍎", "🍐", "🍊", "🍋", "🍌"]
 */

/**
 * 4\. Using unshift method
 * This method will mutate original array [fruits6]
 */

const fruits5 = ["🍏", "🍎", "🍐"];

const fruits6 = ["🍊", "🍋", "🍌"];

fruits6.unshift(...fruits5);
console.log("unshift:",fruits6);

/**
 * Output
 * push: ["🍏", "🍎", "🍐", "🍊", "🍋", "🍌"]
 */

/**
 * 5\. Using splice method
 * This method will mutate original array [fruits7]
 */

const fruits7 = ["🍏", "🍎", "🍐"];

const fruits8 = ["🍊", "🍋", "🍌"];

fruits7.splice(3, 0, ...fruits8);
console.log("splice:",fruits7);

/**
 * Output
 * push: ["🍏", "🍎", "🍐", "🍊", "🍋", "🍌"]
 */ 
```

[玩 jsfiddle](https://jsfiddle.net/varit05/96hn0k1d/)

基于上述实现，推荐且方便的方法是
-选项 1: `Array.prototype.concat()`方法或
-选项 2: `Spread syntax`

如果你知道任何其他的方法，请随意评论。

参考资料:

[MDN - concat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)

[MDN - Spread](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)