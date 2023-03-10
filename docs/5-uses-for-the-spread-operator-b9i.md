# Spread 运算符的 5 种用法

> 原文：<https://dev.to/laurieontech/5-uses-for-the-spread-operator-b9i>

spread 操作符是 JavaScript 开发人员的最爱。这是一个强大的语法，有许多应用。

事实上，数量如此之多，以至于很难把它们都记录下来。在本帖中，我们将回顾 spread 运算符的 5 种最常见的用法。

# 复制一个数组

这是 spread 运算符最常见的用法之一。获取一个数组的内容并“展开”以填充另一个数组。

```
let arr = [1,2,3,4]
let copy = [...arr]
// copy is [ 1, 2, 3, 4 ] 
```

Enter fullscreen mode Exit fullscreen mode

换个角度来看，spread 操作符选择了`arr`数组中的每个元素，并将这些元素放入一个新的数组结构中。

请注意，这不同于将一个数组放入另一个数组中。

```
let arr = [1,2,3,4]
let copy = [arr]
// copy is [ [1, 2, 3, 4] ] 
```

Enter fullscreen mode Exit fullscreen mode

这个选项给了你一个多维数组。

# 串联数组

在前一个例子的基础上，我们发现您可以获得多个数组，并将它们分布在一个新的数组中。一个接一个。

```
let arr1 = [1,2,3,4]
let arr2 = [5,6,7,8]
let concat = [...arr1, ...arr2]
// concat is [ 1, 2, 3, 4, 5, 6, 7, 8 ] 
```

Enter fullscreen mode Exit fullscreen mode

如果我们像上一个例子那样分解它，spread 操作符将提取初始数组中的每个元素，并将其放入新数组中。

# 将参数作为数组传递

这就是 spread 操作符开始展示其多功能性的地方。在这个例子中，我们将三个参数传递给一个函数。spread 运算符用于内部包含三个元素的数组的前面。

```
function dev(x, y, z) { }

var args = [0, 1, 2]

dev(...args) // call function 
```

Enter fullscreen mode Exit fullscreen mode

理解这一点的一个好方法是看看我们之前的例子。如果我们在一个数组上使用了 spread 操作符，并且从来没有把它放在一个新的数组中，会发生什么？

数组中的每个元素都是独立的。这就是中间转换。每个元素都是独立的，没有放在新的数据结构中。因此，这三个元素都可以作为参数单独传递给函数。

# 复制一个对象

spread 运算符不仅可以用于数组，也可以用于对象。就像之前复制数组一样，我们可以复制一个对象。

```
let obj = {a: 1, b: 2, c: 3}
let copy = {...obj}
// copy is {a: 1, b: 2, c: 3} 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，spread 操作符从`obj`中提取每个键值对，并将它们放在一个新对象`copy`中。

就像数组示例一样，值得注意的是，这不同于将一个对象放入另一个对象中。

```
let obj = {a: 1, b: 2, c: 3}
let copy = {obj}
// copy is { {a: 1, b: 2, c: 3} } 
```

Enter fullscreen mode Exit fullscreen mode

# 合并对象

我们也可以使用 spread 操作符将两个对象合并在一起。

```
let obj1 = {a: 1, b: 2, c: 3}
let obj2 = {d: 4, e: 5, f: 6}

let merge = {...obj1, ...obj2}
// merge is {a: 1, b: 2, c: 3, d: 4, e: 5, f: 6} 
```

Enter fullscreen mode Exit fullscreen mode

同样，我们从初始对象中提取所有的键值对，并将它们放入一个新的对象数据结构中。

# 奖金——一个错误！

尽管 spread 操作符对数组和对象都有效，但是您不能混合和匹配这些数据类型。

```
let obj = {a:1, b:2, c:3}
let copy = [...obj] // this won't work! 
```

Enter fullscreen mode Exit fullscreen mode

如果我们考虑一下，这是有意义的，因为当处理数组时，spread 操作符处理的是元素，而对于一个对象，它是键值对。

# 就这样吧！

这是 spread 运算符的一大堆用途，甚至还不是全部。如果你想找全套的，看看 mozilla 文档。

如果您对其他可以帮助您编写干净代码的 JavaScript 语法感兴趣，请查看这些文章！

[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 析构赋值的 3 个有力例子

### 劳丽 6 月 11 日 192 分钟阅读

#javascript #webdev #productivity #beginners](/laurieontech/3-powerful-examples-of-destructuring-assignment-jlf)[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 出口、进口和违约，天啊！

### 劳丽 Apr 29 ' 19 分钟阅读

#javascript #react #webdev #beginners](/laurieontech/exports-and-imports-and-defaults-oh-my-fa3)[![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 引入 Object.fromEntries

### 劳丽 7 月 2 日 193 分钟阅读

#javascript #webdev #learning #productivity](/laurieontech/introducing-object-fromentries-1d5l)