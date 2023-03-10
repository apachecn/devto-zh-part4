# 散开

> 原文：<https://dev.to/austinbh/spreadin-out-33n5>

什么是传播运算符，为什么它很重要？当我开始进入 React 时，这个问题是我思考了很久的。

随着我继续我的 JavaScript 之旅，我想利用这些博客不仅了解更多关于这种语言的不同特性，而且希望对试图理解这种古怪而强大的语言的其他人有所帮助。

## 什么是传播算子？

`MDN's definition can seem a bit confusing at first.` Spread 语法允许在预期零个或多个参数(对于函数调用)或元素(对于数组文字)的地方扩展可迭代对象，例如数组表达式或字符串，或者在预期零个或多个键值对(对于对象文字)的地方扩展对象表达式。

下面是 spread 运算符的一个简单应用示例。

```
let account = {
   username: '',
   password: ''
}

account = {...account, username: 'Foo'} 
```

好吧，这意味着什么？在上面的块中，我们使用了 spread 操作符来维护我们之前的 account 对象，但是覆盖了 username 键的值。在这个例子中，这似乎有点勉强，因为我们可以简单地覆盖帐户的用户名值。然而，其思想是，扩展操作符将 account 对象的键-值对“扩展”到我们的 account 的重新分配中。

```
let account = { username: '', password: '' }
/*
If we are assigning key-value pairs when creating the account
object, the spread operator is 'spreading' out those key-value
pairs into distinct pairs rather than being wrapped in
the original account object.
*/
...account = username: '', password: '' 
```

一种概念化的方法是认为 spread 操作符去掉了 account 对象的括号，这样我们就可以访问它的键值对。

## 为什么有关系？

扩展运算符在 React 中非常有用。当我们总是更新我们的状态时，我们希望确保我们不会修改状态，而是使用 setState 用一个新对象重新定义我们的状态。spread 操作符允许我们复制当前状态，而无需实际修改当前状态。

这意味着我们可以展开我们的当前状态，并且只修改 1 部分。我发现这在处理嵌套状态时非常有用。

```
state = {
   user: {
      username: '',
      password: ''
   },
   isLoading: false
}

this.setState({user: {...this.state.user, username: 'Foo'}}) 
```

在上面的例子中，我们像在第一个例子中那样修改用户名。然而，我们正在处理一个嵌套的对象，我们不想修改用户的密码。通过使用 spread 操作符，我们可以修改用户名并使用初始声明中的密码。

我们还可以对数组使用 spread 操作符来实现类似于 concat 操作的交互。

```
state = {
   numbers: [1,2,3]
}

this.setState({numbers: [...this.state.numbers, 4]})

this.setState({numbers: this.state.numbers.concat(4)}) 
```

这类似于前面的例子，我们展开一个对象来保持原始状态。然而，这里我们处理的是一个数组而不仅仅是一个对象，我们可以简单地提供一个值附加到数组中。第二个例子，concat 也不是一个破坏性的操作，因此不会错误地修改我们的状态。但是，concat 方法更常用于合并两个或多个数组。因此，spread 操作符在这里更好一点，因为我们知道要向数组中添加什么值。

我们可以使用许多非破坏性的操作来更新我们的状态，而无需修改原始状态。然而，spread 操作符是我发现自己反复使用的一个操作符。它的多功能性和非破坏性使它成为处理状态更新的最有吸引力的方法，在这种情况下，我们不能简单地覆盖一个值，而是必须保留状态对象的其他部分。

## 参考文献

*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Operators/Spread _ syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Array/concat](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)