# 通过记忆提高功能的执行速度

> 原文：<https://dev.to/aminnairi/increase-the-speed-execution-of-your-functions-with-memoization-2mg8>

今天，我将尝试解释什么是记忆化，以及什么是用例场景的示例。请记住，我在任何方面都不是专家，我只是一个狂热爱好者，就像你们中的一些人一样。我只是碰巧遇到了这个，我想分享一下我对这个问题的看法。如果我在这篇文章中有任何错误，请在下面的评论区告诉我。我们都可以从他人的正确中获益！

## 像散列表

记忆化是减少函数执行时间的一种方式。它通过存储每次调用函数的结果来实现这一点。就像哈希表一样。一旦您尝试使用与以前相同的参数调用函数，而不是进入整个计算，它将只返回缓存的结果。这当然有助于减少函数返回预期结果所需的时间。

例如，假设我们有一个名为`add`的函数。需要两个参数相加，所以函数的定义是

```
"use strict";

function add(number1, number2) {
    return number1 + number2;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以使用我们的函数并添加一些数字。

```
add(1, 2); // 3
add(3, 4); // 7
add(1, 2); // 3 
```

Enter fullscreen mode Exit fullscreen mode

## 参照透明

这里有两件事需要注意。第一个是我们的函数是纯函数。为了理解记忆是如何工作的，这是一个重要的概念。纯函数是一个没有副作用的函数，并且总是为相同的参数返回相同的结果，也称为引用透明性。

一个副作用会使我们的函数变得不精确，使它的结果不可预测，从而取消了它的引用透明性。引用透明性是这样一个事实:对于相同的参数总是返回相同的结果的函数，在代码中的任何地方都可以被它的结果替换。这意味着这两段代码是相等的。

```
console.log(add(1, 2) === 3);
console.log(add(3, 4) === 7);
console.log(add(1, 2) === 3); 
```

Enter fullscreen mode Exit fullscreen mode

```
console.log(3 === 3);
console.log(7 === 7);
console.log(3 === 3); 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经确定对于给定的一组参数，我们总是得到相同的结果，我们可以完全重写我们的函数来摆脱昂贵的加法过程，并使用 dictionnary(或 JavaScript 中的对象)来返回结果并加速我们的函数。

```
"use strict";

function add(...parameters) {
    const cache = {
        [[1, 2]]: 3,
        [[3, 4]]: 7,
        [[5, 6]]: 11
    }; 

    return cache[parameters];
}

console.log(add(1, 2) === 3);  // true
console.log(add(3, 4) === 7);  // true
console.log(add(5, 6) === 11); // true 
```

Enter fullscreen mode Exit fullscreen mode

## 通过缓存获得很多

但是当我们试图将两个没有缓存的数字相加时，我们必须自己计算。把这些缓存的数字想象成一些来自统计研究的数字，显示了增加最多的数字。此外，我们可以通过缓存最常用的数字，并自己计算其余的数字来获得很多。

```
"use strict";

function add(...parameters) {
    const cache = {
        [[1, 2]]: 3,
        [[3, 4]]: 7,
        [[5, 6]]: 11
    }; 

    if (parameters in cache) {
        return cache[parameters];
    }

    return parameters[0] + parameters[1];
}

console.log(add(1, 2) === 3);   // true (cached)
console.log(add(3, 4) === 7);   // true (cached)
console.log(add(5, 6) === 11);  // true (cached)
console.log(add(7, 8) === 15);  // true (computed) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，缓存的数字是我们预期的参数。其余的像往常一样计算。但是这并不方便。事实上，最常用的数字也在不断变化，一开始就有一个大的缓存确实效率不高。最棒的是在使用我们的函数之后填充我们的缓存。就像保存缓存的某种全局变量。这就是记忆化的意义所在。

让我们使用一些更高级的用例场景。比如斐波那契数列。如果你对数学不放心，不要担心，因为我们是两个人！但这是一个很好的例子，向您展示了如何从记忆函数中获益。我认为斐波纳契数列是一棵呈指数增长的家谱。

下面是这个函数的递归定义。

```
"use strict";

function fibonacci(number) {
    if (number === 1) {
        return 1;
    }

    if (number < 1) {
        return 0;
    }

    return fibonacci(number - 1) + fibonacci(number - 2);
} 
```

Enter fullscreen mode Exit fullscreen mode

这意味着每次我们计算斐波纳契数列的`N-1`和`N-2`并把它们加在一起。停止条件是当我们到达我们已知的`0` & `1`序列的`0th`和`1st`号时。但是由于它是一个递归函数，并且考虑到斐波纳契数列的计算方式，它可能会以相同的参数被调用多次。让我们试着计算序列中第`40`个数字所需的时间。

```
const start = new Date().getTime();

fibonacci(40);

const stop = new Date().getTime();

console.log(`Fibonacci(40) executed in ${stop - start}ms.`);
// Fibonacci(40) executed in 1966ms. 
```

Enter fullscreen mode Exit fullscreen mode

## 令人难以相信

现在让我们尝试使用记忆化来计算它(我将在一分钟内解释实现的细节)。

```
let start = new Date().getTime();

console.log(fibonacci(40));
// 102334155

let stop = new Date().getTime();

console.log(`fibonacci(40) executed in ${stop - start}ms.`);
// Fibonacci(40) executed in 1966ms.

start = new Date().getTime();

console.log(memoizedFibonacci(1250));
// 7.674768958056894e+260

stop = new Date().getTime();

console.log(`memoizedFibonacci(1250) executed in ${stop - start}ms.`);
// memoizedFibonacci(1250) executed in 1ms. 
```

Enter fullscreen mode Exit fullscreen mode

这里是`memoizedFibonacci`函数的实现。

```
const memoizedFibonacci = (function() {
    const cache = {};

    return function $fibonacci(number) {
        if (number === 1) {
            return 1;
        }

        if (number < 1) {
            return 0;
        }

        if (number in cache) {
            return cache[number];
        }

        const result = $fibonacci(number - 1) + $fibonacci(number - 2);

        cache[number] = result;

        return result;
    };
})(); 
```

Enter fullscreen mode Exit fullscreen mode

我承认:这很难相信。所以我建议你自己测试一下，因为练习是最好的学习方法。请注意，如果您在 Repl It 这样的在线平台上进行测试，请尝试为未优化的 fibonacci 版本使用较小的值，因为在他们的服务器上进行计算可能需要更长的时间。

我自己也怀疑过一会儿(我没有使用任何日志记录，所以在写这篇文章的时候添加了它)。但是这里没有任何问题，因为我得到了这个巨大的数字。事实上，在有一个`Infinity`值之前，我不能在我的计算机上超过这个值。由于我不确定 Node.js 给我的是`Infinity`，因为它不能计算这个数，还是因为我的函数有问题，所以我搜索了最有意义和更高的值来演示。

但它不仅比我们写的函数的原始定义，快得多，我们还用了一个更高的值。这都要归功于一个闭包和一个对象。就这么简单！

如果您不熟悉 closure，可以把它看作是一种全局保存值的方法，同时保持这个值只对我们的函数可用(这意味着外界不会修改这个值，这样我们可以确保我们的缓存不会被代码中的其他模块毒害)。

此外，我使用 IIFE(立即调用的函数表达式)将缓存保持在闭包的范围内。出于与上述相同的目的。如果你不熟悉这些术语，不要老是用脑袋去碰它们，只要快速搜索以了解更多就行了。

但是在这种情况下，记忆化更强大的地方在于，既然我们已经成功地计算了序列的第 n 个数字的值，我们就根本不需要计算它，因为它已经被缓存了。接下来用参数`1250`调用我们的`memoizedFibonacci`函数将只需要一次比较和一次对象访问。仅此而已。

## 结论

总而言之，我认为记忆化是函数式编程这个更大计划的一部分。引用透明性使我们能够拥有可靠的缓存，从而使用内存化来加速对相同参数的后续调用。这不是一个神奇的方法，因为它要求我们至少计算一次给定参数集的值。但是在可重用性和因式分解的世界里，它真的很有用，在那里我们不必对一个值计算一次以上。