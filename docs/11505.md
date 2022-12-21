# 映射，过滤，减少和其他数组迭代器你必须知道成为一个算法向导

> 原文：<https://dev.to/uptheirons78/map-filter-reduce-and-others-arrays-iterators-you-must-know-to-become-an-algorithms-wizard-4209>

在本文中，我们将仔细研究一些数组迭代器，如 ***map*** ， ***filter*** ， ***reduce*** 或其他方法，它们使用回调使数组更加灵活，并帮助您解决 javascript 中的问题和算法。
学习这些高阶函数是朝着能够编写干净的函数式代码迈出的重要一步，并且为功能性和反应式编程的强大技术打开了大门。

1)用 ***forEach()*** 循环数组；
循环 的 ***的一个很好的替代方法肯定是 ***forEach*** 方法。它遍历数组，并使用每个值作为参数调用回调函数。回调函数接受 3 个不同的参数:数组中的值、当前索引和调用回调的数组。还有第四个值，它是在执行回调时用作 ***this*** 的值(如果没有提供，它将是 **undefined** )。
注意: ***forEach()*** 不会对调用它的数组进行变异*** 

```
 const cities = ['ROME', 'PARIS', 'LONDON', 'LOS ANGELES', 'VIENNA'];

cities.forEach((value, index, array) => {
  console.log(`${index + 1}  ${value}`); //output: 1 ROME, 2 PARIS, 3 LONDON, 4 LOS ANGELES, 5 VIENNA
});

//we can use it to invert the "cities" array...even though with reverse() would be better 😉
const invertedCities = [];
cities.forEach((value, index, array) => invertedCities.unshift(value));
console.log(invertedCities); //output: ["VIENNA", "LOS ANGELES", "LONDON", "PARIS", "ROME"] 
```

2)用***【map()***对原数组的每个元素调用一个函数的结果创建一个新数组；
非常类似于 ***forEach()*** 的方法。它遍历一个数组，并将回调函数作为参数，在原始数组的每一项上调用。不同的是，它返回一个**新数组**，用回调函数的返回值替换每个值。

```
 //Let's create a new array with all our numbers squared
const numbers = [1, 2, 3, 4, 5];

const squaredNumbers = numbers.map(number => number * number);
console.log(squaredNumbers); //output: [1, 4, 9, 16, 25]

//We all know which is the most beautiful city in the World... 😉
const cities = ['ROME', 'PARIS', 'LONDON', 'LOS ANGELES', 'VIENNA'];
const bestCity = cities.map(city => (city === 'ROME' ? city : 'ROME'));
console.log(bestCity); //output: ["ROME", "ROME", "ROME", "ROME", "ROME"]

//Let's create an array of HTML tag
const html = cities.map(city => `<li>${city}</li>`);
console.log(html); //output: ["<li>ROME</li>", "<li>PARIS</li>", "<li>LONDON</li>", "<li>LOS ANGELES</li>", "<li>VIENNA</li>"]

//Transform an array of strings in an array of objects
const metalBands = ['IRON MAIDEN', 'SLAYER', 'JUDAS PRIEST'];
const collection = metalBands.map((band, index) => {
  let obj = {}; //create an empty object at any call of the loop
  obj.id = index; //create a key called "id" and set it equal to our index parameter
  obj.band = band; //create a key called "band" and set it equal to our band parameter
  return obj; //return an object at any call with key/value pairs like this: {id: 'index', band: 'band-name'}
});
console.log(collection); //output: [{id: 0, band: "IRON MAIDEN"},{id: 1, band: "SLAYER"}, {id: 2, band: "JUDAS PRIEST"}] 
```

3)用 ***filter()*** 过滤一个数组的值；
它返回一个新数组，该数组只包含原始数组中传递给回调函数时返回 true 的元素。

```
 const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

//Return an array of even values from numbers
const evens = numbers.filter(num => num % 2 === 0);
console.log(evens); //output: [2, 4, 6, 8, 10]

//Return an array of odd values from numbers
const odds = numbers.filter(num => num % 2 !== 0);
console.log(odds); //output: [1, 3, 5, 7, 9]

//All the roads lead to Rome 🚋 ...
const cities = ['ROME', 'PARIS', 'LONDON', 'LOS ANGELES', 'VIENNA'];
const rome = cities.filter(city => city == 'ROME');
console.log(rome); //output: ["ROME"]

//You can chain together this methods 🚀🚀🚀
//Example: let's square all even numbers
const squaredEvens = numbers.filter(num => num % 2 === 0).map(num => num * num);
console.log(squaredEvens); //output: [4, 16, 36, 64, 100]; 
```

4)用***【reduce()***累计组合每个结果返回单个值；
这是真正的奇迹发生的地方！***【reduce()***是函数式编程的“基石”,如果你得到了它，你就走上了成为 JS 向导的正确道路🤘😉🤘
这是另一种方法，它遍历数组中的每一项，但这一次它累积地组合每个结果，只返回一个值。
回调函数用于描述如何将数组中的每个值与运行总数相结合。这通常用于计算存储在数组中的数据。
reducer 函数有四个参数:累加器、当前值、当前索引和源数组。它还接受一个初始值，作为回调的第一次调用的第一个参数。

```
 const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
//Let's start with a basic example
//Sum all the numbers in an array of integers
const sum = numbers.reduce((accumulator, currentValue, currentIndex, array) => {
  //Look we set the Initial Value to 0, because it is a sum so the count starts at 0
  //So our Accumulator is 0 at first call and we sum it with the Current Value that is 1 at first call...
  //the new Accumulator will be 0 + 1 = 1 ...at any call the Current Value will be added to it
  //till the end of the array
  return accumulator + currentValue;
}, 0);
console.log(sum); // output: 55

//Same example setting the Initial Value to 1 instead of 0 will return ... 56
const anotherSum = numbers.reduce((accumulator, currentValue, currentIndex, array) => {
  return accumulator + currentValue;
}, 1);
console.log(anotherSum); // output: 56

//Sum from an array of objects
const integers = [{ x: 1 }, { x: 2 }, { x: 3 }];
const anotherSumAgain = integers.reduce((acc, val, idx, array) => {
  return acc + val.x;
}, 0);
console.log(anotherSumAgain); // output: 6

//Count vowels in a string (even though it's easier with regex 😉)
const maryPoppins = 'supercalifragilisticexpialidocious';
const onlyVowels = maryPoppins.replace(/[^aeiou]/gi, ''); //'ueaiaiiieiaioiou'
const arrOfVowels = [...onlyVowels]; //["u", "e", "a", "i", "a", "i", "i", "i", "e", "i", "a", "i", "o", "i", "o", "u"]
const countVowels = arrOfVowels.reduce((acc, val) => {
  acc.hasOwnProperty(val) ? (acc[val] += 1) : (acc[val] = 0);
  return acc;
}, {});
console.log(countVowels); // output: {u: 1, e: 1, a: 2, i: 6, o: 1}

//Flatten an array of arrays
//Hey! I know ES2019 gave us flat and flatMap methods, but we MUST learn reduce() now 😉
const someData = [[1, 2, 3], [4, 5, 6], [7, 8, 9]];
const flatten = someData.reduce((acc, val) => {
  //set the initial value to an empty array
  return acc.concat(val);
}, []);
console.log(flatten); // output: [ 1, 2, 3, 4, 5, 6, 7, 8, 9 ]

//Sum all countries population except China
const population = [
  {
    country: 'China',
    pop: 1409517397
  },
  {
    country: 'India',
    pop: 1339180127
  },
  {
    country: 'USA',
    pop: 324459463
  },
  {
    country: 'Indonesia',
    pop: 263991379
  }
];
const sumPopulationButNotChina = population.reduce((acc, val) => {
  // we use the Ternary Operator as a "filter"
  //if val.country is not equal to "China" we add the value to the accumulator
  //if it is equal to "China" we add nothing and simply return the accumulator
  return val.country !== 'China' ? acc + val.pop : acc;
}, 0);

console.log(sumPopulationButNotChina); // output: 1927630969 
```

类似于 ***reduce()*** 是 ***reduceRight*** 。它对累加器和数组中的每个值应用一个函数，但从右到左。我不打算在这里介绍它，因为它只是重复了已经介绍过的***【reduce()***。

5)每隔() 用 ***检查所有数组项是否通过测试；
测试中使用的回调函数有 3 个参数:当前值、索引和数组。返回值是布尔值。如果回调函数为数组中的任何元素返回 True 值，则为 true。否则为假。*** 

```
 //Check if all values are more than zero
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const moreThanZero = numbers.every((val, index, array) => val > 0);
console.log(moreThanZero); //true

const numbersAgain = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const moreThanZeroAgain = numbersAgain.every((val, index, array) => val > 0);
console.log(moreThanZeroAgain); //false

//Check if there are more than 1000000 people in all the countries
const population = [
  {
    country: 'China',
    pop: 1409517397
  },
  {
    country: 'India',
    pop: 1339180127
  },
  {
    country: 'USA',
    pop: 324459463
  },
  {
    country: 'Indonesia',
    pop: 263991379
  }
];

const check = population.every(val => val.pop > 1000000);
console.log(check); //true 
```

6)用 ***some()*** 检查一些数组项是否通过测试；
测试中使用的回调函数有 3 个参数:当前值、索引和数组。返回值是布尔值。如果回调函数为数组中至少一个元素返回 True 值，则为 true。否则为假。

```
 //Check if a value is more than zero in the array
const numbers = [-1, -2, 0, 10];
const moreThanZero = numbers.some((val, index, array) => val > 0);
console.log(moreThanZero); //true

const numbersAgain = [0, -1, -2];
const moreThanZeroAgain = numbersAgain.some((val, index, array) => val > 0);
console.log(moreThanZeroAgain); //false

//Check if there is at least a country with less than 1000000 people
const population = [
  {
    country: 'China',
    pop: 1409517397
  },
  {
    country: 'India',
    pop: 1339180127
  },
  {
    country: 'USA',
    pop: 324459463
  },
  {
    country: 'Indonesia',
    pop: 263991379
  }
];

const check = population.some(val => val.pop < 1000000);
console.log(check); //false 
```

7)用 ***find()*** 找到第一个通过测试的数组项；
测试中使用的回调函数有 3 个参数:当前值、索引和数组。如果一个项目至少通过了测试，返回值就是项目本身。否则返回 ***未定义*** 。

```
 //Check if there is a value more than 7
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
const moreThanSeven = numbers.find((val, index, array) => val > 7);
console.log(moreThanSeven); //8
//Check if there is a value more than 42
const moreThanFortyTwo = numbers.find((val, index, array) => val > 42);
console.log(moreThanFortyTwo); //undefined

//Check if there is a country with more than 100000000 people
const population = [
  {
    country: 'China',
    pop: 1409517397
  },
  {
    country: 'India',
    pop: 1339180127
  },
  {
    country: 'USA',
    pop: 324459463
  },
  {
    country: 'Indonesia',
    pop: 263991379
  }
];

const check = population.find(val => val.pop > 100000000);
console.log(check); //{ country: 'China', pop: 1409517397 } 
```

这并不是所有 javascript 数组迭代器的详尽列表，而是我发现在解决问题和算法时最重要的列表。为了更好地解决 JS 和问题，我建议多“玩”玩所有这些方法，并订阅[免费代码营](https://www.freecodecamp.org/)或[代码战](https://www.codewars.com/)网站，在那里你可以找到许多算法来使用，并重温你的 javascript 知识。
在 [Codewars](https://www.codewars.com/) 上，你可以寻找关于“数组”的 7kyu 或 6kyu 算法，并用它们进行训练。这将是有趣和有用的！

如果你对这类文章感兴趣，可以看看这些:
[成为算法向导必须知道的 Javascript 字符串方法](https://dev.to/uptheirons78/javascript-string-methods-you-must-know-to-become-an-algorithms-wizard-c84)
[成为算法向导必须知道的 Javascript 数组方法](https://dev.to/uptheirons78/javascript-arrays-methods-you-must-know-to-become-an-algorithms-wizard-2ec7)

我将根据大家的反应和评论，用新的信息和一些关于数组的算法来更新这篇文章。
在[推特](https://twitter.com/UpTheIrons1978)上关注我

***码长而亨通***