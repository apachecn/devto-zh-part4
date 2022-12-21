# JavaScript 中的集合

> 原文：<https://dev.to/attacomsian/sets-in-javascript-2hk7>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/sets-javascript)。*

* * *

集合是 ES6 中一种特殊类型的对象，允许您创建唯一值的集合。每个值在集合中只出现一次。存储在集合中的值可以是基本类型(字符串、数字、布尔值)或对象(对象文字、数组)。

## 初始化一组

您可以使用`Set()`构造函数创建一个空集:

```
const birds = new Set(); 
```

Enter fullscreen mode Exit fullscreen mode

或者，您可以将一个 iterable 对象(如数组)传递给构造函数来初始化集合。iterable 中的所有元素都将被添加到新集合:

```
const birds = new Set(['🐦', '🦉', '🦆', '🦅']); 
```

Enter fullscreen mode Exit fullscreen mode

因为字符串是可迭代的，所以它们也可以被传入来创建一个集合:

```
const fruits = new Set('🍒🍇🍉🍓🍑'); 
```

Enter fullscreen mode Exit fullscreen mode

## 设定方法

您可以在`Set`对象上使用的一些方法有`add()`、`has()`、`size`、`delete()`和`clear()` :

```
const birds = new Set();

// add items
birds.add('🐦');
birds.add('🦉');
birds.add('🦆');
birds.add('🦅');

// check if item exists
birds.has('🦉'); // true
birds.has('🐥'); // false

// get items count
birds.size; // 4

// delete item
birds.delete('🦆'); // true
birds.delete('🦆'); // false - already deleted

// delete all items
birds.clear(); 
```

Enter fullscreen mode Exit fullscreen mode

由于一个集合只能存储唯一的值，用相同的值多次调用`add()`不会增加新的项目:

```
const birds = new Set();
birds.add('🐦');
birds.add('🐦');
birds.add('🐦');

birds.size; // 1

console.log(birds); // Set(1) {"🐦"} 
```

Enter fullscreen mode Exit fullscreen mode

## 设置中的对象

我们还可以放置不同的对象类型，比如对象文字、数组、日期等。进入设定:

```
const set = new Set(['🦉', '🌹']);

set.add(['🦉', '🍌']);
set.add({ name: 'John Doe', planet: 'Earth' });
set.add(new Date());

set.forEach(value => {
    console.log(value);
});

// 🦉
// 🌹
// ["🦉", "🍌"]
// {name: "John Doe", planet: "Earth"}
// Thu May 16 2019 12:47:09 GMT+0100 
```

Enter fullscreen mode Exit fullscreen mode

## 迭代集合

您可以使用`forEach()`来迭代集合:

```
const flowers = new Set(['🌷', '🌹', '🌻', '🌸']);

flowers.forEach(flower => {
    console.log(`Hey ${flower}!`)
});

// Hey 🌷!
// Hey 🌹!
// Hey 🌻!
// Hey 🌸! 
```

Enter fullscreen mode Exit fullscreen mode

您还可以使用`for...of`循环来迭代集合:

```
for (const flower of flowers) {
    console.log(flower);
} 
```

Enter fullscreen mode Exit fullscreen mode

## 键和值

集合也有`keys()`和`values()`方法，就像[地图](https://attacomsian.com/blog/maps-javascript)一样。唯一的例外是`keys()`方法只是`values()`方法的别名。两者都返回一个新的迭代器对象，其值的顺序与它们被添加到集合中的顺序相同。我们也可以使用这些方法来迭代集合:

```
const fruits = new Set('🍒🍇🍉🍓🍑');

for (const k of fruits.keys()) {
    console.log(k);
}

for (const v of fruits.values()) {
    console.log(v);
} 
```

Enter fullscreen mode Exit fullscreen mode

我们也可以使用迭代器像下面这样逐个迭代集合值:

```
const fruits = new Set('🍒🍇🍉');
const items = fruits.values();

console.log(items.next()); // {value: "🍒", done: false}
console.log(items.next()); // {value: "🍇", done: false}
console.log(items.next()); // {value: "🍉", done: true} 
```

Enter fullscreen mode Exit fullscreen mode

调用`next()`将每一项作为`{value: <value>, done: <boolean>}`对象返回，直到迭代器结束，此时`done`为`true`。集合有另一个叫做`entries()`的方法，它也返回一个迭代器，但是值重复了两次:

```
const fruits = new Set('🍒🍇🍉');
const items = fruits.entries();

console.log(items.next()); // {value: ["🍒", "🍒"], done: false} 
```

Enter fullscreen mode Exit fullscreen mode

## 结论

集合是 ES6 中引入的新对象类型，允许您创建值集合。值可以是基元或对象，并且在集合中只能出现一次；在系列中独一无二。您可以按照值在集合中的插入顺序迭代这些值。

如果你想了解更多，请查看我们的 JavaScript 地图指南。

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。

* * *

**喜欢这篇文章？** [在推特上关注@ attacomsian](https://twitter.com/attacomsian)。你也可以在 [LinkedIn](https://linkedin.com/in/attacomsian) 和 [DEV](https://dev.to/attacomsian) 上关注我。