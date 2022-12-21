# Javascript 中 10 个最常用的数组方法

> 原文：<https://dev.to/ayeolakenny/10-most-used-array-methods-in-javascript-1l41>

在这篇博文中，我将带你了解最常用的数组 JavaScript 方法。这对你的 JavaScript 之旅很有帮助，我也会尽量让它简单明了，所以让我们开始吧；

## 1。"推送()；"

这在数组的末尾起作用，
这有助于将元素添加到数组的末尾。

> 请注意，字母“d”已经被推到(添加到)数组的末尾，
> ，它对数组进行了永久的更改。

```
let arr = ['a', 'b', 'c'];
console.log(arr.push('d'));//returns the new value of the array (4)
console.log(arr); //returns ['a', 'b', 'c', 'd'] 
```

Enter fullscreen mode Exit fullscreen mode

## 2。“pop()；”

像上面说的“推”法。
“pop”方法也适用于数组的结尾，
它有助于删除数组结尾的元素，并返回元素。

> 请注意，字母“d”已经从数组的末尾弹出(删除)，
> 并且对数组进行了永久的更改。

```
let arr =  ['a', 'b', 'c', 'd'];
console.log(arr.pop()); //returns 'd'
console.log(arr); //returns  ['a', 'b', 'c'] 
```

Enter fullscreen mode Exit fullscreen mode

## 3。" shift()；"

这在数组的开头起作用，
有助于从数组的开头移除元素，并返回元素。

> 请注意，字母“a”已经被移出了数组的末尾，
> ,它对数组进行了永久的更改。

```
let arr =  ['a', 'b', 'c', 'd'];
console.log(arr.shift()); //returns 'a'
console.log(arr); //returns  ['b', 'c', 'd'] 
```

Enter fullscreen mode Exit fullscreen mode

## 4。" un shift()；"

你猜对了，这是" shift()"方法的反义词，
它也在一个数组的开头工作，并帮助在一个数组的开头添加元素，
并返回数组的长度。

> 请注意，字母“a”已被取消移动(添加)到数组的开头，
> ，它对数组进行了永久更改。

```
let arr = ['b', 'c'];
console.log(arr.unshift('a'));//returns the new value of the array (4)
console.log(arr); //returns ['a', 'b', 'c'] 
```

Enter fullscreen mode Exit fullscreen mode

## 5。" concat()；"

这有助于将两个数组连接在一起。

> 请注意，第一个数组(arr)和第二个数组(arr2)已经连接在一起，
> ，而且“concat”方法没有对数组做任何更改。

```
let arr =  ['a', 'b', 'c', 'd']; //first array
let arr2 = [true, false, true]; //second array
console.log(arr.concat(arr2));//returns ["a", "b", "c", "d", true, false, true]
console.log(arr);//returns ['a', 'b', 'c', 'd']
console.log(arr2);//returns[true, false, true] 
```

Enter fullscreen mode Exit fullscreen mode

## 6。"联接()；"

这将连接数组中的所有元素以创建一个字符串，并且不会影响或修改数组。

> 注意,“join”方法接受一个元素，并在字符串之间输出它。

```
let arr =  ['a', 'b', 'c', 'd'];
console.log(arr.join(''));//returns 'abcd'
console.log(arr.join('-'));//returns 'a-b-c-d'
console.log(arr)//returns ['a', 'b', 'c', 'd'] 
```

Enter fullscreen mode Exit fullscreen mode

## 7。"反转()；"

这基本上是接受一个数组，并将其反转。

> 请注意，它对数组进行了永久的更改。

```
let arr =  ['a', 'b', 'c', 'd'];
console.log(arr.reverse());//returns ['d', 'c', 'b', 'a']
console.log(arr);//returns ['d', 'c', 'b', 'a'] 
```

Enter fullscreen mode Exit fullscreen mode

## 8。"排序()；"

这基本上有助于按顺序对数组进行排序，但是会导致数组的永久更改。

> 请注意，它将数字排在字母之前。

```
let arr = [5, 3, 'c', 'b', 'a', 4, 1, 2];
console.log(arr.sort();)//returns [1, 2, 3, 4, 5, 'a', 'b', 'c']
console.log(arr);//returns [1, 2, 3, 4, 5, 'a', 'b', 'c'] 
```

Enter fullscreen mode Exit fullscreen mode

## 9。" slice()；"

这基本上是以一个数组为目标，并返回目标数组，
，而原始数组保持不变。

> 请注意，该值从数组的第一个值((1)=b)返回，从数组的第二个值((2)=b)返回一个元素，
> 并且原始数组保持不变。

```
let arr =  ['a', 'b', 'c', 'd'];
console.log(arr.slice(1,2));//returns ['b'] 
```

Enter fullscreen mode Exit fullscreen mode

# 10 "拼接()；"

就像“slice()”一样方法，这也以一个数组为目标并返回目标数组，
此外，它还会导致数组的永久改变。

```
let arr = [1, 2, 3, 4, 5, 'a', 'b', 'c'];
console.log(arr.splice(5, 3));//returns ['a', 'b', 'c']
console.log(arr)//returns [1, 2, 3, 4, 5] 
```

Enter fullscreen mode Exit fullscreen mode

> 请注意，该值是从第一个值((5)='a ')和第二个值(3)返回的，第一个值表示元素的起始位置，第二个值表示要从数组中删除的元素的数量。

```
let arr = [1, 2, 3, 4, 5, 'a', 'b', 'c'];
console.log(arr.splice(5, 3, 'replaced'));//returns ['a', 'b', 'c']
console.log(arr)//returns [1, 2, 3, 4, 5, 'replaced'] 
```

Enter fullscreen mode Exit fullscreen mode

> 请注意，通过在第三个参数中添加元素，也可以用另一个元素替换拼接的元素。

* * *

这是我认为所有 javascript 程序员都必须学习的 10 个最常用的数组方法，如果你有任何要添加的，请在下面随意添加。