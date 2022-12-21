# 你可能不知道 javascript 本身可以做的 6 件事

> 原文：<https://dev.to/duranenmanuel/6-things-you-probably-did-not-know-javascript-could-do-natively-2pen>

*原贴于[Enmascript.com](https://enmascript.com/articles/2019/09/24/6-things-you-probably-did-not-know-javascript-could-do-natively)T3】*

<mark>**#你知道吗**是一个新的英语脚本系列，我们对我们认为可能相关或被低估的主题进行简短的解释，如果你正在寻找更完整的深入文章，请查看我们的 **[其他系列](https://enmascript.com/series)** 。</mark>

JavaScript 是一种进化很快的语言；因此，有时很难了解它的所有特性和功能。在这篇短文中，我们将探索一些您可能不知道的特性。

## 获取查询字符串参数

`URLSearchParams`是一个允许我们处理查询字符串参数的接口，它已经出现了很多年，但是它在开发人员中并不流行，这可能会让你感到惊讶，让我们来看看如何使用它:

```
// to get the query strings from the current url use window.location.search
const queryStrings = new URLSearchParams('?browser=chrome&action=redirect');
queryStrings.get('browser');
queryStrings.has('action'); 
```

很简单，如果你对完整的功能感兴趣，这里有更多关于完整 API 的信息。

## 使用 Set 对象创建一个唯一的元素列表

在 javascript 中创建唯一列表是一项常见的任务，这通常是通过应用过滤器或 for 循环来实现的，但还有另一种方法来利用`Set`对象来实现。

```
const list = [1, 2, 3, 5, 2, 5, 7];
const uniqueList = [...new Set(list)]; 
```

我们将原始值的数组传递给`Set`对象；它创建一个唯一值的集合，然后使用 spread 操作符语法和数组文字将这个集合转换成一个*列表*。

## 将原始值列表转换为不同的类型

有时来自 DOM 的端点或处理过的数据不能返回我们需要的值的类型，我看到过这种情况，尤其是在处理数据集属性时。假设我们有下面的列表:

```
const naiveList = ['1500', '1350', '4580']; 
```

我们想对数组中的所有元素求和，在 JavaScript 中，如果你“求和”两个像`'1' + '2'`这样的字符串，它们会连接起来，一般来说，为了解决这个问题，我们会利用`parseInt`函数，但还有另一种方法；我们可以将数组中的元素转换成我们需要的原语类型:

```
const castedList = naiveList.map(Number); 
```

`castedList`现在包含具有正确`Number`类型的值。

## 展平嵌套数组值

随着 Redux 等单页面应用程序架构的兴起，前端数据规范化等概念变得更加流行，这种“数据规范化”趋势的一部分有时意味着将所有元素的 id 列在同一级别。

假设我们有下面的列表，我们想把它展平:

```
const nestedList = [133, 235, 515, [513, 15]];
const flattenList = nestedList.flat(); 
```

就像这样，我们的 id 数组现在变平了。

## 用 Object.freeze 避免对象突变

谈到功能代码，我被问得最多的一件事是如何阻止对象变异，这个问题的答案总是一样的*冻结*它们:

```
const immutableObject = {
    name: 'Enmascript',
    url: 'https://enmascript.com'
};
Object.freeze(immutableObject);
immutableObject.twitter = 'https://twitter.com/duranenmanuel';
immutableObject.name = 'Another name';
// immutableObject is still { name: "Enmascript", url: "https://enmascript.com" } 
```

## 用 Object.seal 创建了受控对象

与`Object.freeze`相同，但它允许您更改已经在对象中定义的属性值，这将使您能够 _ 控制 _ 对象中声明的属性，而不是定义:

```
const controlledObject = {
    name: 'Barry Allen'
};
Object.seal(controlledObject);
controlledObject.name = 'Clark Kent';
controlledObject.hero = 'Superman';
// controlledObject will return { name: "Clark Kent" } 
```

你知道其他不流行但是有用的 javascript 特性吗？[与我们分享](https://www.reddit.com/r/javascript/comments/d8njfy/6_things_you_probably_did_not_know_javascript/)。