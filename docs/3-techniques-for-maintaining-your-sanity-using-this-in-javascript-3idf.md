# 在 JavaScript 中使用“This”保持理智的 3 个技巧

> 原文：<https://dev.to/canderson93/3-techniques-for-maintaining-your-sanity-using-this-in-javascript-3idf>

在 JavaScript 的许多令人困惑的方面中，关键字`this`可能是最复杂的一个——这里有一个关于这个麻烦的关键字的笑话:

> ![Ben Halpern profile image](img/3814cc9cbc453345725bf2b5e578e8a0.png)本·哈尔彭@本·哈尔彭![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)有时候当我在写 Javascript 的时候，我真想举起双手说“这简直是扯淡！”但是我怎么也想不起来“这个”指的是什么2015 年 3 月 20 日 14 点 27 分[![Twitter reply action](img/5d5a32424597af8488f7190c7d7d496b.png)](https://twitter.com/intent/tweet?in_reply_to=578925947245633536)[![Twitter retweet action](img/3d12d4a909b79beaf8d81b6491fd052d.png)](https://twitter.com/intent/retweet?tweet_id=578925947245633536)[![Twitter like action](img/3f89df2f36e8e5624d2a25952b3ac8b8.png)](https://twitter.com/intent/like?tweet_id=578925947245633536)

`this`是*令人沮丧。*每次你认为你有了它，另一个奇怪的情况出现了——它应该是*简单的，*那么为什么它似乎从来没有按照你想要的方式工作？

# 为什么“这个”令人困惑

在其他编程语言中，`this` *总是*指的是一个对象的当前实例。这是一个非常一致的关键字，它只包含两个值:当前对象，或者什么都不包含。

在 JavaScript 中，`this`指的是所谓的*执行上下文*。在实际环境中，这是*与其他语言版本的`this`看似相似的*，但是包含一个基本的区别:基于*如何调用*函数，执行环境是不同的。_

这意味着 JavaScript 版本的`this`可能有不同的值，这取决于您调用函数的方式。

```
class Foo {
    text = "string";

    trigger() {
        // Because of how the function is being called, `this` can have
        // several different values
        this.text = this.text + "a";
    }

    brokenTrigger() {
        // `this` will refer to the current object, so it will act as we expect
        this.trigger();

        // setTimeout resets `this` to the global context object - in web
        // browsers, it is the Window object
        setTimeout(this.trigger, 500);

        // When we refer to the function directly (without the object)
        // `this` refers to the global context object (window)
        const unboundFunction = this.trigger;
        unboundFunction();

        // Event listeners replace "this" with the target element 
        // `this` will refer to the clicked ".triggerButton"
        let button = document.querySelector(".triggerButton");
        button.addEventListener('click', this.trigger);
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 如何安全使用`this`

当你看到所有可能出错的方式时，似乎最简单的选择就是抛下双手，成为一个隐士，开一个小土豆农场。

实际上，`this`的问题远没有这些例子看起来的那么严重。通过限制对对象函数使用`this`，可以很容易地避免`this`的大多数奇怪行为，因为在对象函数中使用`this`是最一致的

正如我在介绍中所说的，对一个对象使用`this`几乎总是*引用对象实例，但是你必须注意两个主要的例外:*

1.  `setTimeout`
2.  `addEventListener`

在这些情况下，我们有几种技术来控制`this`的值，并确保它按照我们想要的方式工作。

## 技巧 1:使用粗箭头功能

胖箭头函数，除了是一种快速声明函数的方式，与其他函数声明略有不同，它们不允许任何东西覆盖`this`。相反，它保留声明函数的位置(其词法范围)的值。

这意味着我们可以将它们用作包装器，或者直接作为事件监听器函数调用来保存我们的`this`引用。

```
class Foo {
    listen() {
        // `this` still refers to Foo
        document.querySelector('.class').addEventListener('click', (e) => {
            this.handler(e); 
            // or
            this.val = 1;
        });
    }

    handler(e) {
        this.val = 1;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 技巧 2:将`this`赋给一个变量

在 ES6 之前，一种流行的模式是在我们知道它引用我们的对象时复制`this`的值，并使用新的变量来代替。

```
var foo = {
    listen: function() {
        // These are both common names for our new `this`
        var that = this;
        var self = this;

        document.querySelector('.class').addEventListener('click', function() {
            self.val = 1;
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 手法三:用`Function.bind`显式设置`this`

函数附带了几个工具来显式设置`this`的值，这样就可以保证`this`的值。

*   `Function.bind`
*   `Function.apply`
*   `Function.call`

实际上，`Function.bind`是三个函数中最有用的，因为它不立即调用函数，而是返回一个新版本，带有一个预置的`this`和你传递的任何参数——你可以在`setTimeout`或`addEventListener`函数中直接使用这个新函数，并保留你的`this`值。

```
class Foo {
    listen() {
        // The first paramter of `bind` is the new `this` value
        document.querySelector('.class').addEventListener('click', this.handleEvent.bind(this));
    }

    handleEvent() {
        this.val = 1;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 加成手法:使用严格模式

JavaScript 的严格模式稍微改变了`this`的行为。它没有将`this`隐式设置为对象之外的全局上下文，而是将其设置为`undefined`。

实际上，这是一个相当小的变化，但是它防止了几个不正确的`this`用法，并导致一个潜在的 bug 抛出一个错误:

```
'use strict';
let obj = {
    update(val) {
        // Normally this will create an `x` property on the global object and
        // continue running, but in strict mode this will throw an error
        this.x = val;
    }
}

// Breaking the reference to `obj` causes `this` to be undefined instead
// of referring to the global object
let func = obj.update;
func(); 
```

Enter fullscreen mode Exit fullscreen mode

## 不要过度复杂化

如果你经常阅读我的博客，你会知道这基本上是我的口号。

不可否认的是，`this`是*奇怪，*但这并不意味着你需要担心它所呈现的所有边缘情况——通常情况下，它们不会出现。

我从事 Web 开发已经八年了，在准备这篇文章的时候，我了解到了一些以前从未遇到过的关于`this`的新的边缘案例。

如果你有兴趣学习更多关于 JavaScript 的知识，你可以查看一下[这个闭包指南](https://dev.to/canderson93/javascript-closures-and-how-they-work-51bk)，或者阅读这篇关于[开始学习前端框架](https://dev.to/canderson93/when-to-start-learning-a-front-end-framework-9hk)你需要知道什么的文章。

* * *

[学习 JavaScript 很困难？注册我的时事通讯，这样的文章会直接发到你的收件箱](https://pages.convertkit.com/9f030a68c9/cd303b3d7b)。