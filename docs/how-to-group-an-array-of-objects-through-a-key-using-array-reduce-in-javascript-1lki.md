# 如何在 javascript 中使用 Array reduce 通过一个键对对象数组进行分组

> 原文：<https://dev.to/paramharrison/how-to-group-an-array-of-objects-through-a-key-using-array-reduce-in-javascript-1lki>

我们经常使用`Array.map`、`Array.filter`和`Array.findIndex`。javascript Array 中还有其他几种方法。

在使用 array reduce 创建`groupBy`函数之前，我们将看到`Array.reduce`的基本示例。

### 如何还原作品

> 简单地说，它将数组简化为单个值。单个值可以是数字、字符串、对象或另一个数组。

让我们看一个常见的数组缩减的例子，

```
// Person array with name and Age
const person = [
  {
    name: 'Jim',
    color: 'blue',
    age: 22,
  },
  {
    name: 'Sam',
    color: 'blue',
    age: 33,
  },
  {
    name: 'Eddie',
    color: 'green',
    age: 77,
  },
];

// Add their sum of ages
const sumOfAges = person.reduce((sum, currentValue) => {
  return sum + currentValue.age;
}, 0);

console.log(sumOfAges); // 132 
```

*   `reduce`接受初始值，这里在我们的年龄总和中，我们通过`0`作为初始值
*   `reduce`对每个值进行迭代，并将每次的总和与它们之前的总和值相加。因此，我们返回每步的总和，这个总和与下一个年龄值相加。
*   最终的和值将在迭代结束时返回。

### 如何在 Javascript 中使用 reduce 进行分组

对于 person 数组，让我们使用颜色值对对象进行分组。在我们的对象中，有两个蓝色对象和一个绿色对象。

```
// Accepts the array and key
const groupBy = (array, key) => {
  // Return the end result
  return array.reduce((result, currentValue) => {
    // If an array already present for key, push it to the array. Else create an array and push the object
    (result[currentValue[key]] = result[currentValue[key]] || []).push(
      currentValue
    );
    // Return the current iteration `result` value, this will be taken as next iteration `result` value and accumulate
    return result;
  }, {}); // empty object is the initial value for result object
};

// Group by color as key to the person array
const personGroupedByColor = groupBy(person, 'color'); 
```

创建`groupBy`函数的步骤，

*   创建一个对象作为结果对象的初始值
*   在数组 reduce 中，为每个不同的键值创建一个数组，如果当前值中存在键值，则推送当前值

```
// this step of code can be restructured to multiple lines, see below for the multi line code
(result[currentValue[key]] = result[currentValue[key]] || []).push(
  currentValue
);

// This is how the above code in multiple line
if (!result[currentValue[key]]) {
  result[currentValue[key]] = [];
}
result[currentValue[key]].push(currentValue); 
```

分组后的最终结果将是这样的，

```
{
  "blue": [
    {
      "name": "Jim",
      "color": "blue",
      "age": 22
    },
    {
      "name": "Sam",
      "color": "blue",
      "age": 33
    }
  ],
  "green": [
    {
      "name": "Eddie",
      "color": "green",
      "age": 77
    }
  ]
} 
```

你可以在这里查看例子，

为了更好地理解 array reduce 如何处理每次迭代。

这就是在每次迭代中创建对象的方法

```
// Initial value for `result`
{}
// After first iteration
{ blue: [{ name: 'Jim', color: 'blue', age: 22 }] }
// After second iteration
{ blue:
   [ { name: 'Jim', color: 'blue', age: 22 },
     { name: 'Sam', color: 'blue', age: 33 } ] }
// After final iteration
{ blue:
   [ { name: 'Jim', color: 'blue', age: 22 },
     { name: 'Sam', color: 'blue', age: 33 } ],
  green: [{ name: 'Eddie', color: 'green', age: 77 }]} 
```

希望你学会了如何使用 array reduce 的技巧，以及如何使用 array reduce 创建`groupBy`函数😎