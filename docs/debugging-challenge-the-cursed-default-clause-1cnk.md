# 调试挑战:被诅咒的默认条款

> 原文：<https://dev.to/sebastianstamm/debugging-challenge-the-cursed-default-clause-1cnk>

一个邪恶的女巫诅咒了我的 switch 语句，所以无论我放入什么，它总是执行 default 子句而不是 case 子句。

这是我在现实生活中遇到的问题，也仍然是我最喜欢的 bug。这里有一个你可以尝试的最小设置:[查看 codesandbox.io](https://codesandbox.io/s/wonderful-meadow-2ph63?expanddevtools=1&fontsize=14&module=%2Fsrc%2Findex.js)

你能查出是怎么回事吗？当我发现它的时候，我有一个启发性的顿悟时刻，所以我鼓励你去看一看！这篇文章的其余部分描述了这个问题，以及为什么会这样。如果你想自己找出答案，就不要在这里看了。

# 到底是怎么回事？

```
function handleError(error) {
  switch (error) {
    case 404:
      console.log("Document not found");
      break;

    case 500:
      console.log("Server Error");
      break;

    default:
      const error = {
        msg: "Got an unknown error code",
        timestamp: Date.now()
      };
      console.log("Unknown Error Incident", error);
  }
}

handleError(404); // should log "Document not found", but logs “Unknown Error Incident” 
```

Enter fullscreen mode Exit fullscreen mode

不管你用什么调用这个函数，它总是执行 default 子句。这很奇怪，因为有些情况下会包含其他值的输入，所以在这些情况下，它不应该转到 default 子句。

但是，您会注意到，代码本身很好。如果您将它复制到您的浏览器控制台并执行它，您将看到它在必要时进入 case 子句，并且仅在没有 case 子句匹配时使用缺省值。

问题不在于编写的代码，而在于执行的代码。

大多数项目使用一种叫做 transpiler 的东西。transpiler 获取 Javascript 代码并将其编译成其他 Javascript 代码。这是为了让你可以用所有很酷的新特性编写漂亮的现代 Javascript，并且仍然可以在 Internet Explorer 中运行。

这是一个非常棒的概念，但它也增加了另一个抽象层，可能会在代码中引起微妙的错误。这正是这里发生的事情。这是在编译后实际执行的代码:

```
function handleError(error) {
  switch (_error) {
    case 404:
      console.log("Document not found");
      break;

    case 500:
      console.log("Server Error");
      break;

    default:
      var _error = {
        msg: "Got an unknown error code",
        timestamp: Date.now()
      };
      console.log("Unknown Error Incident", _error);
  }
}

handleError(404); // should log "Document not found", but logs “Unknown Error Incident” 
```

Enter fullscreen mode Exit fullscreen mode

看一下这段代码，很明显这是不可能的，因为开关使用了一个还没有初始化的变量。这是 transpiler 中的一个实际错误:[https://github.com/babel/babel/issues/5378](https://github.com/babel/babel/issues/5378)

如果我们在 default 子句中重命名 error 变量，那么即使在 transpiled 代码中，一切都会正常工作。

# 这个怎么调试

幸运的是，像这样的错误非常罕见。现在您已经意识到 transpilers 会导致这样的问题，您已经离识别和修复它们更近了一步。

你可以采取一个简单的步骤来**在浏览器控制台**中运行未移植的代码。大多数时候，你需要简化你的代码，把它从你的业务应用程序中分离出来，但是构建一个最小的测试用例是一个很好的实践。

另一种方式是**检查传输过来的源代码**。在 chrome 控制台中，你可以点击“sourcemapped from”链接来查看实际执行的内容。

[![How to get to the transpiled code from the Chrome Console](img/183ef82b0076689b5a6d860e7029dbbe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FWC-mG3x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uy505jr7p8bt47y2qkrd.png)

你有没有遇到过让你在发现一个令人惊讶的解决方案之前挠头的 bug？我很想了解他们！此外，因为这是我的第一篇博文，如果你对我如何改进有任何想法或建议，也请与我分享！