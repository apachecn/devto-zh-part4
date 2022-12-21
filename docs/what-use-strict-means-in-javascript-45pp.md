# JavaScript 中“使用严格”的含义是什么

> 原文：<https://dev.to/iamismile/what-use-strict-means-in-javascript-45pp>

***【用严】***

长期以来，JavaScript 的发展没有面临兼容性问题。这是因为 JavaScript 在不改变旧功能的情况下增加了新特性。它的好处是永远不会破坏现有的代码。

这个案例一直到 2009 年 ECMAScript 5 (ES5)出现。在那个版本中，增加了新的特性，并且修改了一些现有的特性。但是大多数修改在默认情况下是关闭的，以保持旧代码的工作。如果我们想让整个脚本以现代的方式工作，我们需要显式地启用它们。

为此我们可以使用一个看起来像字符串的指令**“use strict”**或**‘use strict’。**

总是在我们的脚本文件顶部使用**“use strict”**，这是推荐的。

```
// At top of the file
"use strict";

// code
// code
//code

// code will work modern way 
```

Enter fullscreen mode Exit fullscreen mode

我们可以在函数体的开头使用**“use strict”**而不是在脚本的顶部。它将仅在该功能中启用**严格**模式。

```
// default mode

(function(){
  "use strict";

  // strict mode
})();

// default mode 
```

Enter fullscreen mode Exit fullscreen mode

但是还是建议在文件顶部使用**“use strict”**。