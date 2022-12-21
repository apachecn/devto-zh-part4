# JavaScript 中的地图

> 原文：<https://dev.to/attacomsian/maps-in-javascript-4148>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/maps-javascript)。*

* * *

映射是 JavaScript 中的一种新数据结构，它允许您创建键值对集合。它们是随 ES6(也称为 ES2015)以及 JavaScript 中的[集合](https://dev.to/attacomsian/sets-in-javascript-2hk7)一起引入的。map 对象可以将对象和图元存储为键和值。

映射类似于也用于存储键值数据的对象。但是主要的区别是映射键可以是任何类型，而不仅限于字符串和符号。与对象不同，它也更容易获得地图的大小。

## 初始化地图

就像集合一样，你可以使用`Map()`构造函数来创建一个空的映射:

```
const items = new Map(); 
```

还可以向构造函数传递一个 iterable(比如 array)来初始化 map:

```
const items = new Map([['🦅', 'Eagle'], ['🐶', 'Dog']]); 
```

## 绘制方法

主要的方法和属性有`set()`、`get()`、`has()`、`size`、`delete()`、`clear()`。下面是一个简单的地图示例，展示了这些方法的使用:

```
const items = new Map();

// add items
items.set('🐶', 'Dog');
items.set('🦅', 'Eagle');
items.set('🚄', 'Train');
items.set(45, 'Number');
items.set(true, 'Boolean');

// get item
items.get(45); // Number
items.get('🐶'); // Dog
items.get('🐺'); // undefined

// check if key exists
items.has('🚄'); // true
items.has('🐺'); // false

// get items count
items.size; // 5

// delete item
items.delete('🦅'); // true
items.delete('🦅'); // false - already removed

// delete all items
items.clear(); 
```

像集合一样，映射键也是唯一的。用同一个键多次调用`set()`不会添加多个键值对。取而代之的是，值部分被替换为最新的值:

```
const animals = new Map();

animals.set('🐺', 'Wolf');
animals.set('🐺', 'Wolf Face');

console.log(animals); // Map(1) {"🐺" => "Wolf Face"} 
```

## 地图中的物体

因为映射允许我们将任何数据类型存储为键或值，所以我们可以存储复杂的对象，比如对象文字、数组甚至函数:

```
const props = {
    browser: 'Chrome',
    os: 'Ubuntu 19.04'
};

const hamburger = () => '🍔'; 

const things = new Map();

things.set('birds', ['🦉', '🦅']);
things.set('user', {name: 'John Doe', planet: 'Earth'});
things.set(props, 59);
things.set(hamburger, 'What is the food?');

things.get(props); // 59
things.get(hamburger); // What is the food? 
```

## 遍历地图

与对象不同，当我们在 map 上迭代时，键-值对的返回顺序与它们被插入的顺序相同。我们可以使用`for...of`循环来迭代所有的键值对:

```
const foods = new Map([
    ['🍌', 'Banana'],
    ['🍕', 'Pizza'],
    ['🥒', 'Cucumber'],
    ['🌽', 'Maize'],
]);

for (const [key, value] of foods) {
    console.log(`${key}: ${value}`);
}

// 🍌: Banana
// 🍕: Pizza
// 🥒: Cucumber
// 🌽: Maize 
```

同样，我们也可以使用内置的`forEach()`方法来迭代所有元素:

```
foods.forEach((key, value) => {
    console.log(`${key}: ${value}`);
}); 
```

## 键和值

映射只提供了`keys()`和`values()`方法来访问键和值。这些方法返回一个新的 iterable 对象，这个对象也可以用来迭代所有的键或值:

```
for (const key of foods.keys()) {
    console.log(key);
}

for (const value of foods.values()) {
    console.log(value);
} 
```

`Map`对象也有`entries()`方法，返回条目`[key, value]`的 iterable。在`for...of`循环中默认使用该方法。这里有一个例子:

```
for (const [key, value] of foods.entries()) {
    console.log(`${key}: ${value}`);
} 
```

类似于[集合](https://attacomsian.com/blog/maps-javascript#keys-and-values)，可以在`entries()`方法返回的 iterable 上调用`next()`方法，来逐个遍历键值对:

```
const entries = foods.entries();

console.log(entries.next()); // {value: ["🍌", "Banana"], done: false} 
```

## 结论

map 是键-值对的集合，它允许我们将对象和原语存储为键和值。与对象不同，映射键可以是任何类型:对象、数组、函数甚至是另一个映射。类似于集合，键是唯一的；它们在地图中只能出现一次。当我们迭代一个 map 对象时，键值对的返回顺序与它们在 map 中的插入顺序相同。

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。

* * *

**喜欢这篇文章？** [在推特上关注@ attacomsian](https://twitter.com/attacomsian)。你也可以在 [LinkedIn](https://linkedin.com/in/attacomsian) 和 [DEV](https://dev.to/attacomsian) 上关注我。