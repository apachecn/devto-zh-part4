# 用闭包封装和调节

> 原文：<https://dev.to/silvestricodes/encapsulate-and-moderate-with-closures-3734>

您是否曾经发现自己想要在应用程序的整个生命周期中修改和更新数据结构？就拿下面这段代码来说:

```
const obj = {}

const modifyObj = (key, value) => {
  obj[key] = value
  return obj
}
modifyObj('foo', 'bar') // Returns { foo: 'bar'}
modifyObj('biz', 'baz') // Returns { foo: 'bar', biz: 'baz' } 
```

我们有一些定义在全局作用域中的对象`obj`，以及一个函数`modifyObj`，它可以接受分配给全局对象的键/值对。通过提供的注释，我们可以看到调用函数时会发生什么。

给定我们的函数是如何构造的，我们修改的对象是在哪里定义的，有什么东西阻止我们在函数之外直接与对象交互吗？

答案是否定的:

```
delete obj.foo // Yup I can do this!
// Or...
obj['foo'] = 'biz' // Totally valid - this is what our modifying function does! 
```

这并不理想。我们真的希望有一个我们可以修改的对象，但是我们也希望这样做时不要将对象暴露给全局范围。我们可以通过关闭来实现这一点！

闭包利用所谓的高阶函数(返回函数的函数)来封装私有范围内的变量和其他数据。返回的函数可以访问和修改该数据，这被称为围绕它创建闭包。

这意味着，我们可以在一个高阶函数的上下文中创建我们的对象，然后修改它而不暴露给全局范围。让我们看一下代码。

```
const higherOrderFunc = () => {
  const obj = {}
  return function modifyObj(...args) {
    const [key, value] = args
    obj[key] = value
    return obj
  }
}

const modifyObj = higherOrderFunc()
modifyObj('foo', 'bar') // Returns { foo: 'bar'}
modifyObj('biz', 'baz') // Returns { foo: 'bar', biz: 'baz' } !!!
console.log(obj) // ReferenceError: obj is not defined!

// And I have no method of messing with the object from the outside! 
```

让我们也浏览一下我们的代码，讨论一下发生了什么。

```
const higherOrderFunc = () => {
  const obj = {}
  return function modifyObj(...args) {
    const [key, value] = args
    obj[key] = value
    return obj
  }
} 
```

这是我们的结案陈词。我们创建了一个高阶函数，它返回一个内部函数，该函数保留了外部`obj`变量的知识。这样，我们可以调用我们的函数来修改`obj`。

```
const modifyObj = higherOrderFunc() 
```

这里，我们调用我们的高阶函数来创建我们的对象，并为我们提供一个 setter 函数。尚未对该对象进行任何操作。

```
modifyObj('foo', 'bar') // Returns { foo: 'bar'}
modifyObj('biz', 'baz') // Returns { foo: 'bar', biz: 'baz' } 
```

我们用闭包对对象进行操作，可以看到对象按照预期进行了更新！

```
console.log(obj) // ReferenceError: obj is not defined! 
```

最后，我们尝试记录闭包范围之外的对象。我们将得到一个引用错误，这意味着我们正试图对一个不存在于我们当前范围内的变量进行操作！

希望您能在当前或未来的工作中找到闭包的一些用处。你现在能想到它的一些用途吗？我们将在我的下一篇文章中讨论一个，以及在这种情况下闭包是多么强大。

*奖金*:我的功能只允许 setters。但是有一种方法可以扩展高阶函数来返回一个 getter，这样我们就可以检查我们的对象了。欢迎在下面的评论中提供你的解决方案！