# Javascript 中的迭代器、生成器和迭代协议

> 原文：<https://dev.to/varundey/iterators-generators-and-iteration-protocol-in-javascript-51hl>

> 这篇文章最初发表在[我的网站](https://varundey.me/blog/js-iteration/)

如果你写 JavaScript 已经有一段时间了，你可能很清楚哪些数据类型在 JavaScript 中是可迭代的。如果你不记得或者就是想不起来，那就是`String`、`Array`、`Map`、`Set`和`TypedArray`。

> 但是 Varun，除了`String`这里的一切不都是`Object`的实现吗？

## 可迭代协议

你这样想是绝对正确的。毕竟 JavaScript 中的大多数数据类型都是从`Object` 中派生出来的。那么是什么使得`Array`、`Map`、`Set`、`TypedArray`可迭代而`Object`不可迭代呢？让我们打开控制台找出答案。

```
Array.prototype[Symbol.iterator]
Map.prototype[Symbol.iterator]
Set.prototype[Symbol.iterator]
Int16Array.prototype[Symbol.iterator]
Object.prototype[Symbol.iterator] 
```

你可能已经注意到，除了最后一条语句，每一行都返回一个函数。所有剩余的对象类型在它们的原型链上有一个名为`Symbol.iterator`的属性。因为这个属性在`Object`中不可用，所以它返回`undefined`。因此，对于一个可迭代的对象，它必须实现可迭代协议，这意味着给定的对象必须在其原型链上有一个`Symbol.iterator`。`Symbol.iterator`是一个不带参数并返回一个`Object`的函数。这个返回的`Object`应该遵循迭代器协议的约定。

## 迭代器协议

迭代器协议声明，对于迭代器对象，有一种标准的方式来返回值。如果从`Symbol.prototype`返回的对象有一个返回以下两个属性的方法`next`,则称该对象遵守迭代器协议:

*   done [boolean]一个布尔值，表示迭代序列是否已完成
*   值迭代时返回的任何值。当`done`为`true`时可以选择

让我们来证明一下到目前为止我们学到了什么

```
const map = new Map()
mapIterator = map[Symbol.iterator]()
mapIterator.next          // function next() 
```

这意味着`Map`实现了

*   可迭代协议
    *   因为它的 __proto__ 链中有`Symbol.iterator`。
*   迭代器协议
    *   因为 iterable 协议返回一个包含方法`next`的`Object`。

## 迭代协议在起作用

让我们用一些实际的数据类型来测试我们的理论

```
const string = "Hello"
const stringIterator = string[Symbol.iterator]()
stringIterator.next()       // Object { value: "H", done: false }
stringIterator.next()       // Object { value: "e", done: false }
stringIterator.next()       // Object { value: "l", done: false }
stringIterator.next()       // Object { value: "l", done: false }
stringIterator.next()       // Object { value: "o", done: false }
stringIterator.next()       // Object { value: undefined, done: true } 
```

我们刚刚证明了`String`同时实现了 iterable 和 iterator 协议。许多构造(用于..扩散、破坏、屈服等。)在幕后实现迭代协议。您可以对其他数据类型进行同样的尝试，结果将是相似的。

```
const map = new Map()
map.set('a', 1)
map.set('b', 2)
const mapIterator = map[Symbol.iterator]()
[...mapIterator] 
```

## 自定义迭代协议

> 所以基本上我的对象应该有一个属性`Symbol.iterator`，它是一个方法，应该返回给我一个`Object`，它应该有一个`next`方法，调用它应该给我一个`done`和`value`属性？这应该不难创造。

事实证明，事实并非如此。😄

```
const customIteratationProtocol = (start, end) => ({
    [Symbol.iterator]: () => {
        let startIndex = start;
        return {
            next: () => {
                if(startIndex !== end){
                    return {
                        value: startIndex += 1,
                        done: false
                    }
                }
                return {
                    done: true
                }
            }
        }
    }
});

const customIteratationProtocolInstance = customIteratationProtocol(1, 3);
const customIterationProtocolObj = customIteratationProtocolInstance[Symbol.iterator]()
customIteratationProtocolInstance.next();  // Object { value: 2, done: false }
customIteratationProtocolInstance.next();  // Object { value: 3, done: false }
customIteratationProtocolInstance.next();  // Object { done: true } 
```

您也可以实现 iterable 协议或 iterator 协议，但这通常是不可取的，因为如果这样的对象被需要 iterable 的构造使用，它可能会引发运行时错误。实现了可迭代协议但没有实现迭代器协议的对象称为非良构可迭代对象。

## 发电机

JavaScript 中的生成器是一种特殊的函数，它的执行不是连续的。它们允许你在函数构造中创建一个内部状态。这个函数的值只有在遇到一个`yield`关键字时才被返回。生成器由`function*`语法定义。生成器函数可以实例化 n 次，但是每个实例化的对象只能迭代一次生成器。但是，您不能使用带有箭头功能的发生器。

```
function* myGenerator(n) {
    let index = n;
    while(true) {
        yield index += 1;
    }
}
const myGeneratorObj = myGenerator(2);
myGeneratorObj.next().value;      // 3
myGeneratorObj.next().value;      // 4
myGeneratorObj.next().value;      // 5 
```

## 发电机真的有用吗？😕

尽管迭代器是 JavaScript 引擎的一个伟大概念，但我个人从未在 JavaScript 中使用过生成器。同样，在 JavaScript 这样的原型语言中，我真的不理解 ES6 生成器试图解决的用例。在我看来，生成器给语言带来了很多复杂性，原因如下:

1.  它创建一个构造函数
2.  然后，它在该构造函数下创建一个方法
3.  该值最终位于该方法调用的对象内部

这造成了性能开销，并引入了许多无用的东西。我认为我们可以通过引入一个简单的函数工厂来废除生成器。上面的例子可以改写为

```
const myGenerator = n => {
    let index = n;
    return () => index += 1;
}
const gen = myGenerator(2);
gen();      // 3
gen();      // 4
gen();      // 5 
```

## 结论

JavaScript 有很多功能。迭代只是其中之一。如果你想学习更多关于迭代器和生成器的知识，我推荐你去浏览官方的 MDN 文档。我很想听听你对这篇文章的看法。此外，如果有一个特定的用例是 generator 为您解决的，我也很乐意听到。编码快乐！😁