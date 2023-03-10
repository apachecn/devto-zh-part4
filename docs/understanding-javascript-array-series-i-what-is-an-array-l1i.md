# 理解 Javascript 数组系列 I -什么是数组？

> 原文：<https://dev.to/nedyudombat/understanding-javascript-array-series-i-what-is-an-array-l1i>

## 什么是数组？💁

在计算机编程中，变量是一个存储单元，它保存一些已知或未知的数据(信息)，通常称为值。数组是一种特殊的变量，一次可以保存多个值。Javascript 和任何其他编程语言中的数组也是如此。这些值可以是不同的数据类型。

假设你想在不同的变量中存储几个名字，你可以这样做:

```
 const player1 = 'Messi';
 const player2 = 'Ronaldo';
 const player3 = 'Kante';
 const player4 = 'Dembele'; 
```

要访问这些名字，我们必须单独调用这些变量，这在我们有大约 100 个名字的情况下是很繁琐的，但是如果我们用数组存储我们的名字，我们可以很容易地用 for 来访问它们...循环。

```
 const players = ['Messi', 'Ronaldo', 'Kante', 'Dembele']; 
```

数组是一种特殊类型的对象。区别在于数组使用索引来访问其中的项，而对象使用它们的键。Javascript 遵循零索引符号，当数组中的每个元素被添加到数组中时，它们都被赋予一个从 0 开始的索引。其他一些语言遵循一个索引符号，其中元素被赋予从 1 开始的索引。

```
 // indexes     0         1           2       3
 const arr = ['Nedy', 'Javascript', 'Tech', 'soccer'];

 //            key     value            key            value
 const obj = {name: 'Nedy Udombat', profession: 'Software Developer'}; 
```

#### 我们如何创建一个数组？

在 Javascript 中，可以通过多种方式创建数组:

*   使用数组文字:这是在 Javascript 中创建数组最简单也是最常见的方式。这包括声明一个变量并用方括号中存储的值初始化它。让我们在下面创建一些数组:

```
 // empty array
 const emptyArray = [];

 const players = ['Messi', 'Ronaldo', 'Kante', 'Dembele'];

 // arrays can have values of different data types in them.
 const arr = [1, '1', undefined, NaN, true, {name: 'Nedy'}];

 // arrays can even consist of other arrays and functions in them.
 const arrInArray = [1, [ '1', 2], function() { console.log('Hey Nedy'); }]; 
```

*   使用 New 关键字:这允许您在声明期间设置额外的选项，如数组的长度。

```
 // specifying the length of the array during the declaration
 const lengthyArray = new Array(8)

 console.log(lengthyArray) // []
 console.log(lengthyArray.length) // 8

 const players = new Array('Messi', 'Ronaldo', 'Kante', 'Dembele'); 
```

创建数组时，需要注意的是，如果在初始化数组时有多个项，这些项应该用逗号分隔。

#### 我们如何访问数组中的项目？

我们可以通过将元素的索引放在数组变量`arr[index]`后面的方括号中来访问数组中的元素。

```
 const players = ['Messi', 'Ronaldo', 'Kante', 'Dembele'];

 console.log(players[0]) // Messi
 console.log(players[1]) // Ronaldo 
```

至此，您应该对 Javascript 中的数组以及如何创建数组有了基本的了解。这是我的 Javascript 数组系列的第一部分。下一个系列出来的时候请关注我。

以下是我撰写的关于这个阵列系列的其他文章的链接:

*   [创建数组的其他方法](https://dev.to/nedyudombat/understanding-javascript-array-series-ii-alternate-ways-of-creating-an-array-26c8)
*   [数组属性](https://dev.to/nedyudombat/understanding-javascript-array-series-iii-array-properties-43co)