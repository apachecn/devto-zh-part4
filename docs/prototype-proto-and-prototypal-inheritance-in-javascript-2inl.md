# JavaScript 中的原型、__proto__ 和原型继承

> 原文：<https://dev.to/varundey/prototype-proto-and-prototypal-inheritance-in-javascript-2inl>

> 这篇文章最初发表在[我的网站](https://varundey.me/blog/js-prototypes/)

如果你花了一些时间在 JavaScript 上，你可能已经见过或者至少听说过原型。如果你不确定原型或者它是做什么的，这篇文章就是为你准备的。在这篇文章中，我将试图帮助你理解 JavaScript 原型的工作，并且在这个过程中，尝试解释什么是原型继承以及它是如何工作的。

在开始之前，我希望您已经意识到 JavaScript 中的一切都是高级对象。这意味着除了 null 和 undefined，JavaSscript 中的所有内容都是从`Object`派生的。

> 但是凡荣，这怎么可能呢？JavaScript 中有几种原语，如字符串、数字、布尔等。一个数据类型，比如说数字，如何从对象中派生出来？

## 原型和 __proto__

要回答这个问题，我们需要首先了解什么是原型。JavaScript 中的原型只不过是一个对象拥有的一组特殊属性(记住，JavaScript 中的几乎所有东西都是从`Object`派生出来的)。每个对象拥有自己的一组`prototype`属性。让我们看一个非常基本的例子来说明我的意思。打开您的浏览器开发工具，并在进行过程中尝试代码片段。

```
var fooFunc = function() {
    return {
        foo: 42
    }
};
fooFunc.prototype.bar = 'baz';
var fooVal = fooFunc();
console.log(fooVal);   // {foo: 42}
console.log(fooFunc.prototype);     // {bar: "baz", constructor: ƒ} 
```

第二个 print 语句向您展示了原型继承的所有优点。函数`fooFunc`是从`Object`实例中派生出来的，它有自己的一组*属性*，即`{bar: baz}`，以及它从`Object`实例化时所携带的任何东西，即`{constructor: ƒ}`。

> 所以如果`fooFunc`是从`Object`衍生出来的，我能上链看看`Object`的原型吗？

好问题，你绝对可以。然而，你需要记住的一件事是，除了 JavaScript `function`类型，对象的所有其他原型都驻留在它的`__proto__`属性中。让我们看看我的意思是什么。

```
console.log('prototype of fooFunc:');
console.log(fooFunc.prototype);     // {bar: "baz", constructor: ƒ}
console.log('prototype of Object:');
console.log(fooFunc.prototype.__proto__);   // {constructor: ƒ, __defineGetter__: ƒ, __defineSetter__: ƒ, hasOwnProperty: ƒ, __lookupGetter__: ƒ, …} 
```

你看到我看到的了吗？最后一个控制台语句返回一个对象，它有自己的一组*特殊属性*。这不过是`Object`的原型链。这证实了我们实际上可以遍历原型链，并且我们的函数`fooFunc`是从`Object`派生出来的。

> 那么当我试图遍历`Object`的原型链时会发生什么呢？

让我们看看会发生什么:

```
console.log(fooFunc.prototype); // {bar: "baz", constructor: ƒ}
console.log(fooFunc.prototype.__proto__);// {constructor: ƒ, __defineSetter__: ƒ, …}
console.log(fooFunc.prototype.__proto__.__proto__);     // null 
```

你看，JavaScript 中的`Object`是顶层构造。如果您试图查看`Object`的父代拥有什么属性，您将得到 null，因为没有`Object`的父代。

在这一点上，我希望你回到开头，把之前的一切都和我在帖子里说的联系起来。

> JavaScript 中的 Prototpyes 只不过是一组由对象持有的特殊属性。

## 原型遗传

既然您已经理解了原型是如何工作的，那么原型继承应该非常简单。我们来看下面这个例子:

```
var obj = function(){
    this.firstName = 'Varun';
    this.lastName = 'Dey'
}
obj.prototype.age = 25;
var nameObj = new obj()
console.log(nameObj.age);   // 25 
```

让我们来分析一下这里发生了什么:

*   首先，我们定义一个函数`obj`。
*   现在我们也在`obj`的原型链上直接分配另一个属性`age`。
*   我们从`obj`实例化一个名为`nameObj`的变量。`nameObj`是一个附加了两个属性的对象，即`firstName`和`lastName`。
*   当我向`newObj`请求它的`age`属性时，它首先进入自己的对象并试图找到它。它在`nameObj`物体中找到`age`了吗？
    *   不。所以它沿着链向上，也就是`nameObj.__proto__`并在那个对象中寻找一个`age`属性。
    *   它在这里找到了一个`age`属性，因为`nameObj.__proto__`与`obj.prototype`完全相同。

这就是 JavaScript 的原型继承的全部内容。每当您请求 JavaScript 获取一个键时，它首先会查看自己对象的属性。如果它没有找到任何东西，它就转到它的原型链(`obj.__proto__`)并试图在那些属性中找到那个键，如果它没有找到，它就转到它当前的原型链的上一级(`obj.__proto__.__proto__`)并做同样的事情。它不断重复同样的过程，直到到达`Object`的原型链，如果在那里也找不到，就从那里返回 undefined。

## 原型污染

这是 JavaScript 中一个有趣的继承案例，与其他基于类的语言如 Java/C++:

```
function parent(){
    return{
        foo: 42,
        bar: 'baz'
    }
}
child = new parent() 
```

仔细看，会发现`child`是`parent`的实例化对象。而`parent`最终不过是`Object`的一个实例化方法。这意味着`child`的原型和`parent`的原型就是`Object`的原型

```
child.__proto__ === parent.prototype.__proto__      // true 
```

现在我们再来看一个例子:

```
function parent(){
    return{
        foo: 42,
        bar: 'baz'
    }
}
parent.prototype.__proto__.baz = 'I should not belong here'
child = new parent()
console.log(child.__proto__) 
```

这里你可以看到一个污染的典型例子。我通过遍历函数的原型链，直接在`Object`的原型上创建了一个属性`baz`。现在这个`baz`将在`Object`的所有实例中共享，这就是为什么如果你看到控制台声明，你会发现除了其他`Object`属性，我们现在还有`baz: "I should not belong here"`。这是一种不好的做法，因为它破坏了封装，所以不被接受。

类似地，我也可以这样做，JavaScript 允许我这样做:

```
function parent(){
    return{
        foo: 42,
        bar: 'baz'
    }
}
delete parent.prototype.constructor
child = new parent() 
```

## 表现

不用说，当您遍历 prorototype 链时，查找时间会增加，因此性能会受到影响。当您试图在整个原型链中访问一个不存在的属性时，这就变得很关键。要检查您需要的属性是否在对象本身中定义，可以使用`hasOwnProperty`。

```
child.hasOwnProperty('foo');    // true
parent.hasOwnProperty('baz');   // false
Object.prototype.hasOwnProperty('baz'); // true 
```

## 完成圆圈

在最开始，我说过除了 null 和 undefined，一切都是`Object`实例化。让我们证明:

```
const foo = 42;
const bar = 'fooBar';
const baz = true;
foo.__proto__.__proto__ === bar.__proto__.__proto__;    // true
bar.__proto__.__proto__ === baz.__proto__.__proto__;    // true 
```

所以你明白我在说什么了。JavaScript 中几乎所有的东西都来自`Object`

## 结论

原型是 JavaScript 的基础。我希望我能够帮助你理解 JavaScript 中的原型是如何工作的。一旦你掌握了正确的窍门，你就可以扩展这些知识来理解`this`在 JavaScript 中是如何工作的。Mozilla 在这方面有很好的资源，我鼓励你也去浏览一下-[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Inheritance _ and _ the _ prototype _ chain](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)

我很想知道这篇文章是否能帮助你更好地理解 JavaScript。:)