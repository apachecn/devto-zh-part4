# 如何在 JavaScript 中使用其他对象的方法

> 原文：<https://dev.to/uriel_hedz/how-to-use-the-methods-from-other-objects-in-javascript-3a10>

我最近在 JavaScript 中遇到了这几行代码:

```
[].slice.apply(arguments)
[].slice.call(document.querySelectorAll('foo')) 
```

虽然难以阅读，但这几行代码非常有用，因为它们允许您将特定的方法数组化到不一定是数组的集合中，例如 NodeLists 和 Arguments 对象。

但是这些代码行在做什么，它们能告诉我们 JavaScript 的灵活性是什么？。

# 该如何如何

根据 W3Schools，slice 方法允许您从一个数组中选择元素并将其复制到一个新数组中。所以在阅读这行代码时，我首先想到的是，为什么我们在一个空数组中调用 slice？如果这个方法的目的是从一个数组中选择和复制元素，如果我们用一个没有元素的数组调用它，它又有什么用呢？

事情是这样的，这个可怜的空数组只是暴露了它宝贵的 slice 方法被拿走了。JavaScript 允许您通过使用 apply 和 call 方法来更改调用方法的上下文。换句话说，您可以通过调用和应用在方法体中设置`this`值。

请记住，每个 JavaScript 函数实际上都是一个对象，一个明显的例子是，每个 JavaScript 函数都有一个 call 和一个 apply 方法，以及其他一些可以通过函数定义调用的方法。

实际的话，对于每一个 JavaScript 函数，都可以调用 apply、call 等其他方法，比如:

```
function foo(){}
foo.apply();
foo.call(); 
```

所以这一行代码`[].slice.apply(arguments)`所做的是将上下文从空数组更改为 arguments 对象，使调用`slice`的对象看起来是`arguments`而不是空数组本身。

之所以这样做，是因为 arguments 对象有一些类似于数组的属性，比如 length，而这些相似性使得 slice 方法可以在上下文中使用 arguments 和 array。

在第一个参数之后，apply 方法允许您发送将作为参数传递给函数调用的参数，在这种情况下，当调用 slice 方法时，因为在这种情况下，我们只传递一个参数，arguments 对象，slice 方法不接收任何参数，这与文档一致，意味着所有数组元素将被复制到新数组中，这在 resume 中意味着这行代码将所有元素从 arguments 对象复制到新数组中。

现在，谈到可读性，这个方法和`Array.from` ES6 方法做的完全一样。

```
Array.from(arguments) === [].slice.apply(arguments) 
```

# 这个为什么

这一行特定的代码`[].slice.apply(arguments)`允许我们轻松地转换一个看起来和可能行为像数组但不是数组的对象，除了 arguments 对象之外，另一个常见的例子是 NodeList 对象，比如从`querySelectorAll`函数返回的那些对象。

这非常有用，因为它允许我们将集合作为一个数组来处理，然后我们可以将结果插入到一个循环中，与其他数组方法(如 concat、includes 等)结合使用。

# 结论

这一行代码让我在阅读后思考的是，JavaScript 如何允许我们从在其原型中不拥有这些方法的对象中调用方法，我认为这符合 Ruby 等语言如此流行的 duck typing 哲学。

因此，只要一个对象可以为一个方法提供属性和方法，您就可以将这个对象设置为该方法的上下文，以便有效地从一个没有该对象的对象中调用一个方法。

那么，你认为 JavaScript 在这些场景中的工作方式如何？请在评论中告诉我。

如果你能向我报告语法错误或打字错误，我也会很感激，因为英语不是我的母语。提前感谢！