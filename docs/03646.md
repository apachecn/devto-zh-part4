# 正确完成角度:第 1 部分—绑定和指令

> 原文：<https://dev.to/johnhenry/angular-done-right-part-1-binding-and-directives-21c7>

*最初发布:[https://medium . com/@ iamjohnhenry/angular-done-right-part-1-binding-and-directives-6112 a 43 aa1dc](https://medium.com/@iamjohnhenry/angular-done-right-part-1-binding-and-directives-6112a43aa1dc)T3】*

作者注:虽然我不再大量使用 angular 编程，而是倾向于使用更小、更模块化的库，但读者会发现本系列中介绍的思想很有用。

有棱角很难吧？

有一件事是肯定的，那就是 Angular 是一个巨大的。这是一个完整的应用程序框架，由包括指令、控制器、服务等组件组成。它笼罩在奇怪的新概念中，比如“*依赖注入*”和“*转子句*”。尽管它的整体吸引力很大，但任何第一次创建 angular 应用程序的人都必须承认，在能够创建任何有趣或有用的东西之前，他或她将面临一个陡峭的学习曲线。

这是网上大多数教程采用的方法。在这个系列中，我们采用不同的方法。我们将逐步建立在 angular 中最基本的概念上；首先介绍构建应用程序所需的最低要求，并在此基础上继续构建。

### 安装角形

我们将从一个非常基本的 HTML 页面开始。用下面的代码创建一个基本的 html 页面:

```
 <!doctype html>
    <html>
     <body>
     </body>
    </html> 
```

接下来，您需要下载并包含 angular.js 文件。你可以直接从 [Angular JS 主页](https://angularjs.org/)下载，或者，如果你喜欢，使用 [Bower 包管理器](http://bower.io/)。我不会详细说明具体如何做到这一点，但已经做到这一步，我怀疑你会有任何麻烦:)。

一旦你在你的应用程序中包含了 angular 文件，你的文件应该是这样的:

```
 <!doctype html>
    <html>
     <body>
     <script src=”path/to/angular.js”></script>
     </body>
    </html> 
```

### 自举

**bootstrapping**是一个术语，用于将您的 angular 应用程序连接到网页。不要把它和流行的 [UI 框架](http://getbootstrap.com/)混淆了。

有两种引导应用程序的方法。最简单可靠的方法是在 html 元素中添加* *ng-app ** *指令*。该元素及其所有子元素将成为 angular 应用程序的一部分。

"*什么是指令？*”，你一定想知道…好吧，现在，就把它们想象成你可以添加到你的 html 中的属性，让你的应用程序与 angular 交互。我们将很快了解更多关于指令的内容，在本系列的后面部分。

We could put it on any element — the **tag; a **tag — but lets go ahead and put it on the top level “html” element. This way, the entire page will be available to our application.**** 

```
 **`<!doctype html>
    <html ng-app>
     <body>
     <script src=”path/to/angular.js”></script>
     </body>
    </html>`** 
```

 ****还有另一种方法来引导你的应用程序(它实际上使用了 [angular.bootstrap](https://docs.angularjs.org/api/ng/function/angular.bootstrap) 方法！)，但是我发现它更容易出错，所以我至少不会用那种方法……至少今天不会。

### 装订

— ng 模型和 ng 绑定

现在您已经设置了应用程序，绑定数据是您在 angular 中可以做的最简单的事情。尝试修改您的 html，如下所示:

```
 <!doctype html>
    <html ng-app>
     <body >
     <input ng-model=”input”> <span ng-bind=”input”></span>
     <script src=”path/to/angular.js”></script>
     </body>
    </html> 
```

您添加了两个新指令。** ng-model ** *指令*允许您将变量绑定到接收用户输入的标准 html 元素。这可以是 input 元素、textarea 元素或 select 元素。(如果您熟悉 jQuery，这可能会让您想起 [val](http://api.jquery.com/val/) 方法。)

ng-bind 指令做了一些类似的事情，但是它的工作方式是用绑定的变量替换任何 html 元素的内部 html。(如果你熟悉 jQuery，这可能会让你想起 [html](http://api.jquery.com/html/) 方法。)

**input** 是我们为要绑定的变量选择的名字，但是我们可以选择几乎任何东西。

注意:变量存在于应用程序的范围内，而不在页面上。javascript 中定义的变量不会被绑定。我们将在后面关于控制器和作用域的章节中学习这些变量的确切位置。

```
 <script>
     var input = “Sorry, I’m not available for binding.”
    </script> 
```

您还可以使用双花括号语法`“{{input}}”`，而不是 **ng-bind** ，在任何地方添加绑定，而不需要 HTML 元素:

```
 <!doctype html>
    <html ng-app>
     <body >
     <input ng-model=”input”><span>{{input}}</span>
     <script src=”path/to/angular.js”></script>
     </body>
    </html> 
```

如果您在浏览器中打开页面，您将看到一个空框，当您键入 box 时，您将看到它被绑定到它旁边的区域。

比如说，这可能对一个表单有用:

```
 <form>
     Name:<input ng-model=”user.name” ng-required=”true”>
     <br />
     Favorite Color:
     <select ng-model=”user.color”>
      <option value=”not available”>N/A</option>
      <option value=”red”>Red</option>
      <option value=”green”>Green</option>
      <option value=”blue”>Blue</option>
     </select>
     <div style=”background-color:{{user.color}}”></div>
     <button>Submit</button>
    </form>
    <p>Your name is
     <b ng-bind=”user.name || ‘Blank’”></b>
      and your favorite color is
     <b ng-bind=”user.color”></b>.
    </p> 
```

注意，**用户名**和**用户名**属性被绑定到表单的其他部分。

您可能已经在输入标签上发现了 **ng-required** 指令。这将阻止表单被提交，除非填写了所需的值。尝试提交下面的表单，而不输入姓名。(请注意:这可能并不适用于所有浏览器。)

能够在页面上执行计算也很有用。这里有一个简单的小费计算器。它有点偏离(检查那些舍入误差！)，但它开始向你展示你可以用 angular app 做什么。

```
 Bill: <input ng-model=”bill”>
    <br/>
    Tip Percentage: <input type=”number” ng-model=”tip”> %
    <br/>
    Total: $<span ng-bind=”(1 + (tip/100) ) * bill”></span> 
```

### 几更指令

-使用 ng-if、ng-show、ng-hide 显示和隐藏元素

方便的是，将 **ng-model** 附加到复选框或单选按钮允许你绑定到它的 **checked** 属性而不是它的值。

```
 <input type=”checkbox” ng-init=”checked=true” ng-model=”checked”>
    <span ng-show=”checked”>The box is checked</span>
    <span ng-hide=”checked”>The box is not checked</span>
    <span ng-if=”checked”> :) </span> 
```

我们可以用这个事实来演示一些新的指令。

虽然我没有使用它的单一推荐用例，但是 **ng-init**
指令允许你初始化一个变量的值——否则它开始为空。

**ng-if** 、 **ng-show** 和 **ng-hide** 指令用于显示和隐藏一个元素。区别在于 **ng-if** 将从页面中移除一个不可见的元素，而 **ng-show** 和 **ng-hide** 将简单地隐藏它。

-使用 ng-repeat 重复元素

最后一个有用的指令是 **ng-repeat** 指令，它允许遍历集合。

```
 <ul ng-init=”beatles = [
     {name : ‘John’},
     {name : ‘Paul’},
     {name : ‘George’},
     {name : ‘Ringo’}
     ]”>
     <li ng-repeat=”beatle in beatles”>
     <span ng-bind=”$index”></span>.<span ng-bind=”beatle.name”></span>
     </li>
    </ul> 
```

您会注意到，除了 ***beatle*** 属性之外， **ng-repeat * *指令还提供了一个** *$index** *属性，该属性也可以绑定到应用程序。这个* **$ index** *等于迭代的索引。

—使用过滤器修改数据

姑且不论，让我们先来看一看过滤器。

```
 <li ng-repeat=”beatle in beatles | orderBy:’name’”>
     <span ng-bind=”$index”></span>.<span ng-bind=”beatle.name”></span>
    </li> 
```

orderBy 可能看起来像一个指令，但它不是，它是一个过滤器。过滤器在某些指令内工作，以修改其中的数据。这个函数改变了数组中元素的顺序。你可以阅读更多关于这个和其他可用的过滤器[在这里](https://docs.angularjs.org/api/ng/filter)。像指令一样，过滤器是组成 angular 应用程序的大约九个组件之一，可以与其他组件一起编织成强大的应用程序。

过滤器和指令一样，是 angular 的核心组件之一。在接下来的文章中，我们会学到更多关于它们的知识。

### 结论

只需几个基本的内置组件，您就可以开始创建有趣的应用程序，几乎不需要任何代码。我很快会带着这个系列的另一个更新回来，但同时，开始使用 Angular 来制作一些很酷的东西！****