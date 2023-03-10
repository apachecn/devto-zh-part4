# 我的新朋友过滤器()和地图()

> 原文：<https://dev.to/savagepixie/my-new-friends-filter-and-map-74a>

今天我一直在学习箭头函数。我不得不说，一开始我很难理解一个函数可以把另一个函数作为它的参数之一。但我想这需要时间。

总之，我学到的一个非常有趣的工具是方法`filter()`和`map()`。

### 他们是做什么的？

最基本的答案是他们在旧阵列的基础上创建一个新阵列。

它们使用一个函数来检查或操作一个数组的元素，并根据操作的结果，创建一个只包含一些元素或修改过的元素的新数组。让我们来看看它们各自是如何工作的。

#### 滤镜()

`filter()`接受一个函数，并用它来*过滤*数组中的元素。它返回一个新数组，其中只包含满足条件的元素。例如:

```
const array = [1, 5, 6, 7, 8, 12, 15] //We have an array with several numbers

//But we want an array with only even numbers
const newArr = array.filter((num) => num % 2 === 0) //newArr = [6, 8, 12] 
```

现在我们有了新的数组`newArr`，它只包含了`array`中的偶数。这个方法允许我们从一个给定的数组中选择我们想要的元素，而不需要创建一个遍历所有元素的循环。

#### 地图()

接受一个函数并在数组的每个元素上调用它。它返回一个新的数组，其中包含改变后的元素。比如:

```
const array = [1, 5, 6, 7, 8, 12, 15] //Again, array with several numbers

//For some reason, we would like them to be multiplied by 10
const newArr = array.map((num) => num * 10) //newArr = [10, 50, 60, 70, 80, 120, 150] 
```

但是等等，还有更好的。如果我们想过滤一个数组的元素**并修改它们的值，会发生什么呢？那么我们可以像这样一起调用这两个方法:** 

```
const newArr = array.filter(function).map(function) 
```

基于我们之前的例子，假设我们想筛选一个数组，只取偶数，然后我们想把这些数放大十倍。我们可以这样做:

```
const array = [1, 5, 6, 7, 8, 12, 15] //Our beloved starting array

const newArr = array.filter((num) => num % 2 === 0).map((num) => num * 10) //newArr = [60, 80, 120] 
```

### 结论

当我们想要通过只取它的一些元素或者在每个元素上调用一个函数来创建一个基于旧数组的数组时，我们可以使用方法`filter()`和`map()`来使我们的生活变得更容易。