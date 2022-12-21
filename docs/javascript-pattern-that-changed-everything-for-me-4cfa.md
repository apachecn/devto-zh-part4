# Javascript 模式改变了一切(对我来说...)

> 原文：<https://dev.to/kristijanfistrek/javascript-pattern-that-changed-everything-for-me-4cfa>

### **前言**

大家好，欢迎来到我的第一篇开发帖子！
我成为这个社区的成员已经有一段时间了，最终决定贡献自己的力量&分享我对自己感兴趣的主题的想法。

我仍然在学习所有我可以使用即将到来的模式的方法，所以如果你看到一些不好的东西，请随时纠正我。

[![](img/2d450581698110f05aff9c925920e828.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FAIR9xpq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0itpe03ifven1ssth3oq.jpg)

### **这个帖子是关于什么的？**

简短的目录:

1)什么是生活？改变一切的 JS 模式...
2.1)优点
2.2)缺点
3)例子
4)结束语

让我们开始吧！

### **1。生命**

在我们开始解释任何种类的 javascript 设计模式之前，我认为有必要提一下 IIFE。

什么是生活？

IIFE 代表*立即调用的函数表达式*或自动执行的匿名函数。
简单来说，就是文件一执行就调用的一段代码。
它看起来像这样:

```
 (function() {

})(); 
```

用不那么简单的话来说，这是一种将函数封装在一个范围内的方法，这种方法将使函数远离全局范围，这在 javascript 世界中可能是一个棘手的地方。

它主要由两部分组成:

1.  第一部分是包含在分组操作符 **()** 中的匿名函数，它限制对函数中数据的访问，正如我们之前所说的，使其远离全局范围。
2.  第二部分创建立即执行的函数表达式**()；**JavaScript 引擎将通过它直接解释函数。

如果你对此感到好奇并想了解更多，因为我想在这里强调 javascript 模式，你可以阅读更多:[https://developer.mozilla.org/en-US/docs/Glossary/IIFE?source=post_page -](https://developer.mozilla.org/en-US/docs/Glossary/IIFE?source=post_page---------------------------)

### **2。改变一切的 Javascript 模式**

一个改变了一切的模式，至少对我来说，是:*鼓声*

**显示模块模式**

示例:

```
 var module = (function() {

    function _setName() {
       console.log("My name is : Slim Shady");
    }

    return {
       init: _setName
    };

})();

module.init(); 
```

你可能会看到一些熟悉的东西，如果你的猜测是生活，那么你是完全正确的。

揭示模块模式，就像许多其他 javascript 模式一样，利用 life 在全局范围内隐藏代码的功能，并使您具有更高的一致性和可重用性。

您在 IIFE 范围内声明的所有属性本质上都是私有的，并且您决定您将在我们代码的 **return** 段中向外部公开什么。

现在让我们来分解上面的例子。

1)我们用模块的名字声明了一个对象，并在其中保存了一个对我们的 IIFE 函数的引用。

```
 var module = (function() {

})(); 
```

2)下一步是创建名为 **_setName()** 的私有函数，在我们的例子中，它输出一个通用结果。
我更喜欢用下划线来命名我的私有属性，这有助于我将它们与其他属性区分开来，并使调试更容易。

```
 var module = (function() {

    function _setName() {
       console.log("My name is : Slim Shady");
    }

})(); 
```

3)我们示例的关键部分来自于表单 a **return** 语句，其中我们将私有属性的功能公开为公共属性。

因为在这个例子中，我们只有一个私有属性，所以我们只有一个公共属性，尽管根据函数的复杂程度，例子可能会有所不同。

```
 var module = (function() {

    function _setName() {
       console.log("My name is : Slim Shady");
    }

    return {
       init: _setName
    }

})(); 
```

最后一步是通过我们的**模块**对象调用我们需要的任何东西。

通过调用我们的**模块**对象，我们可以访问我们在*返回*范围内作为公共返回的所有内容。

因此，如果我们键入 **module.init()** ，在我们的控制台中，我们将看到来自 **_setName()** 函数的控制台日志。
这是因为我们的 *init* 属性存储了 **_setName** 函数可能保存的任何值。

```
 var module = (function() {

    function _setName() {
       console.log("My name is : Slim Shady");
    }

    return {
       init: _setName
    }

})();

module.init();

// Console log
// My name is : Slim Shady 
```

要了解这个简单示例之外的更多信息，请查看下面的链接，深入分析*揭示的模块模式*。

1.  [https://addyosmani . com/resources/essentialjsdesignpatterns/book/# revealingmodulepatternnjavascript](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#revealingmodulepatternjavascript)
2.  [https://gist . github . com/zcaceres/bb 0 EEC 99 c 02 DD a6 AAC 0 e 041d 0d 4d 7 BF 2](https://gist.github.com/zcaceres/bb0eec99c02dda6aac0e041d0d4d7bf2)
3.  [https://developerslogblog . WordPress . com/2017/11/05/JavaScript-quick-guide-to-the-disclosing-module-pattern/](https://developerslogblog.wordpress.com/2017/11/05/javascript-quick-guide-to-the-revealing-module-pattern/)

**优势**

这种模式的最大优点是它允许我们的脚本的语法更加一致。这也使得在模块的末尾可以更清楚地看到哪些函数和变量可以被公开访问，从而降低了可读性。

**缺点**

这种模式的一个令人不快的缺点是，如果一个私有函数引用了一个公共函数，那么如果需要一个补丁，这个公共函数就不能被覆盖。这是因为私有函数将继续引用私有实现，并且该模式不适用于公共成员，只适用于函数。

所以在使用这种模式时要小心，因为用揭示模块模式创建的模块可能比用原始模块模式创建的模块更脆弱。

### **3。示例**

下面是一些使用中的模块揭示模式的例子。
有些例子是我的一些项目直接使用了 MRT，有些是现场编的。

希望你会发现它们很有帮助！

**示例#1 -页脚组件**

这段代码显示了一个包含页脚结构的模块，并且基本上创建了一个组件，您可以在整个网站中重用该组件，并为自己节省了几行 HTML 代码。

```
 var footer = (function() {

    var _footerDesign = function() {

        var html = '';
        html += '<div class="footer-content text-center">';
        html += '<small><b>Copyright &copy; Kristijan Fištrek</b></small>';
        html += '</div>';

        return html;
    }

    var render = function() {
        var footerContainer = document.querySelector('footer');
        footerContainer.innerHTML = _footerDesign();
    }

    return {
        init: render
    }

})();

footer.init(); 
```

**示例 2 -数据存储**

这个例子描述了一种定义特定数据集并保持其私有结构的方法。它唯一公开的方面是返回的对象。

```
 var blogsContainer = (function() {

    var blogs = [
        {
            id: 1,
            name: 'Welcome to my first blog!',
            description: 'Allow me to introduce myself & the content you can expect from me in my blogs. Primarily I will focus on delivering tech driven content, mostly about web development, but I have few ideas about project organization, agile methodology & much more!',
            imgURL: 'assets/img/blogs/1.Welcome/1.Welcome-header.jpg',
            destination: 'pages/blogs/welcome-blog.html'
        }
    ]

    var initialize = function() {
        var contentOfBlogs = blogs;
        return contentOfBlogs;
    }

    return {
        data: initialize
    }
})(); 
```

**例 3——阿迪·奥斯马尼例**

```
 var myRevealingModule = (function () {

        var privateCounter = 0;

        function privateFunction() {
            privateCounter++;
        }

        function publicFunction() {
            publicIncrement();
        }

        function publicIncrement() {
            privateFunction();
        }

        function publicGetCount(){
          return privateCounter;
        }

        // Reveal public pointers to
        // private functions and properties

       return {
            start: publicFunction,
            increment: publicIncrement,
            count: publicGetCount
        };

    })();

myRevealingModule.start(); 
```

### **4。结束语**

我为这篇长文道歉，我真的想涵盖我认为重要的一切。由于这是我第一次在 Dev.to 上写东西，尽管我非常小心，我相信还是有一些错误出现在内容中。

揭示模块模式是一个应该明智使用的强大工具，它并不适合所有场合，但它可能会在许多其他场合证明自己是有用的。

你觉得这节课怎么样？你认为我覆盖的够吗？
你还想让我介绍什么？

下面往下说吧。

欢呼吧，编码快乐。