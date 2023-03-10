# JavaScript 数组初学者指南[第 1 部分]

> 原文：<https://dev.to/ogurinkaben/beginner-s-guide-to-javascript-arrays-part-1-1p2n>

当构建基本或复杂的 web 应用程序时，您可能会遇到数组，能够在数组中操作数据将被证明是非常有用的，尤其是当您还在尝试熟悉 Javascript 时。我将花时间解释什么是数组，何时以及如何使用它的方法。

## 什么是数组？

简单地说，数组可以说是一种令人印象深刻的展示或一系列特定类型的事物。在编程和数据结构方面，解释更进一步。
*数组数据结构，或简称数组，是由元素集合组成的数据结构，每个元素至少由一个数组索引或键标识。但是将一个数组看作同类型变量的集合通常更有用*

在 javascript 中，数组是通过指定数组名，后跟赋值运算符(=)来创建的，数组的值放在方括号中，用逗号分隔。那就是

```
 let arrayname = []; // An empty array

let array = ['ben', 'tammy','bentammy']; //array with values. 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用索引键来访问数组的值，索引键指定数组出现的位置。javascript(和大多数编程语言)中的数组索引通常从 0 和 1 开始，所以当你想引用数组中的第一项时，你应该用“0”而不是“1”。例如

```
 // We create an array of friends

let friends = [
'Daniel', 
'Elvis', 
'Favour',
'Preye',
'Kevwe',
'Deborah',
'Ellie',
'Miracle',
'Joshua',
'Casey',
]; 
```

Enter fullscreen mode Exit fullscreen mode

该数组的第一个值将使用索引键 0 来访问。所以如果我们想输出它，我们应该有类似于
的东西

```
 console.log(friends[0]); // This will print Daniel

console.log(friends[1]); // This will print  Elvis 
```

Enter fullscreen mode Exit fullscreen mode

假设您想获得数组的完整长度，并知道它保存了多少个值，您将使用“length”属性返回数组的完整长度。使用上述数组
的示例

```
 console.log(friends.length); // Prints 10 to the console. 
```

Enter fullscreen mode Exit fullscreen mode

## 修改数组

在处理数据时，通常需要添加新数据、删除旧数据或修改现有数据，javascript 提供了实现这一点的解决方案。

### 添加新的数组项

我们仍将使用先前定义的数组。假设我认识了一个新朋友，我也想和他们交朋友，我可以很容易地把他们添加到我的朋友列表中。push()”方法。

示例

```
 friends.push("Nora"); // This adds a new array item to the friends array 
```

Enter fullscreen mode Exit fullscreen mode

还可以使用。长度属性

示例

```
 friends[friends.length] =  "Victor"; //Adds Victor to the array 
```

Enter fullscreen mode Exit fullscreen mode

### 从数组中删除项目

您可以选择使用一些内置的 javascript 方法来删除现有的数组元素。在下面的例子中，我们仍然使用之前的 friends 数组来演示这一点

#### [T1。流行()](#pop)

pop 方法移除数组的最后一个元素，返回该元素，并更新长度属性

```
 friends.pop(); // Will remove Nora from the array 
```

Enter fullscreen mode Exit fullscreen mode

#### [T1。shift()](#shift)

shift 方法的工作方式与 pop 方法非常相似，只是它移除的是 JavaScript 数组的第一个元素，而不是最后一个

```
 friends.shift(); // Will remove Daniel from the array 
```

Enter fullscreen mode Exit fullscreen mode

#### [T1。拼接()](#splice)

拼接方法可用于在阵列中添加或删除元素。从数组中移除项时，通常需要两个参数。第一个参数指定开始移除元素的位置。第二个参数指定要移除的元素数量

```
 friends.splice(2,2);

/* This will remove two elements from the friends array starting from the element with the index of 2.
The above will remove Favour and Preye from the array
*/ 
```

Enter fullscreen mode Exit fullscreen mode

### 修改数组中的项目

我们可以通过简单地引用一个数组的索引并改变它的值来改变它的值。使用我们的朋友数组，让我们假设我们想要将*丹尼尔*的名字改为*奥克*，我们将简单地做下面的

```
 friends[0] =  "Oke"; // Changes Daniel to Oke 
```

Enter fullscreen mode Exit fullscreen mode

## 迭代显示数组内容

不仅显示数组中的一个元素，而且能够显示数组中的所有元素通常是有用的。这个过程称为“迭代”,通常的做法是迭代一个数组，或者执行特定的操作，或者只是显示它们。这可以通过多种方式实现

### 为循环

for 循环是 javascript 中执行迭代的一种非常常见的方式，当您想要显示数组的内容时，它非常方便。它通常有三个参数，第一个是初始化循环，第二个指定条件，第三个指定应该执行什么操作。假设我们想要打印 friends 数组的所有内容，我们应该有类似于
的东西

```
 for (i=0; i<friends.length; i++)
{
console.log(friends[i]);

} 
```

Enter fullscreen mode Exit fullscreen mode

综上所述，
我们从 0 开始初始化循环，然后我们指定我们的条件，然后是一个动作。我们告诉浏览器，只要我们的初始值“I”小于数组的长度，它就应该将“I”的值加 1。在 for 循环中，我们告诉浏览器根据“I”的当前值打印数组的当前索引。所以当" I "的值为零时，浏览器打印" Daniel ",当值为 1 时，浏览器打印" Elvis ",如此循环下去，直到没有其他内容需要打印。

### forEach()

这是我们可以用来对数组中的每一项执行函数的数组方法。它是专门为处理数组而设计的，并提供了一种更现代的方法来迭代数组和执行不同的操作。为了达到与上面相同的结果，我们可以简单地做

```
 friends.forEach( e => {
  console.log(e); 
});

// This will still print all elements of the friends array 
```

Enter fullscreen mode Exit fullscreen mode

forEach 循环有时需要一些时间来适应，所以你可以在这里阅读更多关于它如何工作的内容。。

暂时就这样吧！！在本文的第 2 部分，我们将进一步讨论数组方法。谢谢大家！！！