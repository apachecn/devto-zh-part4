# Javascript 中的 Object.entries()和 Object.values()方法

> 原文：<https://dev.to/attacomsian/object-entries-and-object-values-methods-in-javascript-3l8c>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/object-entries-values-javascript)。*

* * *

随着 ECMAScript 2017 (ES8)的发布，`Object.entries()`和`Object.values()`方法被引入 JavaScript `Object`构造器。让我们快速浏览一下这些有用的方法。

## `Object.entries()`法

`Object.entries()`方法将一个对象作为参数，并返回一个由键值对组成的数组:

```
const birds = {
    owl: '🦉',
    eagle: '🦅',
    duck: '🦆'
};

const entries = Object.entries(birds);
console.log(entries);

// [['owl', '🦉'], ['eagle', '🦅'], ['duck', '🦆']] 
```

Enter fullscreen mode Exit fullscreen mode

数组元素的顺序不依赖于对象是如何定义的。该顺序与`for...in`循环提供的顺序相同。

### 遍历一个对象

我们也可以使用`Object.entries()`来迭代对象:

```
// using `for...of` loop
for (const [key, value] of Object.entries(birds)) {
    console.log(`${key}: ${value}`);
}

// owl: 🦉
// eagle: 🦅
// duck: 🦆

// using array destructuring
Object.entries(birds).forEach(([key, value]) => console.log(`${key}: ${value}`));

// owl: 🦉
// eagle: 🦅
// duck: 🦆 
```

Enter fullscreen mode Exit fullscreen mode

### 将对象转换成地图

由于[地图构造器](https://attacomsian.com/blog/maps-javascript#initialize-a-map)也使用`entries`的 iterable 来初始化地图对象，所以`Object.entries()`方法可以用来从对象
创建地图

```
const map = new Map(Object.entries(birds));

console.log(map.size); // 3
console.log(map.has('owl')); // true
console.log(map.get('duck')); // 🦆 
```

Enter fullscreen mode Exit fullscreen mode

## `Object.values()`法

`Object.values()`方法的工作方式非常类似于`Object.entries()`，但是只返回自己的可枚举字符串键属性的值，返回顺序与`for...in`循环提供的顺序相同:

```
const foods = {
    cake: '🍰',
    pizza: '🍕',
    candy: '🍬',
    icecream: '🍨'
};

const values = Object.values(foods);
console.log(values);

// ['🍰', '🍕', '🍬', '🍨'] 
```

Enter fullscreen mode Exit fullscreen mode

`Object.values()`和`Object.entries()`都不遵循原型链，只迭代直接添加到给定对象的属性。他们也忽略了所有不可枚举的属性:

```
Object.defineProperty(foods, 'sushi', {
    value: '🍣',
    writable: true,
    configurable: true,
    enumerable: false
});

console.log(Object.values(foods));

// ['🍰', '🍕', '🍬', '🍨'] 
```

Enter fullscreen mode Exit fullscreen mode

### 将一个对象转换为集合

由于[集合构造函数](https://attacomsian.com/blog/sets-javascript#initialize-a-set)接受一个 iterable，用`Object.values()`，我们可以很容易地将一个`Object`转换成一个`Set` :

```
const set = new Set(Object.values(foods));

console.log(set.size); // 4
console.log(set.has('🍨')); // true 
```

Enter fullscreen mode Exit fullscreen mode

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。

* * *

**喜欢这篇文章？** [在推特上关注@ attacomsian](https://twitter.com/attacomsian)。你也可以在 [LinkedIn](https://linkedin.com/in/attacomsian) 和 [DEV](https://dev.to/attacomsian) 上关注我。