# javascript 中如何使用 Reduce？

> 原文：<https://dev.to/vantonio_/how-to-use-reduce-in-javascript-3pbm>

reduce()方法用于对数组中的每个元素应用一个函数，以将数组缩减为单个值。

#### 举例:

```
 const ages = [33,12,20,16,5,54,21,44,23,55,45,90];

let ageSum = 0;

for(let i=0; i < ages.length; i++){
     ageSum += ages[i];
  }
//result
console.log(ageSum);
// 418 
```

方法:

```
const ageSum = ages.reduce((total,age) => total + age ,0);
//result
console.log(ageSum);
// 418 
```