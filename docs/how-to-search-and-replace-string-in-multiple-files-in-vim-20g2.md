# 如何在 Vim 中跨多个文件搜索和替换字符串

> 原文：<https://dev.to/iggredible/how-to-search-and-replace-string-in-multiple-files-in-vim-20g2>

*关注 [@learnvim](https://twitter.com/learnvim) 了解更多 vim 技巧和窍门！*

VSCode 和 Atom 等流行编辑器的一个有用特性是能够搜索和替换项目中许多文件的字符串。如果 Vim 能做同样的事情不是很好吗？

幸运的是，Vim 也有能力做到这一点，尽管一开始可能并不直观。最好的事情是，我们将不必安装任何插件来做到这一点！我将展示如何在 Vim 中跨多个文件进行搜索和替换的两种方法。但在此之前，让我们先回顾一下基本原理。

# 单文件搜索和替换(替换)

如果您知道 Vim 的基本替代，请随意跳到下一节。

vim 中的替换由:
完成

```
:%s/stringToBeReplaced/replacementString/g 
```

Enter fullscreen mode Exit fullscreen mode

Regex 也可以。我将在这里跳过正则表达式的细节，但是你可以在`:h substitute`阅读更多关于它的内容。

# Args

您知道 Vim 有接受文件列表的`:ar[gs]`命令吗？

让我告诉你我们如何使用参数。让我们假设我们的目录包含如下内容:

```
├── index.js
├── server.js 
```

Enter fullscreen mode Exit fullscreen mode

为了捕捉`index.js`和`server.js`，我们可以做`:args *.js`。现在输入`:args`将显示

```
[index.js] server.js 
```

Enter fullscreen mode Exit fullscreen mode

要转到下一个参数，请键入`:n[ext]`和`:prev[ious]`转到上一个参数列表。

我们也可以使用 glob against args 进行递归搜索。以下是使用参数的更多方法:

```
:args index.js server.js // captures only index and server js files
:args **/*.js            // captures every js files
:args **                 // captures everything 
```

Enter fullscreen mode Exit fullscreen mode

用`:s`和`:args`武装起来，我们准备好执行我们的替换！

# 方法 1:使用 argdo

现在我们有了所有的参数，我们可以执行我们的常规替换。
回想一下我们的替换法，我们就把它和`argdo`结合起来。

```
:argdo %s/stringToBeReplaced/replacementString/g | update 
```

Enter fullscreen mode Exit fullscreen mode

这将所有 foo 替换为 bar，并将其应用于所有 args。

如果你对 argdo 感到困惑，你并不孤单。直到不久前，我才听说过它。这其实很简单。如果我们看一下`:h argdo`，它确实:

> 对参数列表中的每个文件执行{cmd}...

简而言之，它应用了你传递给所有参数列表的所有内容，也就是所有的 js 文件。我们传递的是替换命令。

`update`是可选的。它保存所有被替换的文件。我这样做是因为我通常会忘记保存它们。

# 方法 2:用宏执行替换并重复

录制宏时，在一个文件上执行替换，并在所有参数上重复宏。

假设相同的文件夹结构和参数，这是如何做到的:

```
qq                                          // start macro in q register
%s/stringToBeReplaced/replacementString/ge  // the e flag tells vim to not throw an error if there is no match
:wnext                                      //important. This is similar to `:next`, but it also writes the current file
q                                           // stop macro
999@q                                       //repeat this macros either 999 times or to remaining files. 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！

你有没有另一个用来进行全局搜索和替换的技巧？我很想听听！

[![thumbs-up](img/09df05446019ff1c9487d2cd292d20a0.png)](https://i.giphy.com/media/XreQmk7ETCak0/giphy.gif)

*Btw，这里有个好玩的部分。第二种方法实际上列在 Vim 的“聪明绝招”用户手册里面(适用于 vim 7.3 及以上版本和 Neovim)。如果你有时间，你应该看看整个章节。这是相当可读，简短，并包含超级有趣的 vim 黑客！！*