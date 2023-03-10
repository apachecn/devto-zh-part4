# YDKJS —对象和原型—第 3 部分

> 原文：<https://dev.to/nabendu82/ydkjs-objects-and-prototypes-part3-4p4e>

欢迎来到本系列的第 3 部分。

这个系列全部来自于 **Kyle Simpson** 的学习，也来自于 **Kaushik Kothagul** 来自 [Javabrains](https://www.youtube.com/user/koushks/playlists?view=50&shelf_id=1&sort=dd) 的令人惊叹的 youtube Javascript 系列，它也受到了**你不知道的 JS** 的影响。

让我们从前面离开的地方开始。考虑以下在 JS 中使用构造函数的代码。这里的问题是，每当我们用 new 关键字创建一个新变量时。为它们中的每一个都创建了 inflateTires 函数。

[![The problem with new keyword in constructors](img/fd23f03ec940c49db8ebdea760035c7d.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--Etxh23ti--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3856/1%2Ayoz1s0EjaSLowFa7i2xWmQ.png) *构造函数中新关键字的问题*

## 原型介绍

如你所知，在 JavaScript 中一切都是对象。所以每当我们创建一个函数，就有一个对象被创建。但实际上还有另一个被创建的对象，叫做原型对象。

考虑下面的例子。在下面的例子中 **foo** 拥有能够访问其原型的属性。该属性也被称为原型，它被写成 **foo.prototype**

[![The prototype example](img/2413c54abb426bb92270e5cd124e6851.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--6ktY70lx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/5760/1%2AFMsiZaew-yly1pC9dvMCqQ.png) *原型例子*

让我们考虑下面的例子来更好地理解它。每当创建一个函数时，都有两个对象，一个是函数对象，另一个是原型对象。现在，为了访问**原型**对象，我们在函数对象上有一个属性，也称为“**原型**”。

[![The prototype confusion](img/e45c4da21c610b02aa7eebe8fd84537b.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--n7OyFUGK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3948/1%2A8eQqbmF4P3gPTixx4IUtXQ.png) *原型混乱*

当我们用**新关键字**调用函数 **foo** 时，有趣的事情发生了。它创造出被称为 ****原型机**** 的东西。这是 JavaScript 引擎使用 new 关键字为每个函数调用创建的。

[![calling foo with new keywords](img/4940a6fcee4e0d1ac751e40bb694fe13.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--brZwpW9M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/5760/1%2AfoQUWCObJUGV7ylHiWC6mg.png) *用新关键字调用 foo*

让我们看看下面的图表，找出这个看起来怪异的 ****原型**** 属性是什么。实际上，每当我们用 new 关键字调用一个函数时，就会创建一个新的对象，它具有这个 ****proto**** 属性，并指向该函数的对象。如果你用 new 关键字再次调用这个函数，它将再次创建一个类似的东西。

[![The __proto__ property](img/43c6703febbcb35fb6db8a77c082de65.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e0pVSRfX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3864/1%2AYWY14ZxD7zNeVqg2YyK0vg.png)***原型**属性*

让我们看一个例子来学习你在图中看到的东西。我们创建了一个空函数 **foo** 。然后在函数上使用 **new** 关键字创建了一个 **newFooObj** 。现在使用属性 prototype 创建了一个包含一些字符串的测试变量。现在我们可以使用**newfoobj**object****proto****属性来访问测试变量。

[![foo.prototype === newFooObj.__proto__](img/f44af1b7082bceb9eef2ee1f8956abf4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xtKX40bK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/5760/1%2ANvT1T6AwdskCtL0w563UyQ.png)*foo . prototype = = = newfoobj。**原型机**T6】*

现在 ****proto**** 属性还有一个好处。我们首先检查我们的**new foobj**是否有一个 **hello** 变量。它没有一个，所以我们使用 ****proto**** 属性创建一个。现在 ****proto**** 属性有一个 **hello** 变量，这是显而易见的。但是我们也可以使用**newfoobj . hello**直接访问它。这是因为当 JavaScript 编译器运行时，它首先试图在 **newFooObj** 中找到 **hello** 变量。如果它找不到，它检查原型对象，在那里它能够找到它。

[![The benefit of Prototype](img/7ca61cdcde0eee00ffd1c0931b6f0804.png) ](https://res.cloudinary.com/practicaldev/image/fetch/s--UjieRnCg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/5760/1%2AE1wxy99icAJ4JHvXWB7cAA.png) *原型的好处*

本系列的第 3 部分到此结束。