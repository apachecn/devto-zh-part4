# 什么是 __proto__？JavaScript

> 原文：<https://dev.to/inambe/what-is-proto-javascript-191m>

您可能会在每次将`object`登录到开发人员控制台时注意到这个`__proto__`属性。

[![proto](img/73d816929cae532f64bfaeef0ff18b30.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--BawsfHvn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8qwwzv6jwrgi3mys4g2b.png)

在这篇文章中，我将试着解释这个`__proto__`从何而来，它包含了什么，以及两者之间的任何事情。所以，我们来搞定这个`__proto__`。

首先，让我们理解 JavaScript `object`是如何构造的。一旦你理解了一个`object`是如何被构造的，`__proto__`属性将会比其他方式更有意义。

## 一个`object`是如何构造出来的？

JavaScript `object`总是由构造函数构造/生成。现在，你可能会说“但是，对象文字？它们不是构造函数。

好吧，对象文字只是语法糖。您可以在不使用对象文字的情况下编写对象。参见下面的例子，两种语法会产生相同的输出。第二种方法给了我们比 object literal 更多的权力(我们在这里不讨论)。

```
// an object using object literal
const person = {
  name: "John",
  age: 30
};

// an object using Object constructor function
const person = new Object();
Object.defineProperties(person, {
  name: {
    value: "John"
  },
  age: {
    value: 30
  }
}); 
```

Enter fullscreen mode Exit fullscreen mode

既然我们知道 JavaScript 中的每个`object`都是由构造函数构造的，那么让我们回到最初的问题。

## 什么是`__proto__`？

`__proto__`是一个属性，在每个 JavaScript `object`中，它指向`object`的构造函数的`prototype`属性。我知道这有点难以理解，但让我为你简化一下。

每个构造函数都有这个`prototype`属性。下图为我们展示了`Object`构造函数的`prototype`属性。

[![prototype](img/39ce37ee1bca98c38b7b45297d4ed3e9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--juM_vfFH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kvmju7afrsqloq7fftoc.png)

所以，我们从`Object`构造函数构造/产生的每个`object`，都有`__proto__`属性指向`Object`构造函数的`prototype`属性。

## 举例

当我们使用对象文字时，正如我们在下面的例子中所做的，`Object`构造函数用于构造/产生对象。现在我们有了一个从`Object`构造函数构造/产生的`person`对象，我们可以验证来自`person`对象的`__proto__`和来自`Object`构造函数的`prototype`是相同的。

```
// an object using object literal
const person = {
  name: "John",
  age: 30
};
// verify
console.log(person.__proto__ === Object.prototype); // true 
```

Enter fullscreen mode Exit fullscreen mode

如果你不明白帖子里的内容，或者我错过了什么，请告诉我。

这是我的第一篇博文，我欢迎批评/建议。