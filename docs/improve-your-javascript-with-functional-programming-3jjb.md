# 用函数式编程改进您的 JavaScript

> 原文：<https://dev.to/nitinja/improve-your-javascript-with-functional-programming-3jjb>

如果你想以更好的方式编写你的 JavaScript 代码，最有效的技术之一就是函数式编程(FP)。这并不比语言本身难——不要让任何人告诉你别的。这是为初级程序员设计的。这个系列将有多个职位。

### 使用纯函数

纯函数是这样的函数

1.  除了提供的参数之外，不访问任何其他内容。
2.  不修改函数之外的任何内容。它可以返回基于参数的计算值。这意味着当提供相同的输入时，它们将总是返回相同的输出。

#### 纯函数的例子

```
const calculateArea = radius => 3.14 * radius * radius; 
```

### 使用`map()`代替`for/forEach`进行数组转换

(你可能已经在这么做了)

使用`for/forEach`处理并将一个数组转换成一个新数组

```
const nums = [1,2,3,4,5,6];
const doubles = [];
for(let i = 0; i < nums.length; i++){
  doubles.push(nums[i] * 2);
}
// nums.forEach(item => doubles.push(item * 2)); // foreach version
console.log(doubles) 
```

相反，您应该使用`map()`函数(它是 JavaScipt 中内置的)

```
const nums = [1,2,3,4,5,6];
const doubles = nums.map(item => item * 2);
console.log(doubles) 
```

#### 为什么用`map()`代替上面的方法？

*   代码几乎是人类可读的(“用函数 x 将这个数组映射到那个数组”)，很容易理解
*   它很简洁，使用更少的按键，因此潜在的错误更少

### 使用`reduce()`代替 for 循环进行加法计算

如果你想计算依赖于数组中每个值的东西，使用`reduce()`函数。例如，你需要将数组中的每个元素相加。

`for/forEach`方法:

```
const array1 = [1, 2, 3, 4];
let sum = 0;
for(let i=0; i< array1.length; i++){
  sum+=array1[i];
}
// array1.forEach(item => sum+= item); //forEach version

// 1 + 2 + 3 + 4
console.log(sum); 
```

```
const array1 = [1, 2, 3, 4];
const sum = array1.reduce((sum, current) => sum+current, 0);

// 1 + 2 + 3 + 4
console.log(sum); 
```

#### 为什么用`reduce()`代替上面的方法？

*   比 for 循环的样板文件少
*   一个普通的结构，读起来更清晰
*   可以和 map: `array1.map(i => i*2).reduce((sum, current) => sum+current)`等其他数组函数链接

### 使用 filter()方法进行数组过滤操作:

使用 for 循环过滤偶数数组:

```
const array1 = [1, 2, 3, 4];

const evenArray1 = [];
for(let i = 0; i<array1.length; i++){
  if(array1[i] % 2 === 0){
    evenArray1.push(array1[i]); //copy only even elements
  }
}
console.log(evenArray1); 
```

#### 用户`filter()`方法改为:

```
const array1 = [1, 2, 3, 4];

const evenArray1 = array1.filter(item => item % 2 === 0);
console.log(evenArray1); 
```

### [T3 用`every()`和`some()`代替手动搜索 for 循环](#use-raw-every-endraw-and-raw-some-endraw-insted-of-manual-search-with-for-loops)

检查数组中的所有项目是否满足特定标准(偶数)

```
const array1 = [2,4,8];

let isAllEven = true;
for(let i = 0; i<array1.length; i++){
  if(array1[i] % 2 !== 0){
    isAllEven = false;
    break;
  }
}
console.log(isAllEven); 
```

同样使用`every()`:

```
const array1 = [2,4,8, 3];

let isAllEven = array1.every(item => item % 2 === 0)
console.log(isAllEven); 
```

检查数组中是否至少有一项满足特定条件(偶数)

```
const array1 = [1, 3];

let isAtleastOneEven = false;
for(let i = 0; i<array1.length; i++){
  if(array1[i] % 2 === 0){
    isAtleastOneEven = true;
    break;
  }
}
console.log(isAtleastOneEven); 
```

同样使用`some()`:

```
const array1 = [1, 2, 3];

let isAtleastOneEven =  array1.some(item => item % 2 ===0)
console.log(isAtleastOneEven); 
```

### 利用`bind()`使用部分函数从现有函数创建新函数

您可以从现有函数派生出新函数。例如，你有一个计算数字幂的幂函数。

```
const power = (p, num) => num ** p; 
```

您可以创建一个使用现有函数的新函数`square()`和`cube()`

```
const square = power.bind(null, 2);
const cube = power.bind(null, 3);

console.log(square(5))  // 25
console.log(cube(5))  // 125 
```

**关于性能的说明:**注意，for 循环总是比`map()`和其他类似函数快。对于少数项目，比如几百个项目，两者的性能相似，但是对于大量项目，您可能需要考虑循环。

在下一篇文章中，我们将讨论一些高级的函数式编程概念。谢谢！