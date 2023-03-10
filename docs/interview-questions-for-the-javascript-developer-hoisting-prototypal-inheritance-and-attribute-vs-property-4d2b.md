# 面向 Javascript 开发人员的面试问题:提升、原型继承和属性对属性

> 原文：<https://dev.to/bcaruthers/interview-questions-for-the-javascript-developer-hoisting-prototypal-inheritance-and-attribute-vs-property-4d2b>

[![Macbook, Pens, Post-it Pad, and iPhone; Photo by Daniel Fazio on Unsplash](img/bd76b0a572076dfc0576684b3afcc828.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--17Cmxadm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dqejlzsb4/image/upload/v1560393782/daniel-fazio-558736-unsplash.jpg)

去参加工作面试很可怕。如果你正在面试一个新的职业领域，这可能会特别可怕。无论你是刚从大学毕业，完成了一个编码训练营，还是一个自学成才的 web 开发人员，你都需要确保你对面试中可能被问到的问题类型有所准备。在这个新系列中，我将选择一些在 JavaScript 前端开发人员面试中可能会被问到的问题。我这样做不仅是为了帮助你，读者，为你即将到来的面试做准备，也是为了帮助我为自己即将到来的面试做准备。在本文中，我将介绍提升、原型继承以及属性与特性。

### 吊装

[![Crane Hook; Photo by Samuel Zeller on Unsplash](img/b3d9e1896fe8a7839f90abd7cdbaf369.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DhoTbkYn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dqejlzsb4/image/upload/v1560393884/samuel-zeller-368928-unsplash.jpg)

提升是变量声明被“提升”或提升到其作用域的顶部。如果变量在函数内部，它被提升到局部/函数范围的顶部。如果变量在函数之外，它将被提升到全局范围的顶部。不管变量声明是在哪里进行的，都会这样做。

因此，如果我们在全局范围内编写以下代码:

[https://repl.it/@bcaruthers/Hoisting-Example?lite=true](https://repl.it/@bcaruthers/Hoisting-Example?lite=true)

我们会回来的。我们拿回`undefined`的原因是，它在识别变量`hello`的存在，但是因为提升，变量声明被提升到了全局范围的顶部，但是实际赋予变量的值没有被提升。代码被编译，就好像它被写成:

[https://repl.it/@bcaruthers/Hoisting-Example-2?lite=true](https://repl.it/@bcaruthers/Hoisting-Example-2?lite=true)

### 原型遗传

[![Man Holding Jar of Money; Photo by Melissa Walker Horn on Unsplash](img/8f369002d1a3799b0dfade67d7ccd252.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IsjvuDhn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dqejlzsb4/image/upload/v1560395872/melissa-walker-horn-1072597-unsplash.jpg)

人们常说 JavaScript 中的一切都是对象。例外是原语(数字、字符串、布尔值、未定义和空)。函数、数组、对象以及字符串、数字和布尔值的包装器都是对象。对象用于存储数据，保持代码整洁，并将应用程序组织成模块。JavaScript 使用构造函数或原型(其他编程语言称这些类)作为一种创建其他对象(实例)的蓝图。这通常是通过创建一个构造函数来完成的:

[https://repl.it/@bcaruthers/Prototypal-Inheritance?lite=true](https://repl.it/@bcaruthers/Prototypal-Inheritance?lite=true)

一旦定义了构造函数，你就可以像这样创建实例:

[https://repl.it/@bcaruthers/Prototypal-Inheritance-2?lite=true](https://repl.it/@bcaruthers/Prototypal-Inheritance-2?lite=true)

你可能会想，这很酷，但是这和原型继承有什么关系呢？坚持住，我快到了。

我们来定义一下继承。继承就是一个对象获得对另一个对象的属性和方法的访问权。术语原型是指 Javascript 是一种基于原型的编程语言。

现在回到我们的例子。这里我们将添加一个方法来计算狗的年龄(当然是狗的年龄)，但是我们将使用 prototype 属性在构造函数之外添加它。

[https://repl.it/@bcaruthers/Prototypal-Inheritance-3?lite=true](https://repl.it/@bcaruthers/Prototypal-Inheritance-3?lite=true)

通过使用`prototype`属性，对象`fido`和`fefe`可以访问方法`calculateAge`。他们可以使用这个方法的原因是因为**原型继承**(我告诉过你我会把它们都放在一起)。因此，如果我们运行`fido.calculateAge()`和`fefe.calculateAge()`，我们将得到狗的狗龄。以下是完整的代码片段:

[https://repl.it/@bcaruthers/Prototypal-Inheritance-4?lite=true](https://repl.it/@bcaruthers/Prototypal-Inheritance-4?lite=true)

### 属性对属性

[![Government Property Sign; Photo by Bruno Figueiredo on Unsplash](img/8bd4f2a79044357eaba2c6b97670c6ac.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JJ6d2Ctd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dqejlzsb4/image/upload/v1560395878/bruno-figueiredo-1160282-unsplash.jpg)

属性是由 HTML(超文本标记语言)定义的。它们提供了关于 HTML 元素的附加信息。属性的示例有:

*   href
*   科学研究委员会
*   类型
*   价值
*   中高音
*   等等…

属性是由 DOM(文档对象模型)定义的。一旦浏览器解析了 HTML 元素，就会创建一个 DOM 节点。因为这个节点是一个对象，所以它有属性。属性的示例有:

*   accessKey
*   属性
*   儿童人数
*   className
*   接受
*   儿童
*   等等…

属性和特性之间的主要区别是:

*   属性由 HTML 定义，属性由 DOM 定义。
*   DOM 属性由 HTML 属性初始化。
*   属性值不能更改。
*   属性值可以更改。

如你所见，HTML 属性和 DOM 属性是两回事。

我希望这篇文章能够帮助您更好地理解提升、原型继承以及 HTML 属性和 DOM 属性之间的区别。我更大的希望是，它能帮助你在面试中胜出，并让你得到你所申请的前端开发工作。我祝你好运，并感谢你花时间阅读这篇文章。

###### *照片演职员表(按出场顺序):*

###### [丹尼尔](https://unsplash.com/@danielfazio?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from Daniel Fazio")

###### [缪策勒](https://unsplash.com/@samuelzeller?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from Samuel Zeller")

###### [梅丽莎](https://unsplash.com/@sugercoatit?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from Melissa Walker Horn") 沃克霍恩】

###### [布鲁诺](https://unsplash.com/@bfigas?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge "Download free do whatever you want high-resolution photos from Bruno Figueiredo")