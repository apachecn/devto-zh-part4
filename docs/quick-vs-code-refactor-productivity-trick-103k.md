# 快速 VS 代码重构/生产力技巧

> 原文：<https://dev.to/chiangs/quick-vs-code-refactor-productivity-trick-103k>

所以我正在浏览 ***Dev.to*** ，就像我通常在等待我的构建过程完成时所做的那样(🐱‍👤)然后我看到了这个帖子:

[![hexrcs](img/bd14ee25575a4e09e385e08bc5cba0f1.png)](/hexrcs) [## 提高工作效率的 5 个 Visual Studio 代码技巧

### 李晓茹 8 月 8 日 194 分钟阅读

#productivity #vscode #tutorial #tools](/hexrcs/5-visual-studio-code-tricks-to-boost-your-productivity-27a4)

我想我会分享我的最爱之一。

通常，当我需要创建一个对象并作为一个测试或者除了一个单独的字符之外有共同的值时，我会使用这个流程。它还可以快速重构代码片段。

[![demo](img/406da08bd4332852f984fbd8ccc0b9d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--g0y6W0NV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6yh7h8nrs9ek9unt5msa.gif)

这里有一个例子，假设我有一个对象数组，我希望数组中每个对象的值分别是' test1 '，' test2 '，' test3 ':

```
const test = 'test';

const objectArray = [
      {
        prop1: 'test`,
        prop1: 'test`,
        prop1: 'test`,
        prop1: 'test`,
      },
      {
        prop1: 'test`,
        prop1: 'test`,
        prop1: 'test`,
        prop1: 'test`,
      },
      {
        prop1: 'test`,
        prop1: 'test`,
        prop1: 'test`,
        prop1: 'test`,
      },
      ]; 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，你可以只使用一个多光标，并在最后输入数值，但这里有另一种方法，这有助于当它不那么容易。

您可以使用`ctr + h`用`test1`替换`test`，但是这可能会替换掉更多您想要替换的东西，比如`objArray`上面的字符串变量`test`。

所以我是这样做的:

1.  使用最合适的`shift`、`ctrl`和`arrow keys`组合选择代码块。
2.  `alt + l`(这通知 vs 代码你打算只在选中的块上做这个操作。
3.  `ctrl + h`(打开查找/替换对话框)
4.  键入要替换的值
5.  按 tab 键，键入要替换的值
6.  `ctrl + enter`。*更新:新版本的 VS 代码默认为`ctrl + alt + enter`，但可以在键盘快捷键中改回来
7.  对下一个块重复上述步骤

🔥对我来说，一旦模式变得自然，这是相当快的。

Voila❗现在应该是这样的:

```
const test = 'test';

const objectArray = [
      {
        prop1: 'test1`,
        prop1: 'test1`,
        prop1: 'test1`,
        prop1: 'test1`,
      },
      {
        prop1: 'test2`,
        prop1: 'test2`,
        prop1: 'test2,
        prop1: 'test2,
      },
      {
        prop1: 'test3`,
        prop1: 'test3`,
        prop1: 'test3`,
        prop1: 'test3`,
      },
      ] 
```

Enter fullscreen mode Exit fullscreen mode

你的手指永远不会离开键盘，干杯！🍻

如果你觉得这很有价值，请留下评论并关注我的[dev . to @ Chiang](https://dev.to/chiangs)和 [Twitter @chiangse](https://twitter.com/chiangse) ，🍻干杯！