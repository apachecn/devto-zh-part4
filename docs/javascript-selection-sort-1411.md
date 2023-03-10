# JavaScript 选择排序

> 原文：<https://dev.to/letmypeoplecode/javascript-selection-sort-1411>

昨晚我玩 JavaScript 冒泡排序玩得很开心，今晚我挑战自己去理解并编写一个选择排序。

## 什么是选择排序？

选择排序对数组中的每个元素运行一次。在每一次运行中，它从该元素开始到数组的末尾查找元素集合中的最小值。在运行结束时，如果那个元素不是最小的，它就与最小的元素交换。

我们来看一个 4 元素的数组，`[8, 3, 1, 2]`。

对于第一次传递，我们将创建一个值为 0 的变量`n`来保存传递中最小值的数组索引。

**第一关:**

```
Compare 8 to 3, 3 wins and `n` = 1\.  
Compare 3 to 1, 1 wins and `n` = 2\.  
Compare 1 to 2, 1 wins and `n` remains 2\.  
Swap the values at indexes 0 and 2, and the array is now `[1, 3, 8, 2]`. 
```

Enter fullscreen mode Exit fullscreen mode

我们知道数组中的第一项现在是最小的，所以我们将从第二项开始，`n`从 1 开始。

**第二关:**

```
Compare 3 to 8, 3 wins and `n` remains 1\.  
Compare 3 to 2, 2 wins and `n` = 3\.  
Swap the values at indexes 1 and 3, and the array is now `[1,2,8,3]`. 
```

Enter fullscreen mode Exit fullscreen mode

现在我们将 n 增加到 2，再次运行。这实际上是我们需要的最后一次传递，因为我们正在比较最后两个值。

**第三关:**

```
Compare 8 to 3, 3 wins, and `n` = 3\.  
Swap the values at indexes 2 and 3, and the array is now `[1,2,3,8]`. 
```

Enter fullscreen mode Exit fullscreen mode

我们被分类了。

## JavaScript 中的一个选择排序

这是代码。

```
function selsort(arr) { 
  var arlen = arr.length; 
  for (var i = 0; i < arlen - 1; i++) { 
    let lowest = i; 

    for (let n = i + 1; n < arlen; n++) { 
      if (arr[n] < arr[lowest]) lowest = n; 
    } 

    if (lowest !== i) {
      [arr[lowest], arr[i]] = [arr[i], arr[lowest]]; 
    } 
  } 
  return arr;
}

console.log(selsort([4, 15, 2, 9, 31, 3])); 
```

Enter fullscreen mode Exit fullscreen mode

并且控制台显示:`[2, 3, 4, 9, 15, 31]`

有几件事需要注意。

在外部循环(第 3 行)中，我们只需要运行它到数组的长度减 1。当我们到达倒数第二个值时，与最后一个值的比较完成了排序。

此外，由于我们已经将`lowest`变量设置为`i`(第 4 行)，我们从`i` + 1 开始内部循环(第 6 行)，否则我们将浪费时间比较索引`i`和它本身。

我以前读过关于[析构赋值](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)的内容，但是如果你不使用它，你就会失去它。它就像大学西班牙语中“estar”的虚拟变位一样从我的脑海中消失了。

我确信一定有比创建一个临时变量，然后运行两个赋值操作，做一些谷歌搜索，然后析构更短的方法来交换数组中的变量(第 11 行)。我本可以在我的冒泡排序中多保存两行。

给你。