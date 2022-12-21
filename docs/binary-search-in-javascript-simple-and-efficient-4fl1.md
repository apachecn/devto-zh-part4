# JavaScript 中的二分搜索法|简单高效

> 原文：<https://dev.to/kris/binary-search-in-javascript-simple-and-efficient-4fl1>

数据结构和算法的核心概念在编程领域是必不可少的。这些算法是我们在任何产品开发过程中使用的任何工具的重要组成部分。它有助于提高我们产品的整体性能和效率。所以，理解数据结构和算法可以让你成为一个更好更高效的程序员。在这里，我们将学习一种基本而有效的搜索算法，即二分搜索法算法，并用 JavaScript 程序举例说明。

### 什么是二分搜索法？

二分搜索法是一种搜索技术，用于在**排序列表**中定位特定项目。

请记住，必须对列表进行排序。

如果列表没有排序，那么算法将不能产生预期的结果。这种搜索的概念类似于二分搜索法树。

这种技术适用于**分治法**。

这种搜索技术的时间复杂度为 O(logn ),比复杂度为 O(n)的线性搜索技术快得多。

这种搜索技术重复地将数组或列表分成两半并执行搜索。

它的工作原理是检查我们的搜索值是小于、大于还是等于数组中的中间值。

算法很简单:

*   首先，如果搜索值小于列表的中间值，我们删除数组的右半部分。
*   第二，如果搜索值大于列表的中间值，我们可以删除数组的右半部分。
*   最后，如果搜索值等于列表的中间值，我们就返回值。

***看到了，一点也不难理解！*T3】**

现在，你一定明白了为什么在执行二分搜索法之前必须对列表进行排序。这样我们就可以适当地将列表分成两半，通过删除不相关的那一半来搜索我们的项目。这有效地使得算法更快，因为列表的一半被移除。

现在，让我们用 JavaScript 编程语言实现二分搜索法技术。

### 在 JavaScript 中实现

这个想法很简单。

将起始索引初始化为 0，然后将结束索引初始化为 list-1。通过将开始和结束索引相加并除以 2 来定位中间值。如果我们正在搜索的项目等于中间值，则返回 true，因为我们已经找到了该项目。如果该项大于中间值，则将起始索引初始化为比中间值大 1，即中间值+ 1，否则，如果该项小于中间值，则将结束索引初始化为比中间值小 1，即结束-1。这将每次删除数组的一半，直到只有中间值可用，然后如果它等于该项，则返回 true，否则返回 false。

用 JavaScript 实现二分搜索法的代码如下:

```
let BinarySearch = function (list, item) {

let startIndex = 0,
let endIndex = (list.length)-1;

while (startIndex <= endIndex ){

// Find the mid-value

    let midValue = Math.floor((startIndex + endIndex)/2);

// If searched item is located at mid, then return true

     if (list[midValue]===item){

         return true;

      }

// Else divide the array into left and right half

     else if (list[midValue] < item){

        startIndex = midValue + 1;

     }

     else{

        endIndex = midValue - 1;

     }

 }

    return false;

}

let list = [1, 5, 7, 9 ,10, 12, 14, 20];

let item = 7;

if (BinarySearch(list, item)){

  document.write("Item has been found!!");

}

else {

  document.write("Item not found!!!");

} 
```

**输出:**

```
Item has been found!! 
```

### 结论

本文定义了什么是二分搜索法技术及其在 JavaScript 中的实现。二分搜索法算法简单且易于实现。其搜索效率高于线性搜索技术。时间复杂度快很多。这种技术是数据结构和算法的基本部分之一，程序员必须了解这种技术，以便提供更高效、更快速的工作代码。

*原载于 2019 年 8 月 22 日*[*https://kriss . io*](https://kriss.io/binary-search-in-javascript-simple-and-efficient/)*。*

* * *