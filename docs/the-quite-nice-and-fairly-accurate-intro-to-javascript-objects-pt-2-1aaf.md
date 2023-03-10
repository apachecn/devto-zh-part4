# 相当好和相当准确的 JavaScript 对象介绍(pt。2)

> 原文：<https://dev.to/ale3oula/the-quite-nice-and-fairly-accurate-intro-to-javascript-objects-pt-2-1aaf>

JavaScript 有两种数据类型:原语和对象。我在[中写了一篇介绍 JavaScript 原语的文章，非常好而且相当准确地介绍了 JavaScript 原语数据类型](https://dev.to/ale3oula/the-quite-nice-and-fairly-accurate-introduction-to-javascript-primitive-data-types-e99)。

那么，现在让我们看看对象的基础知识。😎

**物体**

在 JavaScript 或通用编程中，对象可以模拟现实世界的事物，例如动物、汽车或人。对象用于存储键值集合，并表示更复杂的实体。

我们可以用花括号{}定义(并创建)一个带有*对象文字*的 JavaScript 对象:

[![Empty Object](img/fca7efa653aef4cf172df216625600bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dvn6Bm6a--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/C10SLmy/carbon-1.png)

**键:值**

我们可以用*无序的*数据填充一个对象，称为**属性**。一个*属性*是一个**键:值**对，其中键是一个字符串(也是*属性名*)，值可以是任何数据类型。键类似于指向内存中保存值的位置的变量名。该值可以是任何数据类型(包括函数或其他对象)。

[![Object with properties](img/ed8ae84be32a400d42b71c40912531ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KlIPTLJw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/McRw4rj/Group-1.png)

**访问属性**

访问对象属性有两种方式:**点符号**和**括号符号**。

使用*点符号*，我们写下对象的名称、点运算符，然后是属性名:

[![Dot notation](img/d08a352827126618bc92ab2ce50a1780.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cUqdX-TE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/WnLJNhL/Group-5.png)

使用*括号符号*，我们将属性名(键)作为字符串传入:

[![Bracket notation](img/a53454b7e566ae4c7bd39b1634ce12a7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1pckp07x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/rKhtm07/Group-6.png)

**分配给一个属性**

对象是**可变的**，这意味着我们可以*更新*它们。我们可以使用*点或括号符号*来选择属性，在赋值操作符的帮助下，我们可以添加新的键值对(如果没有同名的属性)或更改现有的属性(如果属性已经存在)。我们还可以用删除操作符从对象中删除一个属性。

[![carbon-3](img/300ab7c2bc965436e1fa40e259e8f020.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mOYBfqUM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/rxpsN05/carbon-3.png)

**作为属性的方法**

我们也可以在我们的对象文字中包含*方法*；使用 ES6 语法，我们可以将它们包含在对象中，如下所示:

[![carbon-4](img/70c2d8a1fb85939ca6dd6b18241b9aaf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M7w3uTVF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/WBBJLJN/carbon-4.png)

* *路过什么？**

在 JS 中，当一个函数被调用时，它直接将变量的值作为参数传递。在函数内部改变变量不会影响传递的变量。然而，对象是通过引用传递的**。当我们将一个赋给对象的变量传递给一个函数时，这被解释为指向内存中保存该对象的空间。因此，函数可以永久更改该对象。**

[![carbon-5](img/2cdc42c929f8222648acab449fadb344.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--quPK05Vo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/ZLX6Jx6/carbon-5.png)

**遍历对象**

JavaScript 为我们提供了...语句循环访问对象的属性。

[![carbon-6](img/d32c9f21d83b87820846f96147c0f486.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b0xb0dYG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/DQv9pRz/carbon-6.png)

参考资料:

[对象](https://javascript.info/object)
[JavaScript 对象](https://www.w3schools.com/js/js_objects.asp)
[使用对象](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Working_with_Objects)
[JS 中创建对象的 4 种方法(附示例)](https://www.developerdrive.com/2018/11/4-ways-to-create-an-object-in-javascript-with-examples/)