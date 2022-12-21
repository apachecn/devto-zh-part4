# 在 Javascript 中删除数组中的重复项

> 原文：<https://dev.to/yongliang24/remove-duplicates-from-arrays-in-javascript-354m>

### 总结

在 JS 中有几种方法可以从数组中删除重复项。本文将演示一个使用 loop 和 indexOf()的方法，以及另一个使用 Array.from()和 Set()的方法。

### 方法 1

1.  创建一个新的空数组
2.  遍历原始数组
3.  使用 indexOf 检查新数组是否已经具有相同的值
4.  如果在新数组中找不到该值，则推送它。

```
//Objective: remove the duplicate values in array1
let array1 = [1,2,3,3,4,2,5,7,1]

let array2 = []

for(let eachValue of array1){
    if(array2.indexOf(eachValue) === -1){
      array2.push(eachValue)
    }
}

console.log(array2) //returns [ 1, 2, 3, 4, 5, 7 ] 
```

请注意，如果没有找到值，array.indexOf 将返回-1。在这种情况下，我们将把该值推入我们创建的空数组中，因为它是一个唯一的值。

### 方法二

1.  Set 是一个 javascript 内置方法，允许存储唯一的值。
2.  由于 set 返回一个对象类型中唯一值的集合，我们需要将这个对象转换回一个数组。
3.  方法将把 Set()对象转换回数组。这将创建一个新的浅复制数组。

```
//Objective: remove the duplicate values in array1

let array1 = [1,2,3,3,4,2,5,7,1]

array1 = Array.from(new Set(array1))

console.log(array1) //returns [ 1, 2, 3, 4, 5, 7 ] 
```

我们还可以使用 array.filter()和 indexOf()只返回唯一的值。