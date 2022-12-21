# Javascript 内置的 sort()方法

> 原文：<https://dev.to/yongliang24/javascript-built-in-sort-method-18ia>

## JS 内置的 sort()方法是如何工作的？

sort()方法将就地对数组进行排序，并返回一个排序后的数组。默认的排序顺序建立在将元素转换为字符串，然后比较它们的 UTF-16 代码单元值序列的基础上。

sort()方法接受 compareFunction(a，b)作为参数。为了正确排序，我们需要定义 compareFunction 做什么。

### 例子

```
var months = ['March', 'Jan', 'Feb', 'Dec'];
months.sort();
console.log(months);
// expected output: Array ["Dec", "Feb", "Jan", "March"]

var array1 = [1, 30, 4, 21, 100000];
array1.sort();
console.log(array1);
// expected output: Array [1, 100000, 21, 30, 4]

//the results are not sorted correctly. 
```

默认的 sort()将元素作为字符串进行比较，所以 21 大于 10000，因为 2 大于 1。为了解决这个问题，我们可以使用 compareFunction。

### 比较函数

这些是我从 Mozilla 的文档中找到的规则:

1.  如果 compareFunction(a，b)小于 0，则将 a 排序到比 b 低的索引(即 a 排在第一位)。

2.  如果 compareFunction(a，b)返回 0，则 a 和 b 相对于彼此保持不变，但相对于所有不同的元素进行排序。注意:ECMAscript 标准并不保证这种行为，因此，并不是所有的浏览器(例如，至少可以追溯到 2003 年的 Mozilla 版本)都尊重这一点。

3.  如果 compareFunction(a，b)大于 0，则将 b 排序到比 a 低的索引(即 b 排在第一位)。

4.  当给定特定的一对元素 a 和 b 作为其两个参数时，compareFunction(a，b)必须始终返回相同的值。如果返回不一致的结果，则排序顺序是未定义的。

```
var numbers = [4, 2, 5, 1, 3];
numbers.sort(function(a, b) {
   return a - b;
 });
 console.log(numbers);

// [1, 2, 3, 4, 5]

//to sort by the length of strings

function lengthCompare(a,b){
    return a.length - b.length 
}

console.log(["grapefruitPlusKiwi", "apple", "pie", "orange", "pineapple", "banananananana"].sort(lengthCompare))

//["pie", "apple", "orange", "pineapple", "banananananana", "grapefruitPlusKiwi"] 
```

[来源于 mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)