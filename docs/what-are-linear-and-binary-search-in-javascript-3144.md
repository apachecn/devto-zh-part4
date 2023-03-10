# Javascript 中的线性和二分搜索法是什么？

> 原文：<https://dev.to/yongliang24/what-are-linear-and-binary-search-in-javascript-3144>

### 线性搜索&二分搜索法如何工作？

线性搜索一次一个地检查数据集合中的每个项目，以便找到搜索项目。例如，当我们在一个数组中搜索一个项目时，更糟糕的情况是我们必须遍历整个数组，这给了我们一个 O(n)的性能。Javascript 中的线性搜索示例如下:

```
//Linear Search 
let arr = [1,4,3,9,5,2,7,8]

//this function accepts an array and an element to be searched
function linearSearchArray(arr, targetElement){

 for(let eachItemIndex in arr){
   if(arr[eachItemIndex] === targetElement){
     return eachItemIndex;
  }
 }
 return -1;
}

linearSearchArray(arr, 9) //will return 3 because 9 has an index of 3 in the array. 
```

### 二分搜索法

二分搜索法稍微复杂一点，只适用于有序数组。简单地说，二分搜索法就是我们首先查看一个排序数组中的中间元素，然后将其与我们要搜索的项进行比较，如果中间元素大于要搜索的项，那么我们知道数组中有一半的元素大于我们要搜索的项，因此我们可以丢弃数组的这一半，只搜索另一半。我们继续这个操作，直到我们找到我们正在寻找的元素。

在更糟糕的情况下，当我们缩小数组时，最终只剩下一个元素需要检查，如果不是匹配的元素，那么我们要找的元素就不在数组中。因为我们每次检查都会丢弃数组的一半，所以每次进行比较时，数组的大小都会除以 2。因此，二分搜索法的时间复杂度是 O(log n)。它比线性搜索平均要快得多。这里有一种用 Javascript 实现二分搜索法的方法:

```
//Binary Search 

let arr = [1,4,3,9,5,2,7,8]

//this function takes an array and an element we want to find arguments

function binarySearchArray(arr, target){

//define the positions we will work with
  let startPosition =0, endPosition = arr.length -1, midPosition = 0;

//use a while loop to keep dividing and looking for the target
 while(startPosition <= endPosition){

//define and update the middle position
       midPostion = Math.floor((startPosition + endPosition) /2);

//break the loop when target is equal to the middle element
//otherwise we update either the start or the end position so that half //of the array will be dropped.

       if(target == arr[midPostion]){
           console.log("target found at index:", midPostion);
           return true;
         }
        else if(arr[midPostion] < target){

            startPosition = midPostion +1;
            //console.log("target not yet found");
        }
        else{
            endPosition = midPostion -1;
            //console.log("target not yet found");
        }

    }

    return false;

}

//call the function
binarySearchArray(arr, 9)
//this returns target found at index: 3 true 
```