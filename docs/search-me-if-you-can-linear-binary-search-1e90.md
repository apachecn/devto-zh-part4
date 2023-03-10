# 有本事搜我！！线性和二分搜索法

> 原文：<https://dev.to/shimphillip/search-me-if-you-can-linear-binary-search-1e90>

当你在一个数组中搜索一个元素时，你的默认方法是什么？你可能熟悉流行的`indexOf`方法，或者如果你精通函数式编程范式，那么`find`或`findIndex`可能会让你想起什么。如果有一天这些方便的数组方法从你身边剥离了怎么办？你将如何自己实现一个解决方案？今天我们将讨论如何自己实现搜索算法。

## 线性搜索

这可能是你能采取的最天真的方法，几乎是一种蛮力方法。该算法简单地从头开始遍历给定的数组，并将每个值与提供的匹配值进行比较。让我们创建一个名为 linearSearch 的函数来返回匹配的第一个索引(如果有)，否则返回 false。

```
linearSearch([1,2,3,4,5], 5)

function linearSearch(arr, match) {
  for(let i=0; i<arr.length; i++) {
    if(arr[i] === match) {
      return i;
    }
  }
  return false;
} 
```

很简单，对吗？

使用线性搜索算法的一个优点是传入的数组不必排序。这个数组可能看起来像`[3,1,4,2,5]`一样混乱，但它仍然可以工作。实际上，我们只是基本上实现了`Array.indexOf`方法😄

缺点是什么？想象一下，如果数组包含数千个值，而我们的匹配恰好位于最后一个索引。更糟糕的是，如果根本没有匹配呢？对我们的计算机来说，迭代一大组数字并进行计算是非常费力和耗时的。我认为我们可以做得更好！顺便说一下，因为我们使用的是单循环，所以它的复杂度是 O(n)。

## 二分搜索法

在二分搜索法，我们通过将搜索间隔减半来寻找我们的匹配。想象一下，将我们传入的数组重复分成长度为其一半的子数组，直到找到匹配为止。那会很有效率的！高效到时间复杂度只有 **O(log n)** 。这是它的工作原理。如果中间的值小于匹配值，则意味着匹配值是在后半部分找到的，在后半部分其值大于中间值(也就是说，如果有任何匹配)。如果中间的值大于匹配值，那么我们可以在数组的前半部分再次搜索。冲洗重复，分而治之！现在用一些代码来解释一下:)

```
function binarySearch(arr, match) {
  let start = 0; // first index
  let end = arr.length - 1; // last index
  let middle = (start + end) / 2; // middle index
} 
```

这是我们的起始代码，非常简单。作为 arr 传入`[1,2,3,4,5]`的结果是'(0 + 4) / 2 = 2 '，在索引 2 处是我们的中间数。不过，有一个陷阱需要注意！只有当数组的长度为奇数时，我们的中间值才是正确的整数索引。为了说明偶数长度的数组，让我们稍微修改一下第三行。

```
 let start = 0;
  let end = arr.length - 1;
  let middle = Math.floor((start + end) / 2); 
```

没有 Math.floor，传入[1，2，3，4]的结果是 1.5。使用它会将数字向下舍入到 1，因此 middle 指向数组中的数字 2。现在我们算法的面包和黄油。

```
function binarySearch(arr, match) {
  let start = 0;
  let end = arr.length - 1;
  let middle = Math.floor((start + end) / 2);

  while(arr[middle] !== match) {
    if(match > arr[middle]) {
      start = middle + 1;
    } else {
      end = middle - 1;
    }
    middle = Math.floor((start + end) / 2);
  }
  return middle;
} 
```

我们创建了一个 while 循环来不断重复一些动作，直到中间值等于我们的匹配值。在循环内部，如果我们的匹配大于当前的中间值，这意味着我们的匹配可以在数组的后半部分找到。因此，我们可以通过将起始索引 1 移动到大于中间索引来安全地排除前半部分。如果我们的匹配较小，那么我们的匹配属于前半部分，并使我们的结束指数比中间指数小 1。这意味着我们的数组必须是一个有序的数组。

我们刚刚把数组缩小了一半，所以我们需要用修改后的 start 或 end 重新设置中间值。我们一直重复，直到找到匹配，然后返回它的索引。

太好了！但是如果数组中没有匹配的呢...我们在 while 循环中的条件将永远旋转，并导致无限循环。这是解决办法。

```
function binarySearch(arr, match) {
  let start = 0;
  let end = arr.length - 1;
  let middle = Math.floor((start + end) / 2);

  while(arr[middle] !== match && start <= end) {
    if(match > arr[middle]) {
      start = middle + 1;
    } else {
      end = middle - 1;
    }
    middle = Math.floor((start + end) / 2);
  }

  if(arr[middle] === match) {
    return middle;
  }
  return false;
} 
```

有什么变化？while 循环条件和我们的返回语句！如果中间值没有找到我们的匹配，除了继续运行循环之外，我们还检查 start 是否小于或等于 end，这样如果 start 大于 end，循环就可以说‘数组中没有匹配！’然后退出。想象一个场景，我们通过[1，3，5]和 6 作为匹配。我们首先从索引 1 开始，值 3 作为我们的中间值，因为 6 大于 3，开始变成索引 2，等于结束，因此中间值也变成 2。这一次循环再次运行，检查中间值是否匹配，如果不匹配，它将把 start 或 end 向一个方向移动 1，因此在下一次迭代中，start 大于 end，因此循环不会执行。

如果我们最终在数组中找到了匹配，返回索引！否则，返回 false。

## 总结

线性搜索写起来很直观，也很有逻辑性，我们不需要传入一个已排序的数组。但是很慢。二进制搜索要快得多，但对逻辑来说更具挑战性，传入的数组需要排序。因此，如果您正在处理大型数据集，请使用二分搜索法算法！！😉感谢你们阅读！