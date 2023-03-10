# JavaScript 中克隆对象的 3 种方法

> 原文：<https://dev.to/samanthaming/3-ways-to-clone-objects-in-javascript-2oie>

[![CodeTidbit by SamanthaMing.com](img/14f0e2d328ad2b1ab0bb1aac3fdaa416.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hKg1SbkB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cc861cn6f2047d6ydyec.png)

因为#JavaScript 中的对象是引用值，所以不能简单地使用`=`进行复制。但是不要担心，这里有三种方法可以让你克隆一个对象👍

```
const food = { beef: '🥩', bacon: '🥓' }

// "Spread"
{ ...food }

// "Object.assign"
Object.assign({}, food)

// "JSON"
JSON.parse(JSON.stringify(food))

// RESULT:
// { beef: '🥩', bacon: '🥓' } 
```

## 对象为参照类型

你的第一个问题可能是，为什么我不能使用`=`。让我们看看这样做会发生什么:

```
const obj = {one: 1, two: 2};

const obj2 = obj;

console.log(
  obj,  // {one: 1, two: 2};
  obj2  // {one: 1, two: 2};
) 
```

到目前为止，两个对象似乎输出相同的东西。所以没问题，对。但是让我们看看如果编辑第二个对象会发生什么:

```
const obj2.three = 3;

console.log(obj2);
// {one: 1, two: 2, three: 3}; <-- ✅

console.log(obj);
// {one: 1, two: 2, three: 3}; <-- 😱 
```

WTH？！我改变了`obj2`但是为什么`obj`也受到了影响。这是因为对象是引用类型。所以当你使用`=`时，它把指针复制到它所占据的内存空间。引用类型不保存值，它们是指向内存中的值的指针。

如果你想了解更多这方面的知识，可以去看看高顿的朱[手表和代码](https://watchandcode.com)课程。报名免费，看视频《与物体对比》。他给出了一个超级棒的解释。

## 利用传播

使用扩展将克隆您的对象。请注意，这将是一个肤浅的副本。在这篇文章中，克隆对象的扩展操作符处于第 4 阶段。所以还没有正式列入规格。所以如果你要用这个，你需要用 Babel(或者类似的东西)来编译它。

```
const food = { beef: '🥩', bacon: '🥓' };

const cloneFood = { ...food };

console.log(cloneFood); 
// { beef: '🥩', bacon: '🥓' } 
```

## 使用 Object.assign

或者，`Object.assign`是在正式发布的同时也创建一个浅拷贝的对象。

```
const food = { beef: '🥩', bacon: '🥓' };

const cloneFood = Object.assign({}, food);

console.log(cloneFood);
// { beef: '🥩', bacon: '🥓' } 
```

## 使用 JSON

这最后的方式会给你一个深刻的副本。现在我要提一下，这是一种快速而肮脏的深度克隆对象的方法。对于一个更健壮的解决方案，我推荐使用类似于[洛达什](https://lodash.com/docs/#cloneDeep)
的东西

```
const food = { beef: '🥩', bacon: '🥓' };

const cloneFood = JSON.parse(JSON.stringify(food))

console.log(cloneFood); 
// { beef: '🥩', bacon: '🥓' } 
```

### Lodash DeepClone vs JSON

这是来自社区的评论。是的，是为了我之前的帖子，[如何深度克隆一个数组](https://www.samanthaming.com/tidbits/50-how-to-deep-clone-an-array)。但是这个想法仍然适用于物体。

我想让你注意到，deepClone 和 JSON.stringify/parse.之间有一些不同

*   **JSON.stringify/parse**只处理数字、字符串和没有函数或符号属性的对象文字。
*   **deepClone** 工作用的所有类型、函数和符号都是通过引用复制的。

这里有一个例子:

```
const lodashClonedeep = require("lodash.clonedeep");

const arrOfFunction = [() => 2, {
    test: () => 3,
}, Symbol('4')];

// deepClone copy by refence function and Symbol
console.log(lodashClonedeep(arrOfFunction));
// JSON replace function with null and function in object with undefined
console.log(JSON.parse(JSON.stringify(arrOfFunction)));

// function and symbol are copied by reference in deepClone
console.log(lodashClonedeep(arrOfFunction)[0] === lodashClonedeep(arrOfFunction)[0]);
console.log(lodashClonedeep(arrOfFunction)[2] === lodashClonedeep(arrOfFunction)[2]); 
```

*[@ OlegVaraksin](https://twitter.com/OlegVaraksin/status/1152850845303824384):*JSON 方法有循环依赖的麻烦。此外，克隆对象中属性的顺序可能不同。

## 浅层克隆 vs 深层克隆

当我使用 spread 时...要复制一个对象，我只是创建一个浅拷贝。如果数组是嵌套的或者多维的，那就不行。我们来看看:

```
const nestedObject = {
  country: '🇨🇦',
  {
    city: 'vancouver'
  }
};

const shallowClone = { ...nestedObject };

// Changed our cloned object
clonedNestedObject.country = '🇹🇼'
clonedNestedObject.country.city = 'taipei'; 
```

所以我们通过改变城市来改变我们的克隆对象。让我们看看输出。

```
console.log(shallowClone);
// {country: '🇹🇼', {city: 'taipei'}} <-- ✅

console.log(nestedObject);
// {country: '🇨🇦', {city: 'taipei'}} <-- 😱 
```

浅复制意味着复制第一个级别，引用更深的级别。

### 深度复制

让我们举一个相同的例子，但是使用“JSON”
应用深度拷贝

```
const deepClone = JSON.parse(JSON.stringify(nestedObject));

console.log(deepClone);
// {country: '🇹🇼', {city: 'taipei'}} <-- ✅

console.log(nestedObject);
// {country: '🇨🇦', {city: 'vancouver'}} <-- ✅ 
```

如您所见，深层副本是嵌套对象的真实副本。通常浅拷贝就足够了，你不需要深拷贝。就像钉枪 vs 锤子。大部分时间锤子都很好。对于一些小的艺术和工艺来说，使用射钉枪经常是大材小用，锤子就可以了。这一切都是为了使用正确的工具完成正确的工作🤓

## 表现

不幸的是，我不能为 spread 编写测试，因为它还没有正式纳入规范。尽管如此，我还是将它包含在测试中，以便您将来可以运行它。但是结果显示`Object.assign`比`JSON`快很多。

[性能测试](https://jsperf.com/3-ways-to-clone-object/1)

## 社区输入

### object . assign vs spray

*[@d9el](https://dev.to/adameier/comment/d9el) :* 需要注意的是 Object.assign 是一个修改并返回目标对象的函数。在莎曼珊的例子中使用了下面的话，

```
const cloneFood = Object.assign({}, food) 
```

`{}`是被修改的对象。此时，目标对象没有被任何变量引用，但是因为`Object.assign`返回目标对象，我们能够将结果赋值对象存储到`cloneFood`变量中。我们可以改变我们的例子，使用下面的:

```
const food = { beef: '🌽', bacon: '🥓' };

Object.assign(food, { beef: '🥩' });

console.log(food);
// { beef: '🥩', bacon: '🥓' } 
```

很明显，我们的食物对象中的`beef`的值是错误的，所以我们可以使用`Object.assign`来赋值`beef`的正确值。我们实际上根本没有使用函数的返回值，但是我们修改了我们用 const `food`引用的目标对象。

另一方面，Spread 是将一个对象的属性复制到一个新对象中的操作符。如果我们想复制上面的例子，使用 spread 来修改我们的变量`food...`

```
const food = { beef: '🌽', bacon: '🥓' };

food = {
  ...food,
  beef: '🥩',
}
// TypeError: invalid assignment to const `food' 
```

我们得到了一个错误，因为我们在创建新对象时使用了 spread，因此将一个全新的对象赋给了用`const`声明的`food`，这是非法的。所以我们可以选择声明一个新变量来保存我们的新对象，如下:

```
const food = { beef: '🌽', bacon: '🥓' };

const newFood = {
  ...food,
  beef: '🥩',
}

console.log(newFood);
// { beef: '🥩', bacon: '🥓' } 
```

或者我们可以用`let`或`var`声明`food`，这将允许我们分配一个全新的对象:

```
let food = { beef: '🌽', bacon: '🥓' };

food = {
  ...food,
  beef: '🥩',
}

console.log(food);
// { beef: '🥩', bacon: '🥓' } 
```

*感谢:[@ d9el](https://dev.to/adameier/comment/d9el)T3】*

### 使用外部库进行深度克隆

*   *[@ lesjeuxdebel](https://twitter.com/lesjeuxdebebel/status/1153259251705503744):*我个人用的是带`$.extend();`功能的 jquery
*   *[@edlinkiii](https://twitter.com/edlinkiii/status/1153035836356747267) :* 下划线. js ~~ `_.clone()`
*   *[@Percy_Burton](https://twitter.com/Percy_Burton/status/1152671704155660289) :* 我所知道的唯一的方法就是使用 Lodash 库，cloneDeep 方法。

### 使用 JavaScript 的更多方式

*   *[@ hariharan _ d3v](https://twitter.com/hariharan_d3v/status/1152815791009460224):*`Object.fromEntries(Object.entries(food))`【浅薄】克隆对象。

**💬你还知道如何克隆一个对象的其他方法吗？把它们放在评论里🙃**

* * *

## 资源

*   [MDN Web Docs: Object.assign](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)
*   [堆栈溢出:在 JavaScript 中深度克隆一个对象最有效的方法是什么？](https://stackoverflow.com/questions/122102/what-is-the-most-efficient-way-to-deep-clone-an-object-in-javascript)
*   [2 属性:剩余/展开属性](https://2ality.com/2016/10/rest-spread-properties.html)
*   [堆栈溢出:对象扩散 vs. Object.assign](https://stackoverflow.com/questions/32925460/object-spread-vs-object-assign)

* * *

**感谢阅读❤**
问好！[insta gram](https://www.instagram.com/samanthaming/)|[Twitter](https://twitter.com/samantha_ming)|[脸书](https://www.facebook.com/hisamanthaming) | [博客](https://www.samanthaming.com/blog)|[SamanthaMing.com](https://www.samanthaming.com/)