# 简单基数排序

> 原文：<https://dev.to/philipsterling/simple-radix-sort-4pbp>

整理数据需要时间。

为了更好地理解基数排序，我建议你要么阅读我以前关于计数排序的帖子，要么阅读任何关于计数排序如何工作的文档，因为 LSD 基数排序是建立在计数排序基础上的。

在大多数基数排序中，首先对 LSD(最低有效位)进行排序，然后遍历所有数字，直到到达(MSD)最高有效位。

基数(或基数)的定义是在一个数字系统中代表数字的唯一数字的个数，包括 0。

二进制的基数是 2。十六进制的基数是 16。
你日常使用的十进制或十进制典型系统的基数是 10。

LSD 基数排序可以在有两个输入的 JavaScript 中运行。第一个用于要排序的数组，第二个用于数组的单个元素中的最大位数。

```
function LSDRadixSort(array, radix) {

  //sets the radix to base 10 by default unless otherwise specified.
  radix = radix || 10;

  // Determine minimum and maximum values in the array to be sorted.
  var minValue = array[0];
  var maxValue = array[0];
  for (var i = 1; i < array.length; i++) {
    if (array[i] < minValue) {
      minValue = array[i];
    } else if (array[i] > maxValue) {
      maxValue = array[i];
    }
  }

  var exponent = 1;
  // This while loop runs a counting sort for each index in an array from
  //least significant to most significant
  while ((maxValue - minValue) / exponent >= 1) {

    array = countingSortByDigit(array, radix, exponent, minValue);

    exponent *= radix;
  }

  return array;
}

function countingSortByDigit(array, radix, exponent, minValue) {
  var i;
  var bucketIndex;
  var buckets = new Array(radix);
  var output = new Array(array.length);

  // Initialize the bucket array
  for (i = 0; i < radix; i++) {
    buckets[i] = 0;
  }

  // Count the frequencies
  for (i = 0; i < array.length; i++) {
    bucketIndex = Math.floor(((array[i] - minValue) / exponent) % radix);
    buckets[bucketIndex]++;
  }

  // Compute cumulative amounts
  for (i = 1; i < radix; i++) {
    buckets[i] += buckets[i - 1];
  }

  // Move items in the array to be sorted
  for (i = array.length - 1; i >= 0; i--) {
    bucketIndex = Math.floor(((array[i] - minValue) / exponent) % radix);
    output[--buckets[bucketIndex]] = array[i];
  }

  // set output to the original array
  for (i = 0; i < array.length; i++) {
    array[i] = output[i];
  }

  return array;
} 
```

基数看起来很复杂，但是如果我们把它分解成一个简单的例子，它会变得更容易理解。
假设有一个基数为 10 的数字数组，我们要对其进行排序

```
[99, 88, 2 ,33 ,79, 44, 10] 
```

当我们在基数排序的 while 循环中时，我们将首先按照位
进行排序

```
[9, 8, 2, 3, 7, 4, 0]
//=> 
[0, 2, 3, 4, 8, 9, 9]
//which makes the actual array
//=> 
[10, 2, 33, 44, 88, 99, 79]
//then we sort that array by the 10's place
//=> 
[0, 1, 3, 4, 7, 8, 9]
//which makes the actual array
//=> 
[02, 10, 33, 44, 79, 88, 99] 
```

无论基数是 2、10 还是 16，最长的数总共有多少位，这都适用。