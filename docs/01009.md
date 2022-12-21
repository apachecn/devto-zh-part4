# 超越基础:对象 vs 贴图/WeakMap

> 原文：<https://dev.to/maftalion/beyond-the-basics-object-vs-map-1ccp>

**TLDR**

*   地图可以有任何值，因为它的关键，对象只能有字符串或符号
*   地图基于插入进行排序
*   地图比物体更容易清除
*   解构与贴图和对象的工作方式不同
*   与常规地图相比，WeakMap 提供了垃圾收集优势

ES6，也称为 ECMAScript 2015，引入了许多新功能。在本文中，我将讨论 ES6 中引入的一种新的数据结构——Map，以及在常规 javascript 对象上使用 Map 的优缺点。

*   我们现在上 ES10 了，我还应该关心 ES6 吗？
*   我不能继续使用常规对象吗？

以上两个问题的答案都是肯定的。虽然您仍然可以使用普通对象，但是知道何时以及如何使用映射会带来很多好处，可以使您的代码更加整洁。

## 什么是地图？

映射的结构非常类似于对象，因为它包含键/值对。区别在于它存储这些值的方式和位置。

如果要用数据初始化映射，可以传递一个数组，该数组包含或一个具有键/值对的 iterable 对象作为构造函数的参数。

```
const map = new Map(); // Create a empty Map

// Map initialized containing [ 'one', 1 ], [ 'two', 2 ]
const anotherMap = new Map([ [ 'one', 1 ], [ 'two', 2 ] ]); 
```

关于数据在地图中的存储方式，有两个最大的好处:

*   一个对象的键被设置为一个字符串或者一个符号，但是一个地图可以将*任何东西*设置为键...包括函数、对象和原语。
*   地图根据插入顺序进行排序

### 地图中的按键

让我们更深入地了解对象和贴图中的关键点之间的区别:

因为键是以字符串的形式存储的，所以非字符串也会被强制。这意味着当它们被设置为对象的键时，基本上“1”和 1 是相同的。

```
const obj = {};

// Integers will be casted to a string
obj[1] = 'one';
obj['1'] // one
obj[1]   // one

// Keys will not be casted when setting keys for a Map
const map = new Map();

map.set(1, 'one');           // a numeric key
map.set('1', 'another one'); // a string key
// map will contain two items: 1, 'one' and '1', 'another one' 
```

当我说任何东西都可以被设置为密钥时...我是说*任何事情* :

```
const person = {
  name: 'John'
}

const map = new Map();

// Set an object as a key
map.set(person, 30);
map.get(person); // 30

// You can even use a map as a key for a map!
const anotherMap = new Map();

anotherMap.set(map, true);
anotherMap.get(map); // true 
```

比较关键点时，地图使用`sameValueZero`算法。这非常类似于严格的等式`===`，但也考虑了`NaN === NaN`。

在对象中，你需要一次设置一个键/值，但是由于`Map.set()`返回映射，你可以链接调用:

```
const map = new Map();

map.set(1, 'one')
  .set(2, 'two')
  .set(3, 'three')
  .entries();
// 1 => "one", 2 => "two", 3 => "three" 
```

#### 删除属性

从对象和地图中删除属性非常相似，但是地图提供了一些额外的好处。

从对象中删除属性时，它将始终返回 true，除非该属性是不可配置的属性。

从地图中删除属性时，如果该属性存在且已被移除，则返回 true 否则，如果该属性不存在，则返回 false。

```
// deleting properties from objects
const obj = {
  one: 'one'
}

delete obj.one // true
delete obj.two // also true

// deleting properties from Maps
const map = new Map()

map.set('one', 'one')
map.delete('one') // true
map.delete('two') // false 
```

但是，如果您想要删除属于该对象的所有属性，该怎么办呢？
你可以做:

```
const obj = {
  one: 'one'
}

obj = {} 
```

在这个实现中，您并没有真正删除属性，您只是将 obj 设置为一个新的空对象，并依靠垃圾收集器来清理旧对象。问题是，如果该对象在其他地方被引用，它将仍然存在。更好的实现应该是:

```
for (let key in obj){
    if (obj.hasOwnProperty(key)){
        delete obj[key];
    }
} 
```

这样更好，但是仍然不能处理符号键。
地图使得清除所有元素变得非常容易，不管什么是关键:

```
const values = [['1', 'one'], [true, '5'], [Symbol('test'), 10], [function() {}, 3]]
const map = new Map(values)

map.clear() // completely empties out the map 
```

根据关于性能的 MDN 文档:

> 在涉及频繁添加和删除密钥对的情况下，Map 可能会表现得更好。

### 迭代地图

正如我在本文开头提到的，与对象不同，映射是基于插入排序的，这使得迭代更加可预测。

```
const obj = {};

obj[5] = 'five';
obj[4] = 'four';
Object.entries(obj); // [ ['4', 'four'], ['5', "five"] ]

const map = new Map();

map.set(5, 'five')
  .set(4, 'four')
  .entries(); // [ 5 => "five", 4 => "four" ] 
```

与对象类似，有三种方法可用于在地图上循环:

*   `map.keys()`返回包含关键字的 iterable
*   `map.values()`返回包含值的 iterable
*   `map.entries()`返回包含`[key, value]`对的 iterable

对象使用`Object.keys`、`Object.values`和`Object.entries`。一个主要的区别是这些方法返回数组，而 map 方法返回 iterables。

```
const obj = {
  one: 1,
  two: 2,
};

for (let key of Object.keys(obj)) {
  console.log(key)
} // logs "one" then "two"

for (let value of Object.values(obj)) {
  console.log(value)
} // logs 1 then 2

for (let entry of Object.entries(obj)) {
  console.log(entry)
} // logs ["one", 1] then ["two", 2] 
```

地图工作方式类似:

```
const map = new Map([["one", 1], ["two", 2]]);

for (let key of map.keys()) {
  console.log(key)
} // logs "one" then "two"

for (let value of map.values()) {
  console.log(value)
} // logs 1 then 2

for (let entry of map.entries()) {
  console.log(entry)
} // logs ["one", 1] then ["two", 2]

// One difference is that map.entries() is used by default in a for..of loop
for (let entry of map) {
  console.log(entry)
} // still logs ["one", 1] then ["two", 2] 
```

注意:因为任何东西都可以被设置为一个键，所以 map 将总是迭代所有的条目。但是对于对象来说，有些属性像符号一样不会被默认迭代。

### 在地图和物体之间转换

既然您已经知道了一些区别，那么了解如何将对象转换为地图或者将地图转换为对象可能会有所帮助，以便利用每种数据结构的优势。

Map 需要一个数组或 iterable，所以我们可以使用`Object.entries`来获取作为数组的键/值对，并将其传递给构造函数:

```
const obj = {
  'one': 1,
  'two': 2,
}

const map = new Map(Object.entries(obj));

console.log(map.get('one')) // 1 
```

好的，这看起来很简单...但是我们如何从地图上创建一个物体呢？幸运的是，我们有`Object.fromEntries`，它的工作方式基本上与`Object.entries` :
相反

```
const map = new Map();

map.set('one', 1);
map.set('two', 2);

const obj = Object.fromEntries(map.entries());
const obj = Object.fromEntries(map); // Or we can even omit the entries() since that's used by default

console.log(obj.one') // 1 
```

### 解构

因为贴图的排序类似于数组，所以您无法像处理对象那样通过键来进行析构。

```
const obj = {
  one: 1,
  two: 2,
  three: 3,
}

let { one, two } = obj;
console.log(one) // 1 
```

现在让我们试着解构一张地图:

```
const map = new Map([ [ 'one', 1], ['two', 2] ]);

let { one, two } = map;
console.log(one) // undefined

// But you can destructure it similar to an array where you destructure by the order items were added into the map
let [ firstEntry, secondEntry ] = map;
console.log(firstEntry) // ["one", 1]
console.log(secondEntry) // ["two", 2] 
```

## 地图 vs WeakMap

既然你是一个地图鉴赏家，学习一点 WeakMap 将是有益的，它也是在 ES6 中引入的。

使用 WeakMap 的一个主要区别是键必须是对象，而不是原始值。这意味着它们将通过引用传递。

那么为什么要使用 WeakMap 呢？与地图相比，使用 WeakMap 的主要优势是有利于记忆。

不可到达的对象被垃圾收集，但是如果它们作为一个键存在于另一个可到达的结构中，那么它们不会被垃圾收集。让我们看一个例子:

```
let obj = { name: 'Matt' } // object can be accessed

let obj = null // overwrite the reference, the object above will be garbage collected and removed from memory 
```

如果对象仍然可访问，它将不会从内存中删除:

```
let obj = { name: 'Matt' } // object can be accessed

let map = new Map();
map.set(obj, true);

obj = null // overwrite the reference, but since it's still reachable through the map, the object will not be garbage collected 
```

WeakSet 不阻止其关键对象的垃圾收集。

```
let obj = { name: 'Matt' } // object can be accessed

let weakMap = new WeakMap();
weakMap.set(obj, true);

obj = null // overwrite the reference, the object was removed from memory
// weakMap is now empty 
```

武器只有以下几种方法:`get`、`set`、`delete`、`has`。

为什么只有这些？因为 Javascript 引擎处理内存清理，所以它可以选择立即清理或等到更多的删除发生。

因此像武器地图的当前数量这样的事情永远不会完全准确。

那么你什么时候会使用 WeakMap 呢？
当您想将它用于仅持续到引用被销毁的额外存储时。

举个例子，你有一些用户，你想在他们访问某个页面时增加计数，但是一旦用户离开，你就不再关心了。

```
let userCountStorage = new WeakMap();

let user = { name: 'matt' };

incrementCount(user); // pretend this function adds the user to the userCountStorage or increments the count if they already exists

// increment every time they visit a page
incrementCount(user);
incrementCount(user);

// Now they're gone so we get rid of the reference
user = null // since the object is no longer reachable, the garbage collector will automatically also remove the item from our userCountStorage 
```

在上面的例子中，如果我们使用 Map 而不是 WeakMap，一旦我们销毁了其他地方的引用，如果我们不从存储中手动删除引用，我们就会遇到内存问题。

## 总结

使用地图或对象总是视情况而定，但希望现在您已经了解了使用每种数据结构的一些优点和缺点。

关于地图/对象的更多信息:

*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/weak map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)
*   [https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)