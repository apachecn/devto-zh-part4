# 基于使用 javaScript 的唯一值合并世界的 javaScript 中对象的联合

> 原文：<https://dev.to/paramharrison/union-of-objects-in-javascript-based-on-unique-value-merging-worlds-using-javascript-40g7>

在本教程中，我们将看到

*   两个或多个对象的合并
*   将对象合并到数组中
*   合并两个数组
*   基于唯一键的对象联合
*   基于唯一键值的数组中对象的并集

### 使用对象析构合并两个或多个对象

使用 es6 对象销毁，很容易合并两个对象。

```
const firstObj = { name: 'Vennila' };
const secondObj = { age: 26 };
// object destructuring to merge two objects
const mergedObj = { ...firstObj, ...secondObj };
console.log(mergedObj);
// result - { name: 'Vennila', age: 26 } 
```

如果两个对象有相同的键值，那么它只是合并最后一个对象的值，并且只有一个键值。

```
const firstObjWithSameKey = { name: 'Vennila', age: 26 };
const secondObjWithSameKey = { name: 'Param', city: 'Tallinn' };

const mergedObjWithSameKey = {
  ...firstObjWithSameKey,
  ...secondObjWithSameKey,
};
console.log(mergedObjWithSameKey);
// result - { name: 'Param', age: 26, city: 'Tallinn' } 
```

### 将对象合并成一个对象数组

到目前为止，我们已经看到了基本的例子，现在让我们更深入一步。如何将对象合并到对象数组中？它使用了相同的析构技术，但是使用了数组。

```
const arrayOfObj = [
  {
    name: 'Ironman',
  },
  {
    name: 'Spiderman',
  },
];
const objToMergeInArray = {
  name: 'Captain America',
  from: 'Marvel Universe',
};
const mergedArrayOfObj = [...arrayOfObj, objToMergeInArray];
console.log(mergedArrayOfObj);
/* result
[ 
  { name: 'Ironman' },
  { name: 'Spiderman' },
  { name: 'Captain America', from: 'Marvel Universe' } 
] 
*/ 
```

### 合并两个数组

合并两个不同的对象数组同上，唯一的不同是，你需要析构两个数组。

```
[...arrayOfFirstObj, ...arrayOfSecondObj]; 
```

### 基于键的两个对象的并集

我们已经讨论过了，就这么简单。如果值自动匹配，对象将采用最后一个值并合并对象。参见`mergedObjWithSameKey`的示例。它合并了 name 属性并显示最后一个对象的名称值。

### 基于唯一键值的数组中对象的并集

这就是有趣的地方。举这个例子

```
const firstArr = [
  {
    id: 'marvel',
    winner: 'Captain America',
  },
  {
    id: 'DC',
    winner: 'Aquaman',
  },
];

const secondArr = [
  {
    id: 'freelancer',
    winner: 'Wonder women',
  },
  {
    id: 'marvel',
    winner: 'Param', //why not
    strenths: ['fly', 'fight', 'speed'],
  },
]; 
```

如果你检查两个数组，它们都有键值`id: marvel`。如何合并这两个对象，并显示这样的结果

```
// expected result

const mergedArray = [
  {
    id: 'marvel',
    winner: 'Param', //why not
    strenths: ['fly', 'fight', 'speed'],
  },
  {
    id: 'DC',
    winner: 'Aquaman',
  },
  {
    id: 'freelancer',
    winner: 'Wonder women',
  },
]; 
```

让我们使用现代的 javascript 技术来实现它。

在这样做之前，我们需要理解一种叫做`Set`的 javascript 对象类型。

> Set 是 es6 中引入的新对象类型，用于创建不同值的集合。

我们将使用 Set 来合并我们的数组，让我们看看它是如何工作的。

> Set 是 es6 中引入的新对象类型，用于创建不同值的集合。

#### 设定的简单例子

```
// Simple example for Set
const a = [44, 33, 22];
const b = [33, 22, 11];
const AandB = [...a, ...b];
console.log(AandB); // [44, 33, 22, 33, 22, 11]
const distinctValuesInAandBSet = new Set(AandB);
console.log(distinctValuesInAandBSet); // Set { 44, 33, 22, 11 }
const setToArray = [...distinctValuesInAandBSet];
console.log(setToArray); // [44, 33, 22, 11] 
```

这是一个非常简单的例子，我们只是合并了一个一维数组，并使用`Set`创建了一组不同的值。

让我们对我们的对象数组使用相同的原理。

```
const mergedArray = [...secondArr, ...firstArr];
// mergedArray have duplicates, lets remove the duplicates using Set
let set = new Set();
let unionArray = mergedArray.filter(item => {
  if (!set.has(item.id)) {
    set.add(item.id);
    return true;
  }
  return false;
}, set);
console.log(unionArray);
/*
[ { id: 'freelancer', winner: 'Wonder women' },
  { id: 'marvel',
    winner: 'Param',
    strenths: ['fly', 'fight', 'speed'] },
  { id: 'DC', winner: 'Aquaman' } ]
*/ 
```

在`mergedArray`中，第二个数组首先被析构，因为如果`id`值已经存在于第二个数组中，我们使用 filter 从一个数组中过滤出值。

我听到了，这不是纯粹的联盟所有的内容。它不会深度合并两个数组中的所有键值。

即使是`id`的`unionBy`值。这个例子并不适用于所有的用例。

例如，如果我们需要仅通过`id`值来合并两个不同的对象。这个例子将只返回第二个数组对象的结果，但是它不会从第一个数组对象
中获取属性

```
const firstArray = [
  {
    id: 'marvel',
    winner: 'Captain America',
    weakness: ['emotion'],
  },
];

const secondArray = [
  {
    id: 'marvel',
    name: 'Param',
  },
]; 
```

真正的`unionBy`值应该返回

```
/* expected result
  [
    {
      id: "marvel",
      winner: "Captain America",
      weakness: ["emotion"],
      name: "Param",
    }
  ]
  */

/* Our actual result
  [
    {
      id: "marvel",
      name: "Param",
    }
  ]
  */ 
```

我们将在下一课中看到如何处理`unionBy`键值，以及如何深度合并两个数组中的对象。

对于本文，我们已经了解了如何合并对象以及如何基于 javascript 中的键值联合对象数组。

但是这种方法的缺点是，

*   它从第一个数组中过滤出唯一的键值
*   它还改变了数组内部的排序，不完全与相同的排序合并

敬请期待，希望你喜欢这个教程😋