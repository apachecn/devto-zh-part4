# 使用 JavaScript 获取 DOM 元素

> 原文：<https://dev.to/attacomsian/getting-dom-elements-using-javascript-4do0>

*本文最初发表于[attacomsian.com/blog](https://attacomsian.com/blog/getting-dom-elements-javascript)。*

* * *

文档对象模型(DOM)是一个 HTML 和 XML 文档的编程接口，在文档加载后由浏览器创建。网页本质上是由 DOM 表示为节点和对象的文档。它允许程序操作文档的内容、结构和样式。

在本教程中，我们将学习如何使用 JavaScript 来访问 DOM 中的不同节点(HTML 元素)。让我们从第一种方法开始:通过 ID 获取元素。

## 按 ID 获取元素

`docuemnt`的`getElementById()`方法将元素 ID 作为输入，并返回一个代表 DOM 元素的`Element`对象。下面是一个例子:

```
<div id="unicorn">🦄</div> 
```

现在，我们可以通过使用 ID:
来获取上面的`<div>`元素

```
const unicorn = document.getElementById('unicorn'); 
```

ID 是区分大小写的和在整个 HTML 文档中唯一的。所以这个方法总是返回单个元素。如果没有找到匹配的元素，则返回`null`。

> **注意:**在调用`getElementById()`方法时，不要在 ID 字符串前加`#`符号。你将得到`null`而不是元素，然后你可能会想几个小时哪里出错了。

## 通过标签名称获取元素

`getElementsByTagName()`方法用于访问多个元素。它将标记名作为输入，并将所有匹配标记名的 DOM 元素返回为`HTMLCollection` :

```
<p>🐱</p>
<p>🐰</p>
<p>🐯</p>
<p>🐧</p> 
```

访问所有`<p>`元素的 JavaScript 代码:

```
const animals = document.getElementsByTagName('p'); 
```

此方法一次只搜索一个标记名。但是如果您将`*`作为标记名传入，您将获得 DOM 中的所有元素:

```
const allNodes = document.getElementsByTagName('*'); 
```

## 按名称获取元素

`getElementsByName()`方法用于通过元素的`name`属性获取元素的集合，并返回一个`NodeList`对象:

```
<input type="text" name="email">
<input type="tel" name="phone">
<input type="date" name="dob"> 
```

让我们得到所有名为`email` :
的元素

```
const emails = document.getElementsByName('email'); 
```

> **注意:**与必须唯一的`id`属性不同，多个 HTML 元素可以有相同的`name`属性。这就是为什么`getElementsByName()`返回一个节点集合。

## 按类名获取元素

想用`class`属性获取匹配元素列表？您可以使用`getElementsByClassName()`方法，只需给它传递一个类名(不带`.`，它将返回一个包含所有具有给定类名的 DOM 元素的`HTMLCollection`:

```
<div class="bird owl">🦉</div>
<div class="bird">🐦</div>
<div class="bird eagle">🦅</div>
<div class="animal cat">🐱</div> 
```

让我们得到所有的`bird`:

```
const birds = document.getElementsByClassName('bird'); 
```

该方法还接受多个由空格分隔的类名。让我们得到所有既有`bird`又有`eagle`类的元素:

```
const eagle = document.getElementsByClassName('bird eagle'); 
```

## 查询选择器

`querySelector()`是两种现代 JavaScript 方法之一，允许您基于 CSS 选择器从 DOM 获取元素。只需传入 CSS 选择器，您将获得与指定选择器匹配的第一个元素。如果不存在匹配，它返回`null`。这里有一个例子:

```
const email = document.querySelector('#signup input[name="email"]'); 
```

## 查询选择器全部

想要选择与指定选择器匹配的元素列表吗？请改用`querySelectorAll()`方法。该方法将多个 CSS 选择器作为输入，并返回一个`NodeList`——匹配给定选择器的 DOM 元素列表。让我们从上面的 HTML 标记:
中选择类为`bird`或`animal`的所有元素

```
const elems = document.querySelectorAll('.bird, .animal');
console.log(elems.length); // 4 
```

## 功能链接

还可以将多个函数链接在一起，在另一个元素中搜索元素。您首先需要使用`getElementById()`或`querySelector()`选择一个元素，然后链接另一个函数在
中进行搜索

```
<form id="signup">
    <input type="text" name="email">
    <input type="tel" name="phone">
    <input type="date" name="date-of-birth">
</form> 
```

获取 ID 为`signup` :
的元素内的所有`input`元素

```
const inputs = document.getElementById('signup').getElementsByTagName('input');
// OR
const inputs = document.querySelector('#signup').querySelectorAll('input'); 
```

## 遍历 html 集合和节点列表

我们上面讨论的大多数方法(除了`getElementById()`和`querySelector()`)都以`HTMLCollection`或`NodeList`的形式返回多个元素。

`HTMLCollection`不是数组，而是元素的一般集合。所以不可能用`forEach()`或`map()`来迭代它。但是，我们可以将它转换成一个真实的数组，然后使用`Array.from()`方法进行迭代:

```
const inputs = document.getElementById('signup').getElementsByTagName('input');
// iterate over HTMLCollection
Array.from(inputs).forEach(element => {
    console.log(element);
}); 
```

虽然`NodeList`也不是一个数组，但是它提供了`forEach()`方法来迭代元素:

```
const inputs = document.querySelector('#signup').querySelectorAll('input');
//iterate over NodeList
inputs.forEach(element => {
    console.log(element);
}); 
```

## 结论

这就是使用 JavaScript 获取 DOM 元素的全部内容。我们已经学习了很多不同的方法来访问 DOM 元素:使用`id`属性、HTML 标签名称、`name`属性、`class`属性和 CSS 选择器。我们还讨论了迭代这些方法返回的元素的一般集合的方法。

* * *

✌️:我写了关于现代 JavaScript、Node.js、Spring Boot 和所有 web 开发的东西。 [**订阅我的简讯**](https://attacomsian.com/newsletter) 每周获取 web 开发教程& protips。

* * *

**喜欢这篇文章？** [在推特上关注@ attacomsian](https://twitter.com/attacomsian)。你也可以在 [LinkedIn](https://linkedin.com/in/attacomsian) 和 [DEV](https://dev.to/attacomsian) 上关注我。

* * *

<small>照片由[潘卡杰·帕特尔](https://unsplash.com/@pankajpatel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/search/photos/website?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄。</small>