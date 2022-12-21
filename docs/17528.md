# JavaScript 数组教程-入门

> 原文：<https://dev.to/jamesqquick/javascript-arrays-tutorial-getting-started-34n8>

[https://www.youtube.com/embed/xh7njXASFjU](https://www.youtube.com/embed/xh7njXASFjU)

数组是任何语言中最常见的数据类型之一。在本文中，让我们探索在 JavaScript 中使用数组。

## 什么是数组

数组是一种允许您存储一系列或一列数据的数据类型。在 JavaScript 中，数组...

*   大小是动态的
*   可以保存任何类型的数据

## 创建一个数组

以下是创建数组的几种不同方法。

### 创建一个空数组

```
const arr = []; 
```

Enter fullscreen mode Exit fullscreen mode

### 创建一个已经包含元素的数组

```
const arr = [1,2,3,4,5,6]; 
```

Enter fullscreen mode Exit fullscreen mode

### 创建一个包含已有元素的数组(不同的数据类型)

```
const arr = [1,2,"James", "Jess", true]; 
```

Enter fullscreen mode Exit fullscreen mode

## 向数组中添加元素

这里有一些不同的方法来添加元素到你的数组。

### 用 Push 在末尾添加一个元素

```
const arr = [1,2,3,4,5,6];
arr.push(7); //[1,2,3,4,5,6,7] 
```

Enter fullscreen mode Exit fullscreen mode

### 用 Unshift 在开头添加一个元素

```
const arr = [1,2,3,4,5,6];
arr.unshift(0); //[0,1,2,3,4,5,6] 
```

Enter fullscreen mode Exit fullscreen mode

## 从数组中取出元素

现在，我们如何访问数组中的特定元素呢？为此，您可以使用索引(从零开始)。

### 获取数组的第一个元素

```
const arr = [1,2,3,4,5,6];
arr[0];//1 
```

Enter fullscreen mode Exit fullscreen mode

### 获取数组的最后一个元素

```
const arr = [1,2,3,4,5,6];
arr[arr.length-1];//6 
```

Enter fullscreen mode Exit fullscreen mode

### [更新数组的第一个元素](#update-the-first-element-of-the-array)

```
const arr = [1,2,3,4,5,6];
array[0] = 0; //[0,2,3,4,5,6] 
```

Enter fullscreen mode Exit fullscreen mode

## 数组排序

数组有一个排序函数，可以尝试自动排序，或者您可以对它进行定制，以确定如何对项目进行特定排序。

### 排序数字

```
const arr = [4,2,6,8,3];
const sortedArr = arr.sort(); //[2,3,4,6,8] 
```

Enter fullscreen mode Exit fullscreen mode

### 排序名称

我将整理我妻子和我以及我们三只狗的名字。是的，我们的狗是以哈利波特人物命名的！

```
const arr = ["Jess", "James", "Padfoot", "Lily", "Sevi"];
const sortedArr = arr.sort() //["James", "Jess", "Lily", "Padfoot", "Sevi"]; 
```

Enter fullscreen mode Exit fullscreen mode

## 遍历一个数组

### 使用一个标准进行循环

```
const arr = ["Jess", "James", "Padfoot", "Lily", "Sevi"];
for(let i = 0; i< arr.length; i++){
console.log(arr[i]);
} 
```

Enter fullscreen mode Exit fullscreen mode

### 用字母表示的

```
const arr = ["Jess", "James", "Padfoot", "Lily", "Sevi"];
for(let name of arr){
console.log(name);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 额外的数组函数

这些函数将使用 Arrow 函数语法，所以如果您还不了解的话，请花些时间阅读它们。

> 此时，您可能需要研究一下 [ES6 箭头函数](https://www.sitepoint.com/es6-arrow-functions-new-fat-concise-syntax-javascript/)。

### 使用 For Each 进行循环

```
const arr = ["Jess", "James", "Padfoot", "Lily", "Sevi"];
arr.forEach( name => console.log(name)); 
```

Enter fullscreen mode Exit fullscreen mode

### 地图

Map 允许您通过以某种方式转换每个项目，从现有数组创建一个新数组。例如，让我们尝试创建一个新的数组，在现有数组中的每个元素上加 1；

```
const arr = [1,2,3,4];
const newArr = arr.map(item => item+1); //[2,3,4,5] 
```

Enter fullscreen mode Exit fullscreen mode

### 减少

Reduce 函数将允许你把一个数组转换成一个最终结果。使用 Reduce 最常见的例子是对数组中的所有元素求和。

```
const nums = [1,2,3,4,5];
nums.reduce( (total, num) => total += num, 0); 
```

Enter fullscreen mode Exit fullscreen mode

## 复制数组

复制数组有几种不同的方法。让我们看看两种最现代的方法。

### 切片

```
const arr = [1,2,3,4,5];
const copy = arr.slice(); 
```

Enter fullscreen mode Exit fullscreen mode

### 传播算子

```
const arr = [1,2,3,4,5];
const copy = [...arr]; 
```

Enter fullscreen mode Exit fullscreen mode