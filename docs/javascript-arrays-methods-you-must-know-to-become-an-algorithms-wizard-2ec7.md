# 成为算法向导必须知道的 Javascript 数组方法

> 原文：<https://dev.to/uptheirons78/javascript-arrays-methods-you-must-know-to-become-an-algorithms-wizard-2ec7>

这一次我想谈谈 Javascript 中常用的解决问题和算法的基本数组方法。

在过去的 4 周里，我一直致力于解决 FreeCodeCamp 和 CodeWars 上的算法，发现我用了很多次这种方法。

如果你对字符串方法感兴趣，你可以看看我以前的帖子:
[成为算法向导必须知道的 Javascript 字符串方法](https://dev.to/uptheirons78/javascript-string-methods-you-must-know-to-become-an-algorithms-wizard-c84)

在谈论数组方法**之前首先要记住**数组索引从 **0** 开始！

1)用 ***获取任意数组的长度。长度*** 。

```
const avengers = ['Captain America', 'Iron Man', 'Thor', 'Hulk', 'Hawkeye', 'Black Widow'];
console.log(avengers.length); //6
//This is a mutable property...you can change it "manually"
avengers.length = 8;
console.log(avengers); //["Captain America", "Iron Man", "Thor", "Hulk", "Hawkeye", "Black Widow", empty × 2]
//If you try to retrieve one of the two elements added...it will give you "undefined"
console.log(avengers[7]); //undefined 
```

2)用 ***pop()*** 删除数组的最后一项。它改变数组的长度并返回移除的元素。

```
const avengers = ['Captain America', 'Iron Man', 'Thor', 'Hulk', 'Hawkeye', 'Black Widow'];
console.log(avengers.pop()); //Black Widow
console.log(avengers); //[ 'Captain America', 'Iron Man', 'Thor', 'Hulk', 'Hawkeye' ]
console.log(avengers.length); //5

//Attention: If you call pop() on an empty array, it returns undefined !
const emptyArray = [];
console.log(emptyArray.pop()); //undefined 
```

3)用 ***shift()*** 删除数组的第一项。它改变数组的长度并返回移除的项。

```
const avengers = ['Captain America', 'Iron Man', 'Thor', 'Hulk', 'Hawkeye', 'Black Widow'];
console.log(avengers.shift()); //Captain America
console.log(avengers); //['Iron Man', 'Thor', 'Hulk', 'Hawkeye', 'Black Widow' ]
console.log(avengers.length); //5

//Attention: If you call shift() on an empty array, it returns undefined !
const emptyArray = [];
console.log(emptyArray.shift()); //undefined 
```

4)用 ***【推()*** 在数组末尾添加一个或多个项。它返回并更改数组的长度。

```
const avengers = ['Captain America', 'Iron Man', 'Thor', 'Hulk', 'Hawkeye', 'Black Widow'];
console.log(avengers.push('DeadPool')); // 7
console.log(avengers); //['Captain America', 'Iron Man', 'Thor', 'Hulk', 'Hawkeye', 'Black Widow', 'DeadPool']

const animals = ['snake', 'bear', 'hawk'];
console.log(animals.push('monkey', 'cat', 'dog')); // 6
console.log(animals); //[ 'snake', 'bear', 'hawk', 'monkey', 'cat', 'dog' ] 
```

5)用***【un shift()***将一个或多个项目添加到数组的开头。它返回并更改数组的长度。

```
const avengers = ['Captain America', 'Iron Man', 'Thor', 'Hulk', 'Hawkeye', 'Black Widow'];
console.log(avengers.unshift('DeadPool')); // 7
console.log(avengers); //['DeadPool', 'Captain America', 'Iron Man', 'Thor', 'Hulk', 'Hawkeye', 'Black Widow']

const animals = ['snake', 'bear', 'hawk'];
console.log(animals.unshift('monkey', 'cat', 'dog')); // 6
console.log(animals); //['monkey', 'cat', 'dog', 'snake', 'bear', 'hawk'] 
```

6)用 ***concat()*** 将一个数组与一个或多个数组合并。它不会改变原来的数组，而是创建一个新的数组。使用 ES6 ***spred 运算符*** 可以得到相同的结果。它由三个点组成**...**放在你想要展开的数组前面，具有展开每个数组中所有值的效果，这允许它们被放入一个新的数组中。

```
const firstArr = [0, 1, 2, 3, 4, 5];
const secondArr = [6, 7, 8, 9, 10];

console.log(firstArr.concat(secondArr)); //[ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]
console.log(secondArr.concat(firstArr)); //[ 6, 7, 8, 9, 10, 0, 1, 2, 3, 4, 5 ]
console.log(secondArr.concat(firstArr, secondArr)); //[ 6, 7, 8, 9, 10, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]

//ES6 SPREAD OPERATOR
console.log([...firstArr, ...secondArr]); //[ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]
console.log([...secondArr, ...firstArr]); //[ 6, 7, 8, 9, 10, 0, 1, 2, 3, 4, 5 ]
console.log([...secondArr, ...firstArr, ...secondArr]); //[ 6, 7, 8, 9, 10, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ] 
```

7)用 ***join()*** 将数组变成字符串。该字符串包含数组中用逗号分隔的所有项目。注意:您可以选择逗号以外的分隔符，方法是将它放在括号内。

```
const avengers = ['Captain America', 'Iron Man', 'Thor', 'Hulk', 'Hawkeye', 'Black Widow'];
console.log(avengers.join()); //Captain America,Iron Man,Thor,Hulk,Hawkeye,Black Widow
console.log(avengers.join(' - ')); //Captain America - Iron Man - Thor - Hulk - Hawkeye - Black Widow
console.log(avengers.join(', ')); //Captain America, Iron Man, Thor, Hulk, Hawkeye, Black Widow
console.log(avengers.join(' & ')); //Captain America & Iron Man & Thor & Hulk & Hawkeye & Black Widow 
```

8)用 ***切片()*** 创建一个子阵列。它从一个位置开始到另一个位置结束，截取原始数组的一部分。这是一个非破坏性的操作！原始数组保持不变。

```
const avengers = ['Captain America', 'Iron Man', 'Thor', 'Hulk', 'Hawkeye', 'Black Widow'];
console.log(avengers.slice(2, 4)); //[ 'Thor', 'Hulk' ]
console.log(avengers.slice(0, 1)); //[ 'Captain America' ]
console.log(avengers); //['Captain America', 'Iron Man', 'Thor', 'Hulk', 'Hawkeye', 'Black Widow']

//If begin parameter is not defined it slices from index 0
console.log(avengers.slice()); //['Captain America', 'Iron Man', 'Thor', 'Hulk', 'Hawkeye', 'Black Widow']
//if end parameter is not defined it slices from begin to array length
console.log(avengers.slice(2)); //[ 'Thor', 'Hulk', 'Hawkeye', 'Black Widow' ]

//Attention: A negative index can be used, indicating an offset from the end of the sequence. slice(-2) extracts the last two elements in the sequence.
console.log(avengers.slice(-2)); //[ 'Hawkeye', 'Black Widow' ]
console.log(avengers.slice(2, -2)); //[ 'Thor', 'Hulk' ] 
```

9)改变数组的内容，用*删除、替换或添加条目。它采用第一个参数(通常称为 **start** )等于从哪里开始改变数组的索引，第二个参数( **itemsToDelete** )等于从 **start** 移除的项目数。之后的每个值都被插入到数组中其他项被移除的位置。返回值是一个包含已删除元素的数组。这是一个破坏性的方法，它改变了原来的数组。* 

```
const animals = ['monkey', 'bull', 'python', 'cow'];
console.log(animals.splice(3, 1, 'gorilla')); //[ 'cow' ]
console.log(animals); //[ 'monkey', 'bull', 'python', 'gorilla' ]
console.log(animals.splice(2, 2)); //[ 'python', 'gorilla' ];
console.log(animals); //[ 'monkey', 'bull' ]

//The splice() method can be used to insert values into an array at a specific index without removing any items, by indicating that zero items are to be removed:
const bestMetalBands = ['Iron Maiden', 'Slayer', 'Pantera', 'Megadeth', 'Judas Priest'];
console.log(bestMetalBands.splice(2, 0, 'Saxon')); //[]
console.log(bestMetalBands); //['Iron Maiden', 'Slayer', 'Saxon', 'Pantera', 'Megadeth', 'Judas Priest']
console.log(bestMetalBands.splice(2, 2)); //[ 'Saxon', 'Pantera' ]
console.log(bestMetalBands); //[ 'Iron Maiden', 'Slayer', 'Megadeth', 'Judas Priest' ]

//If you want to remove a value completely, you can use the splice() method with a length of 1 and without specifying any values to add
console.log(bestMetalBands.splice(2, 1)); //[ 'Slayer' ]
console.log(bestMetalBands); //[ 'Iron Maiden', 'Megadeth', 'Judas Priest' ] 
```

10)用 ***反转一个数组的顺序()*** 。它永久地改变了数组的顺序。

```
const bestMetalBands = ['Iron Maiden', 'Slayer', 'Pantera', 'Megadeth', 'Judas Priest'];
console.log(bestMetalBands.reverse()); //[ 'Judas Priest', 'Megadeth', 'Pantera', 'Slayer', 'Iron Maiden' ]
console.log(bestMetalBands); //[ 'Judas Priest', 'Megadeth', 'Pantera', 'Slayer', 'Iron Maiden' ] 
```

11)用 ***sort()*** 对数组的顺序进行排序。它将所谓的**比较函数**作为参数。如果没有定义，默认的排序顺序是基于将元素转换成字符串，然后比较它们的 UTF-16 代码单元值序列。如果没有为整数数组的排序定义 compareFunction，这会产生问题，因为它们将按字母顺序排序！

```
const bestMetalBands = ['Iron Maiden', 'Slayer', 'Pantera', 'Megadeth', 'Judas Priest'];
console.log(bestMetalBands.sort()); //[ 'Iron Maiden', 'Judas Priest', 'Megadeth', 'Pantera', 'Slayer' ]

const numbers = [4, 2, 5, 1, 3];
//ascending sorting
console.log(numbers.sort((a, b) => a - b)); //[ 1, 2, 3, 4, 5 ]
//descending sorting
console.log(numbers.sort((a, b) => b - a)); //[ 5, 4, 3, 2, 1 ] 
```

12)用***【index of()***找到数组中第一个出现的值。如果该值在数组中，则返回该值第一次出现的索引。如果不是，它返回-1。它还有第二个可选参数，称为来自 Index 的**。开始搜索的索引。如果它是负数，它将作为从数组末尾的偏移量。** 

```
const bestMetalBands = ['Iron Maiden', 'Slayer', 'Pantera', 'Megadeth', 'Judas Priest'];
console.log(bestMetalBands.indexOf('Iron Maiden')); //0
console.log(bestMetalBands.indexOf('Megadeth')); //3
console.log(bestMetalBands.indexOf('Metallica')); //-1 ...they are no more a metal band ;)
console.log(bestMetalBands.indexOf('Iron Maiden', 2)); //-1 
```

13)用***【lastIndexOf()***找到数组中最后一个出现的值。如果该值在数组中，则返回该值最后一次出现的索引。如果不是，它返回-1。它还有第二个可选参数，称为来自 Index 的**。开始搜索的索引。如果它是负数，它将作为从数组末尾的偏移量。** 

```
const numbers = [4, 2, 5, 1, 3, 5, 3];
console.log(numbers.lastIndexOf(5)); //5
console.log(numbers.lastIndexOf(3)); //6
console.log(numbers.lastIndexOf(3, -3)); //4
console.log(numbers.lastIndexOf(42)); //-1 ...It is hard to find the Meaning of Life 
```

14)用 ***includes()*** 查找数组中是否包含某项。它将返回一个布尔值:真或假。这里也可以使用来自 Index 的**参数，如 indexOf()或 lastIndexOf()。** 

```
const bestMetalBands = ['Iron Maiden', 'Slayer', 'Pantera', 'Megadeth', 'Judas Priest'];
console.log(bestMetalBands.includes('Slayer')); //true
console.log(bestMetalBands.includes('Slayer', 3)); //false
console.log(bestMetalBands.includes('Metallica')); //false...Again? They are no more a metal band! 
```

15)从数组中取出值，用 ***析构*** 表示为单个值。这不是一个数组方法，而是一种特殊的语法，让我们可以将数组(和对象)解包到变量中。解决算法的时候非常得心应手。

```
const bestMetalBands = ['Iron Maiden', 'Slayer', 'Pantera'];
const [ironMaiden, slayer, pantera] = bestMetalBands;
console.log(ironMaiden); // Iron Maiden
console.log(slayer); //Slayer
console.log(pantera); //Pantera

//With Destructuring to swap the value of two variables it is now very easy
const arr = [42, 71];
let [x, y] = arr; //don't use const...
console.log(x); //42
console.log(y); //71
//...here comes the swap...
[x, y] = [y, x];
console.log(x); //71
console.log(y); //42 
```

我不想详尽列出所有 javascript 数组方法，而是列出我发现在解决问题和算法时最重要的方法。为了更好地解决 JS 和问题，我建议多“玩”玩所有这些方法，并订阅[免费代码营](https://www.freecodecamp.org/)或[代码战](https://www.codewars.com/)网站，在那里你可以找到许多算法来使用，并重温你的 javascript 知识。
在 [Codewars](https://www.codewars.com/) 上，你可以寻找关于“数组”的 7kyu 或 6kyu 算法，并用它们进行训练。这将是有趣和有用的！

我将根据大家的反应和评论，用新的信息和一些关于数组的算法来更新这篇文章。

***码长而亨通****