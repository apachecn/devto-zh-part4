# “使用严格”；在 javascript 中

> 原文：<https://dev.to/midasxiv/use-strict-in-javascript-524e>

嘿伙计们，所以充分披露；我一直是一名产品工程师，我的任务是快速制作一个功能的原型，长话短说，我基本上向其他几个成员寻求帮助，因为我无法正确处理某个 POST 请求，几个小时后，因为他们都表示 POST 请求的方式非常正确，我决定调试应用程序，此时应用程序已经非常复杂和分支。就在我准备开始的时候，我的一个同事问他是否可以检查代码，当我滚动到 POST 请求时，我意识到我拼错了一个变量，我的同事发现了这一点，这时他告诉我关于“使用严格”的事情，所以我花了一点时间阅读它，并认为这是每个开始使用 JavaScript 的人都应该知道的事情。

“use strict”指令是 ECMAScript 版本 5(大约从 2011 年开始)中新增的，它强制程序(或函数)在“严格”的操作环境下工作。它不是一个语句，而是一个文字表达式，被早期版本的 JavaScript 所忽略。在严格模式下，更容易检测 JavaScript 静默错误，因为它们现在会抛出一个错误。这使得调试更加容易，开发人员出错的机会也减少了。除了 Internet Explorer 9，大多数现代浏览器都支持严格模式。

### 那么为什么“使用严格”到底是一个字符串呢？

如果旧浏览器看到“use strict”字符串，它会将其视为一个字符串，并简单地忽略它的执行，而不启用严格模式，因为它只是一个字符串 Internet Explorer 9 不会抛出错误，即使它不理解它，在新浏览器中，当它看到关键字 use strict 时，它就会知道将自己转换为严格模式操作。

### 启用严格模式！

严格模式有两种使用方式

*   用于全局范围

    ```
    // Whole-script strict mode syntax
    'use strict';
    var v = "Hi! I'm a strict mode script!"; 
    ```

*   用于功能

    ```
    // not strict
    function strict() {
        // Function-level strict mode syntax
        'use strict';
        return "Hi!  I'm a strict mode function! ";
    }
    // not strict 
    ```

### “严格模式”；报告以下错误:

*   使用变量，但不声明它。
*   使用对象，而不声明它。
*   使用保留关键字作为变量名。
*   不允许删除变量(或对象)。
*   不允许删除功能。
*   不允许复制参数名。
*   单词 eval 不能用作变量。
*   出于安全原因，不允许 eval()在调用它的范围内创建变量。

### 例子

1.  使用未声明的变量/对象。(帮助您拼写错误的变量)

```
'use strict';
var uniqueValue = 23;
uniquValue = 17;         // this line throws a ReferenceError due to the 
                                    // misspelling the variable

unknownValue = 25;      // this line also throws a ReferenceError as variable is not declared 
```

Enter fullscreen mode Exit fullscreen mode

将导致创建一个新的全局变量(如 unknownValue 和 uniqueValue ),而 unique value 将保持不变。在严格模式下，给未定义的变量赋值会引发 ReferenceError。

1.  使用保留字声明变量。(未来证明您的代码)

```
var let = 24;
console.log(let) // will output 24 
```

Enter fullscreen mode Exit fullscreen mode

```
"use strict";
var let = 24;
console.log(let) // will raise an error 
```

Enter fullscreen mode Exit fullscreen mode

为将来的 JavaScript 版本保留的关键字不能在严格模式下用作变量名。这些是:实现、接口、let、包、私有、受保护、公共、静态、产出。

1.  在严格模式下删除变量、对象和函数会引发错误。

```
"use strict";
var variable = 23;
var obj = { 'name':'mxiv' };
function func(p1, p2 ) {
    console.log("Hello");
}
delete variable; // will raise error
delete obj; // will raise error
delete func; // will raise error 
```

Enter fullscreen mode Exit fullscreen mode

1.  函数参数不能被删除并具有相同的名称。

```
"use strict";
function func(p1, p2 ) {
    console.log("Hello");
    delete(p1); // raises error
}

function func2(p1,p1) { // raises error
    console.log("hey");
} 
```

Enter fullscreen mode Exit fullscreen mode

1.  评估限制。

```
eval('var variable = 2;');
console.log(variable); // displays 2 as the variable is leaked out of the eval function. 
```

Enter fullscreen mode Exit fullscreen mode

使用严格模式时，eval 不会泄漏传递给它的表达式中声明的变量。因此，出于安全原因，在严格模式下，不允许 eval 在调用它的范围内创建变量，这就引出了本文的结尾。