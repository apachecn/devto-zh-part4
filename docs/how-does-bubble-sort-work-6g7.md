# 冒泡排序是如何工作的？

> 原文：<https://dev.to/yongliang24/how-does-bubble-sort-work-6g7>

### 什么是冒泡排序？

冒泡排序是一种算法，其中最大的值将冒泡到列表的顶部，继续这样排序，我们将得到一个升序排序的数组或列表。我们将使用嵌套循环来比较每个当前项和列表中的其余项。

### 了解如何使用 Javascript 交换两个元素

```
//swapping function takes an array, and two indexes

 function swap2(arr, index1, index2){
     let temp = arr[index1]; //let a temp variable to hold index1's value
     arr[index1] = arr[index2]; //replace index1 with index2' value
     arr[index2] = temp;//finally replace index2 with temp, which was index1
 } 
```

在 ES2015 中，我们可以使用以下语法进行交换

```
const swap = (arr, index1, index2) => {     
[arr[index1], arr[index2]] = [arr[index2], arr[index1]]
 } 
```

### 使用嵌套数组实现冒泡排序——Javascript

Sudo 代码:
1 .反向迭代数组
2 .创建计数器变量
3 .迭代内部循环以比较每个项目
4 .如果当前项目大于
则交换项目 5 .当交换发生时，将 noSwap 设置为 false，以便它不会中断循环
6 .当 noSwap 从不设置为 false 时，循环中断。

```
function BubbleSort(arr){
    for(let i = arr.length; i >0; i--){ 
        let noSwap = true; 
        for(let j =0; j < (i-1); j++){ 
            if(arr[j] > arr[j+1]){ 
                let temp = arr[j];
                arr[j] = arr[j + 1];
                arr[j+1]= temp;
                noSwap = false;
            }
        }
        if(noSwap){break;}
    }
    return arr;
} 
```

冒泡排序的时间复杂度是 O(n^2)，这使得它与合并排序和其他排序算法相比不太受欢迎。