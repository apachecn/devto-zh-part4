# ES6:发现器械包、武器包、地图和武器地图

> 原文：<https://dev.to/albertomontalesi/es6-discovering-sets-weaksets-maps-and-weakmaps-db2>

> 这篇文章摘自我的书:现代 JavaScript 完全指南。
> 
> [![book banner](img/a6eae9be07be922038dfd254ab4f485e.png) ](http://a-fwd.to/5gUojI8) Get my ebook on [Amazon](http://a-fwd.to/5gUojI8) and [Leanpub](https://leanpub.com/thecompleteguidetomodernjavascript2019)

## 什么是`Set`？

一个`Set`是一个`Object`，在这里我们可以存储任何类型的**唯一值**。

```
// create our set
const family = new Set();

// add values to it
family.add("Dad");
console.log(family);
// Set [ "Dad" ]

family.add("Mom");
console.log(family);
// Set [ "Dad", "Mom" ]

family.add("Son");
console.log(family);
// Set [ "Dad", "Mom", "Son" ]

family.add("Dad");
console.log(family);
// Set [ "Dad", "Mom", "Son" ] 
```

如你所见，最后我们试图再次添加“爸爸”，但是`Set`仍然保持不变，因为一个`Set`只能接受**的唯一值**。

让我们继续使用同一个`Set`，看看我们能在它上面使用什么方法。

```
family.size;
// 3
family.keys();
// SetIterator {"Dad", "Mom", "Son"}
family.entries();
// SetIterator {"Dad", "Mom", "Son"}
family.values();
// SetIterator {"Dad", "Mom", "Son"}
family.delete("Dad");
family;
// Set [ "Mom", "Son" ]
family.clear;
family;
// Set [] 
```

如你所见，`Set`有一个`size`属性，我们可以从它里面`delete`一个项目，或者使用`clear`从它里面删除所有项目。

我们还可以注意到，一个`Set`没有键，所以当我们调用`.keys()`时，我们得到的与调用`.values()`或`.entries()`相同。

### 循环过一次`Set`

我们有两种迭代`Set`的方法:使用`.next()`或者使用`for of`循环。

```
// using `.next()`
const iterator = family.values();
iterator.next();
// Object { value: "Dad", done: false }
iterator.next();
// Object { value: "Mom", done: false }

// using a `for of` loop
for(const person of family) {
  console.log(person);
}
// Dad
// Mom
// Son 
```

### 从数组中删除重复项

我们可以使用一个`Set`从一个数组中删除重复的值，因为我们知道它只能保存唯一的值。

```
const myArray = ["dad", "mom", "son", "dad", "mom", "daughter"];

const set = new Set(myArray);
console.log(set);
// Set [ "dad", "mom", "son", "daughter" ]
// transform the `Set` into an Array
const uniqueArray = Array.from(set);
console.log(uniqueArray);
// Array [ "dad", "mom", "son", "daughter" ]

// write the same but in a single line
const uniqueArray = Array.from(new Set(myArray));
// Array [ "dad", "mom", "son", "daughter" ] 
```

如您所见，新数组只包含原始数组中的唯一值。

## 什么是`WeakSet`？

一个`WeakSet`类似于一个`Set`，但是它只能**只有**包含对象。

```
let dad = {name: "Daddy", age: 50};
let mom = {name: "Mummy", age: 45};

const family = new WeakSet([dad,mom]);

for(const person of family){
  console.log(person);
}
// TypeError: family is not iterable 
```

我们创建了新的`WeakSet`，但是当我们试图使用一个`for of`循环时，它不起作用，我们不能迭代一个`WeakSet`。

当我们从 A `WeakSet`中删除一个元素后，它会自动清理。

```
dad = null;
family;
// WeakSet [ {…}, {…} ]

// wait a few seconds
family;
// WeakSet [ {…} ] 
```

如你所见，几秒钟后**爸爸**被移走*垃圾被收集*。这是因为当我们将值设置为`null`时，对它的引用丢失了。

## 什么是`Map`？

一个`Map`类似于一个`Set`，但是它们有键和值对。

```
const family = new Map();

family.set("Dad", 40);
family.set("Mom", 50);
family.set("Son", 20);

family;
// Map { Dad → 40, Mom → 50, Son → 20 }
family.size;
// 3

family.forEach((key,val) => console.log(val,key));
// Dad 40
// Mom 50
// Son 20

for(const [key,val] of family){
  console.log(key,val);
}
// Dad 40
// Mom 50
// Son 20 
```

如果你还记得的话，我们只能用一个`for of`循环来迭代一个`Set`，而我们可以用一个`for of`和一个`forEach`循环来迭代一个`Map`。

## 什么是`WeakMap`？

一个`WeakMap`是键/值对的集合，类似于一个`WeakSet`，即使在一个`WeakMap`中，键也被*弱引用*，这意味着当引用丢失时，值将从`WeakMap`和*垃圾收集*中移除。

一个`WeakMap`是**不是**可枚举的，因此我们不能循环访问它。

```
let dad = { name: "Daddy" };
let mom = { name: "Mommy" };

const myMap = new Map();
const myWeakMap = new WeakMap();

myMap.set(dad);
myWeakMap.set(mom);

dad = null;
mom = null;

myMap;
// Map(1) {{…}}
myWeakMap;
// WeakMap {} 
```

如你所见，*妈妈*在我们将其值设置为`null`后被垃圾收集，而*爸爸*仍然留在我们的`Map`中。

* * *

非常感谢您的阅读。关注我的 DevTo 或我在 inspiredwebdev.com 的博客了解更多信息。

 [![book banner](img/a6eae9be07be922038dfd254ab4f485e.png)](http://a-fwd.to/5gUojI8) 
在[亚马逊](http://a-fwd.to/5gUojI8)和 [Leanpub](https://leanpub.com/thecompleteguidetomodernjavascript2019) 上获取我的电子书