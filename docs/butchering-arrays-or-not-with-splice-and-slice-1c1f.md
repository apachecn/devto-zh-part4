# 使用拼接()和切片()切割(或不切割)阵列

> 原文：<https://dev.to/savagepixie/butchering-arrays-or-not-with-splice-and-slice-1c1f>

今天我已经深入研究了不同的方法来增加，减少和修改数组。方法`splice()`和`slice()`特别引起了我的注意，尤其是因为它们的名字如此相似，以至于当我第一次读到`slice()`时，我想，*等等，什么？我以为`splice()`在一个数组内移除并改变了东西？几分钟后，我发现少了 p。*

### 如何用 splice()从数组中删除元素

如果我们想从一个数组中移除最后一个元素，我们可以使用`pop()`(我喜欢它有暗示性的名字)。对于第一个元素，我们可以用`shift()`(我不那么喜欢这个名字，但还是有用的)。但是当我们想要移除数组中间的元素时会发生什么呢？或者如果我们想一次删除多个元素呢？

这就是`splice()`发挥作用的地方。此方法至少接受一个参数，最多可接受...你想要多少就有多少，真的，但是为了移除，最多需要两个。第一个参数是索引，在这里`splice()`将开始删除，第二个参数是它将删除的元素数量。作为回报，`splice()`会给我们一个删除了元素的数组。

那么我们来看一个例子:

```
let ourArr = [1, 2, 3, 4, 5, 6, 7, 8] //An array with 8 elements
let splicedStuff = ourArr.splice(3, 3) //We remove 3 elements starting from index 3
console.log(ourArr) //1,2,3,7,8
console.log(splicedStuff) //4,5,6 
```

正如我们在日志中看到的，`ourArr`现在有五个元素，而不是八个，索引 3-5 中的值已经被复制到`splicedStuff`中。

但是，如果我们省略第二个参数，`splice()`将删除从起始索引到数组末尾的所有元素。

```
let ourArr = [1, 2, 3, 4, 5, 6, 7, 8]; //Our known array
let splicedStuff = ourArr.splice(3) //We remove all the elements starting from index 3
console.log(ourArr) //1,2,3
console.log(splicedStuff) //4,5,6,7,8 
```

但是等等，还有更多！它还可以用新元素替换它移除的元素。

### 如何使用 splice()替换数组中的元素

我们只需要在第二个参数之后添加一个或多个参数，添加元素来代替我们移除的元素。注意，我们**不需要**来替换我们移除的所有元素。

```
let ourArr = [1, 2, 3, 4, 5, 6, 7, 8];
let splicedStuff = ourArr.splice(3, 3, 'four', 'five') //This time, we replace two of the three elements we remove
console.log(ourArr) //1,2,3,four,five,7,8
console.log(splicedStuff) //4,5,6 
```

### 如何用 slice()部分复制数组

如果我们想从一个数组中复制一些东西，但保持原始数组不变，我们可以使用方法`slice()`来指示开始和结束。请注意，开始是包容的，而结束是**排他的**(即不复制)。

```
let ourArr = [1, 2, 3, 4, 5, 6, 7, 8]; //Let's start with the same array, 'cause why not?
let slicedStuff = ourArr.slice(3, 6) //We take elements from indexes 3-5
console.log(ourArr) //1,2,3,4,5,6,7,8 original array untouched
console.log(slicedStuff) //4,5,6 
```

### 结论

有时我们可能想删除数组中间的一个或多个元素。当`pop()`和`shift()`不能胜任任务时，`splice()`接受挑战。它甚至可以用新的元素替换掉被移除的元素。如果我们想从一个数组中复制一些东西，但不动原来的，我们可以使用`slice()`。