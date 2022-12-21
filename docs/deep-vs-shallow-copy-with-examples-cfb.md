# 深层与浅层拷贝-示例

> 原文：<https://dev.to/laurieontech/deep-vs-shallow-copy-with-examples-cfb>

几周前，我写了一篇关于 spread operator 的帖子。

[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## Spread 运算符的 5 种用法

### 劳丽 1919 年 7 月 10 日 3 分钟阅读

#javascript #webdev #productivity #learning](/laurieontech/5-uses-for-the-spread-operator-b9i)
I noted that you could use it to copy both arrays and objects. However, there was one thing I didn't mention (and should have).

这些文案“浅薄”。这意味着它们复制原始对象中的所有值。然而，如果这些值是对其他数据结构的引用，事情就有点棘手了。

我认为说明这一点的最好方法是展示一堆不同的例子！希望这将有助于解释 spread operator 复制方式的潜在好处(和局限性)。

> 注意，这些例子都不包括循环引用或重复键等。您可能会遇到许多不同的问题，但是我们专注于“理想的”用例。

# 数组

这是一个复制扁平数组的简单例子。

```
let arr = [ 1, 2, 3 ]
let copy = [...arr]
arr.push(4)
// arr is [ 1, 2, 3, 4 ]
// copy is [ 1, 2, 3 ] 
```

Enter fullscreen mode Exit fullscreen mode

如果我们给`arr`加一个元素，`copy`不受影响。如果我们给`copy`增加一个元素，那么`arr`也是如此。

现在，如果我们的数组包含一个对象元素会怎么样？

```
let arr = [ 1, 2, {'a': 3} ]
let copy = [...arr]
arr[2]['a'] = 5
// arr is [ 1, 2, {'a': 5} ]
// copy is [ 1, 2, {'a': 5} ] 
```

Enter fullscreen mode Exit fullscreen mode

我们仍然可以使用 spread 运算符进行复制。然而，这引入了一些潜在的问题行为。如果我们改变对象的内容，它会影响原始数组和副本。对象是通过引用复制的，所以由`arr`和`copy`共享。

多维数组呢？

```
let arr = [ 1, 2, [3, 4] ]
let copy = [...arr]
arr[2][0] = 4
// arr is [ 1, 2, [ 4, 4 ] ]
// copy is [ 1, 2, [ 4, 4 ] ] 
```

Enter fullscreen mode Exit fullscreen mode

这个例子和上面的例子是一样的。数组是通过引用复制的，因此由`arr`和`copy`共享。

所以多维数组不能改？不完全是。

```
let arr = [ 1, 2, [3, 4] ]
let copy = [...arr]
arr[2] = [ 1, 2 ]
// arr is [ 1, 2, [ 1, 2 ] ]
// copy is [ 1, 2, [ 3, 4 ] ] 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，即使我们有一个多维数组，我们也是在顶层改变它。所以这只影响`arr`而不影响`copy`。即使`[3,4]`是共享的，一个新的数组`[1,2]`还是被`arr`创建和引用了。所以我们没有对`[3,4]`的内容做任何修改，我们只是删除了`arr`中对它的引用。

# 物体

让我们看看这种行为如何影响对象。第一个例子显示了复制平面对象时会发生什么。

```
let obj = {a:1, b:2, c:3}
let copy = {...obj}
obj['d'] = 4
// obj is {a:1, b:2, c:3, d:4}
// copy is {a:1, b:2, c:3} 
```

Enter fullscreen mode Exit fullscreen mode

与我们的数组一样，这两个对象是彼此的唯一克隆。

嵌套对象呢？

```
let obj = {a:1, b:2, c: {a:1}}
let copy = {...obj}
obj['c']['a'] = 5
// obj is {a:1, b:2, c: {a:5}}
// copy is {a:1, b:2, c: {a:5}} 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们在上面的数组示例中看到了类似的行为。嵌套对象是“共享”的，对它的任何改变都将在顶层对象`obj`和`copy`中显现。

# 那么这一切意味着什么呢？

事实证明，“深度复制”完全是基于你的原结构是否不止一级深。如果它是一个扁平的数组，或者一个扁平的对象结构，spread 操作符可以很好地创建一个克隆。

如果在数组或对象中引用另一个数据结构，就会出现“问题”。这些是通过引用复制的，对它们的更改会影响所有的“副本”。

# 如何获得深度副本

那么如果要“深度复制”会怎么样呢？嗯，你不要传播运营商！

对于多维数组，您可以这样做。

```
let arr = [ 1, 2, [3, 4] ]
var copy = JSON.parse(JSON.stringify(arr))
copy[2][0] = 1
// copy is [ 1, 2, [ 1, 4 ] ]
// arr is [ 1, 2, [ 3, 4 ] ] 
```

Enter fullscreen mode Exit fullscreen mode

即使数组引用了一个对象，它也能工作！

```
let arr = [ 1, 2, {'a': 3} ]
var copy = JSON.parse(JSON.stringify(arr))
arr[2]['b'] = 4
// arr is [ 1, 2, { a: 3, b: 4 } ]
// copy is [ 1, 2, { a: 3 } ] 
```

Enter fullscreen mode Exit fullscreen mode

# 结论

如果您总是使用扁平化的数据结构，深层和浅层副本可能是一个令人困惑的概念。希望这些例子能让你更好地理解这些术语的含义。

如果你正在寻找像这样的其他内容，看看下面的帖子。

[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 析构赋值的 3 个有力例子

### 劳丽 6 月 11 日 192 分钟阅读

#javascript #webdev #productivity #beginners](/laurieontech/3-powerful-examples-of-destructuring-assignment-jlf)[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 引入 Object.fromEntries

### 劳丽 7 月 2 日 193 分钟阅读

#javascript #webdev #learning #productivity](/laurieontech/introducing-object-fromentries-1d5l)[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 让我们绕圈...在 vs for...关于

### 劳丽 16 年 7 月 19 日 4 分钟阅读

#javascript #webdev #productivity #learning](/laurieontech/let-s-loop-for-in-vs-for-of-4loh)