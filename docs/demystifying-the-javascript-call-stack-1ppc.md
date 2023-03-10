# 揭开 JavaScript 调用栈的神秘面纱

> 原文：<https://dev.to/ogwurujohnson/demystifying-the-javascript-call-stack-1ppc>

JavaScript 是一种单线程、单并发语言，这意味着它一次可以处理一项任务或一段代码。它有一个单独的调用栈，与其他部分一起构成了 Javascript 并发模型(在 V8 内部实现)。

本文将重点解释什么是调用栈，以及它为什么重要，为什么 JavaScript 需要它。

> ***在最基本的层面上，调用栈是一种利用后进先出(LIFO)原则来存储和管理函数调用的数据结构。*T3】**

由于调用堆栈是单个的，所以函数执行是从上到下一次执行一个，这使得调用堆栈是同步的。在管理和存储函数调用时，调用栈遵循后进先出原则(LIFO ),这就要求在调用栈被弹出时，最后被推入调用栈的函数执行总是被清除的。

> ***调用栈在 JavaScript 应用中有什么作用？JavaScript 是如何利用这个特性的？*T3】**

当 JavaScript 引擎运行您的代码时，会创建一个执行上下文，这个执行上下文是创建的第一个执行上下文，它被称为`Global Execution Context`。最初，这个执行上下文将由两件事组成——一个全局对象和一个名为`this`的变量。

现在，当在 JavaScript 中执行一个函数时(当调用一个标签后带有`()`的函数时)，JavaScript 会创建一个名为`local execution context`的新执行上下文。因此，对于每个函数执行，都会创建一个新的执行上下文

如果您想知道，执行上下文就是 JavaScript 代码执行的环境。执行上下文包括:

*   执行的线程和
*   本地记忆

由于 JavaScript 将创建一大堆执行上下文(或执行环境)，而它只有一个线程，那么它如何跟踪它的线程应该在哪个执行上下文中以及应该返回哪个执行上下文呢？我们简单说一下`call stack`。

[![the call stack image](img/93e70a1c93f070eb61edb6051549d06c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LqQZAZPH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/piahcfqczl77rulnqejf.png)

当一个函数被执行时，JavaScript 为这个函数的执行创建一个执行上下文。新创建的执行上下文被推送到调用堆栈。现在，无论调用堆栈的顶部是什么，JavaScript 线程都将驻留在那里。最初，当 JavaScript 运行应用程序并创建`global execution context`时，它将这个上下文推入调用堆栈，由于它似乎是调用堆栈中的唯一条目，JavaScript 线程就生活在这个上下文中，并运行在那里找到的每个代码。

现在，当一个函数被执行时，一个新的`execution context`被创建，这次是`local`，它被推入调用堆栈，在那里它占据了顶部位置，并且自动地，这是 JavaScript 线程将移动到的位置，运行它在那里找到的指令。

JavaScript 知道一旦到达返回语句或花括号，就该停止执行函数了。如果一个函数没有显式的返回语句，它将返回`undefined`，不管怎样，都会发生一个返回。

因此，当 JavaScript 在执行函数的过程中遇到 return 语句时，它会立即知道这是函数的结尾，并擦除已创建的执行上下文，同时，被擦除的执行上下文会从调用堆栈中弹出，JavaScript 线程会继续执行占据顶部位置的执行上下文。

为了进一步说明这是如何工作的，让我们看一下下面的代码，我会告诉我们它是如何执行的。

```
 function randomFunction() {
        function multiplyBy2(num) {
          return num * 2;
        }
        return multiplyBy2;
      }

      let generatedFunc = randomFunction();
      let result = generatedFunc(2);
      console.log(result) //4 
```

Enter fullscreen mode Exit fullscreen mode

通过上面的小函数，我将说明 JavaScript 如何运行应用程序，以及它如何利用调用堆栈。

JavaScript 第一次运行这个应用程序时，如果我们记得全局执行上下文被推入调用堆栈，对于我们上面的函数，同样的事情发生了，让我们走一遍；

1.  `global execution context`被创建并被推入`call stack`。
2.  JavaScript 在内存中创建一个空间来保存函数定义，并将其分配给一个标签`randomFunction`，此时函数只是被定义了，并没有被执行。
3.  下一个 JavaScript，来到语句`let generatedFunc = randomFunction()`，因为它还没有执行函数`randomFunction()`,`generatedFunc`将等同于`undefined`。
4.  现在，由于 JavaScript 遇到了括号，这表示要执行一个函数。它执行函数，从前面我们记得，当一个函数被执行时，一个新的执行上下文被创建，同样的事情发生在这里。一个我们称之为`randomFunc()`的新的执行上下文被创建，它被推入调用栈，占据顶层位置并推动全局执行上下文，在调用栈中我们称之为`global()`，使 JavaScript 线程驻留在上下文`randomFunc()`中。
5.  由于 JavaScript 线程在`randomFunc()`中，它开始运行在其中找到的代码。
6.  它首先要求 JavaScript 在内存中为一个函数定义腾出空间，并将其分配给标签`multiplyBy2`，由于函数`multiplyBy2`尚未执行，它将转移到 return 语句。
7.  当 JavaScript 遇到 return 关键字时，我们已经知道会发生什么了，对吗？JavaScript 终止该函数的执行，删除为该函数创建的执行上下文并弹出调用堆栈，从调用堆栈中移除该函数的执行上下文。对于我们的函数来说，当 JavaScript 遇到 return 语句时，它返回它被指示返回到下一个执行上下文的任何值，在这种情况下，它是我们的`global()`执行上下文。

在语句`return multiplyBy2`中，值得注意的是，返回的不是标签`multiplyBy2`，而是`multiplyBy2`的值。还记得我们曾要求 JavaScript 在内存中创建一个空间来存储函数定义，并将其分配给标签`multiplyBy2`。所以当我们返回时，返回的是函数定义，它被赋值给变量`generatedFunc`，使得`generatedFunc`成为下面的:

```
 let generatedFunc = function(num) {
        return num * 2;
      }; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们说，JavaScript 应该在内存中为之前称为`multiplyBy2`的函数定义创建一个空间，这次将它分配给变量或标签`generatedFunc`。

在下一行，`let result = generatedFunc(2)`，我们执行`generatedFunc`引用的函数定义(之前是我们的`multiplyBy2`)，然后发生了:

1.  变量 result 等于`undefined`，因为此时它引用的函数还没有执行。
2.  JavaScript 创建了另一个执行上下文，我们称之为`generatedFunc()`。当创建本地执行上下文时，它由本地内存组成。
3.  在本地内存中，我们将把参数`2`赋给参数`num`。
4.  我们不要忘记，本地执行上下文`generatedFunc()`将被推入调用堆栈，假设在顶部位置，JavaScript 线程将运行在其中找到的每个代码。
5.  当 JavaScript 遇到 return 语句时，它对`num * 2`求值，由于`num`引用最初存储在本地内存中的`2`，它对表达式`2*2`求值并返回。
6.  在返回表达式`2*2`的求值时，JavaScript 终止`generatedFunc`函数的执行，返回值存储在变量`result`中，然后弹出调用堆栈，移除`generatedFunc()`上下文并让线程返回到`global()`上下文。所以当我们`console.log(result)`时，我们得到`4`。

### 总之:

从这篇文章中得到的关键信息是:

*   对于每一次函数执行，都会创建一个新的执行上下文，它会被弹出到调用堆栈中，JavaScript 线程就是通过它来学习从哪个环境中获取指令并执行。

> **感谢您的阅读。如果这篇文章是有帮助的，请给它一些反馈和分享，这样其他人可以找到它。我也想看看你的评论。**

***演职员表*** `FreecodeCamp` ***为本文使用的图片***