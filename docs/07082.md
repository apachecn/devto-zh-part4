# 理解 Rest 参数语法

> 原文：<https://dev.to/laurieontech/understanding-rest-parameter-syntax-1apn>

欢迎使用 Rest 参数语法。就像是传播算子！

[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## Spread 运算符的 5 种用法

### 劳丽 1919 年 7 月 10 日 3 分钟阅读

#javascript #webdev #productivity #learning](/laurieontech/5-uses-for-the-spread-operator-b9i)

...除了没有。

我想先说，我并不是在为 JavaScript 语言中重复使用符号`...`辩护。但希望这篇文章能帮助你理解 spread 和 rest 的不同和相似之处。

## 用例为 rest

假设你想给一个函数传入未知数量的参数。这时你就要使用 rest 参数了！

```
function testRest(arg1, arg2, ...otherArgs) {
   console.log(arg1) // 1
   console.log(arg2) // 2
   console.log(otherArgs) // [3, 4, 5]

}

testRest(1, 2, 3, 4, 5) 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，`arg1`和`arg2`按照预期被传递，然后所有附加的参数被添加到`otherArgs`数组中。

好处之一是`otherArgs`确实是一个数组。这意味着所有的数组函数都是可用的。

你可以看看里面的参数数量。

```
function testRest(arg1, arg2, ...otherArgs) {
   const numOfArgs = otherArgs.length() // 3

}

testRest(1, 2, 3, 4, 5) 
```

Enter fullscreen mode Exit fullscreen mode

您还可以使用数组原型函数来操纵它。`map`、`filter`、`reduce`、`flat`等。

## 单个多余的自变量

需要记住的一点是，`otherArgs`总是会创建一个数组。这意味着单个参数将被包装在一个数组中。

```
function testRest(arg1, arg2, ...otherArgs) {
   console.log(arg1) // 1
   console.log(arg2) // 2
   console.log(otherArgs) // [3]

}

testRest(1, 2, 3) 
```

Enter fullscreen mode Exit fullscreen mode

此外，不包括任何附加参数将导致空数组。

```
function testRest(arg1, arg2, ...otherArgs) {
   console.log(arg1) // 1
   console.log(arg2) // 2
   console.log(otherArgs) // []

}

testRest(1, 2) 
```

Enter fullscreen mode Exit fullscreen mode

## 还记得析构吗？

如果你需要快速刷新析构赋值，看看这篇文章。

[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 析构赋值的 3 个有力例子

### 劳丽 6 月 11 日 192 分钟阅读

#javascript #webdev #productivity #beginners](/laurieontech/3-powerful-examples-of-destructuring-assignment-jlf)

事实证明，您可以一起使用析构和 rest 参数。

```
function testRest(...[first, second, third]) {
   console.log(first) // 1
   console.log(second) // 2
   console.log(third) // 3

}

testRest(1, 2, 3) 
```

Enter fullscreen mode Exit fullscreen mode

请记住，析构要求参数在那里。如果你违反了合同，你应该知道会发生什么。

如果你没有包含一个被析构的预期参数，它将导致一个未定义的引用。

```
function testRest(...[first, second, third]) {
   console.log(first) // 1
   console.log(second) // 2
   console.log(third) // undefined

}

testRest(1, 2) 
```

Enter fullscreen mode Exit fullscreen mode

如果你包含了一个超出你所构造的参数，这个参数将被删除。

```
function testRest(...[first, second, third]) {
   console.log(first) // 1
   console.log(second) // 2
   console.log(third) // 3
   // 4 is not destructured

}

testRest(1, 2, 3, 4) 
```

Enter fullscreen mode Exit fullscreen mode

### 结论

现在你知道了！正如你在上面的例子中看到的，rest 和 spread 的主要区别在于位置。

扩展语法可以出现在函数内部，也可以在调用函数时出现。Rest 参数语法仅限于函数签名本身。如果您试图确定哪个正在被使用，请记住这一点。

希望这有助于你更好地理解这些概念。

和往常一样，如果你对这样的概念感兴趣，可以看看下面的一些帖子:

[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 拼接！切！该死，我是说换档！

### 劳丽 7 月 30 日 194 分钟阅读

#webdev #javascript #learning #productivity](/laurieontech/splice-slice-shoot-i-meant-shift-424p)[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 让我们绕圈...在 vs for...关于

### 劳丽 16 年 7 月 19 日 4 分钟阅读

#javascript #webdev #productivity #learning](/laurieontech/let-s-loop-for-in-vs-for-of-4loh)[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 深层与浅层拷贝-示例

### 劳丽 7 月 24 日 194 分钟阅读

#javascript #webdev #learning #beginners](/laurieontech/deep-vs-shallow-copy-with-examples-cfb)