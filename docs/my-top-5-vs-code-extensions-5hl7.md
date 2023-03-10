# 我最喜欢的 5 种代码扩展

> 原文：<https://dev.to/codingsam/my-top-5-vs-code-extensions-5hl7>

自从我开始编码以来，我已经使用了许多文本编辑器，如 Sublime、Atom 甚至 vim(我知道这有点不同，但无论如何它都可以被认为是一个编辑器)。我使用 VS 代码已经有几年了，我真的非常喜欢它。

我喜欢这个编辑器，因为它有一个非常好的性能(至少到目前为止我没有任何问题)，它是免费的，你可以安装很多扩展。这些扩展可以让你的日常工作与众不同，当然，让你的编辑器看起来棒极了！

<figure>[![](img/8927dc8a433d2e74973aac7a0083efd6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OTeK24jN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AQD7srEXJUpS8KSuRu8WJhg.png) 

<figcaption>只是我最喜欢的文字编辑截图</figcaption>

</figure>

我将与你分享我对 VS 代码的 5 大扩展。你会发现只有当你写 Javascript 代码时，这两个工具才是有用的，我就是这样做的...我很乐意！

### 分机

[**GitLens**](https://github.com/eamodio/vscode-gitlens)

如果你正在做一个使用 git 作为 VCS(版本控制系统)的项目，你会喜欢这个的。这基本上告诉你谁在你的光标所在的行上做了给定的改变。它显示了提交消息和进行更改的用户。

<figure>[![](img/927a7de6385a86e4a07dc0757f2a13bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JRTUQ8tc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/858/1%2Af46Iy-ZRSL22GdaAjYepDQ.gif) 

<figcaption>使用 gitlens 逐行查看提交消息</figcaption>

</figure>

这个扩展还在左侧菜单中增加了一个新的选项，在这里你可以看到更多的细节，提交日志，比较文件，查看分支等等。

[![](img/6db6038e07ffa9a97d3dba37ee01030c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---RnxqjUq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AyFYqfp_lPA4zO-vtUisJpg.png)

[**vscode-icons**](https://marketplace.visualstudio.com/items?itemName=robertohuertasm.vscode-icons)

这本书也适用于几乎所有类型的软件开发人员。它只不过是一个图标包。让我给你看一个例子…

<figure>[![](img/40ed1fffb02f90d2a38eb89750a889bf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6g-xLRVW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/406/1%2AcSTfrWStf9cmGh_nzlNj3Q.png) 

<figcaption>使用 vscode-icons 扩展</figcaption>

</figure>

的图标显示示例

如你所见，它不仅仅是查看每个文件的扩展名和使用正确的图标。有些文件甚至没有扩展名，例如“许可证”文件。但是不知何故，扩展为这个文件使用了不同的图标，尽管它只是一个文本文件。“Dockerfile”也有了自己的图标！它真的不仅仅是文本编辑器的图标主题！

[**括号对着色**](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer)

有时候你有很多“*(*)*{*”，很难找到对应的“*)*”*}*”关闭字符。我用这个扩展来解决这个第一世界的问题。它为每对“()”和“{}”应用不同的颜色。

<figure>[![](img/1eb32220dced3ed34b329f020b5a56e1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--V3FQmtpu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/922/1%2A6p_Zzisd3glWVHBy1rAx4Q.png) 

<figcaption>每对“()”和“{}”会得到不同的颜色</figcaption>

</figure>

太棒了，对吧？

[**ESLint**](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

[ESLint](https://eslint.org) 是一个指定代码标准的工具。一些问题，如:

*   我应该在作业中的“=”周围划一个空格吗？

```
const a = b; 
```

或者

```
const a=b; 
```

*   我应该在 *if* 关键字和 *if* 语句中的条件之间留一个空格吗？

```
if (condition) //... 
```

或者

```
if(condition) //... 
```

还有更多，可以通过这个工具来回答…

所有这些代码标准都可以由 ESLint 定义和执行。您只需要将这个工具集成到您的项目中，并在一个`eslintrc`文件中设置您的规则。如果违反了某些规则，您的构建工具将会引发错误。但是你可以做得更好。您可以在 VS 代码中弹出所有这些错误。有一个扩展可以做到这一点。它会查找一个配置文件，当您键入时，您的文本编辑器会突出显示违反规则的错误。其中一些错误可以自动修复。只需按下`Ctrl` + `Shift` + `P`(在 PC 上)或`Cmd` + `Shift` + `P`(在 Mac 上)并输入“修复”，直到你找到一个选项，上面写着“*修复所有可自动修复的问题*”。

<figure>[![](img/c930f9b8242fa7e9f18c8a87c9de78fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x8TuviWz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/940/1%2A6MQHAAbiDLkV84sqHezBeA.gif) 

<figcaption>使用自动修复来修复代码标准问题</figcaption>

</figure>

[**导入成本**](https://marketplace.visualstudio.com/items?itemName=wix.vscode-import-cost)

当你从一个包中导入一些东西时，这意味着你包含了一些外部代码。如果您有一个生成包文件(所有代码都在一个 JS 文件中)的构建过程，那么您使用的每个库都会增加最终包的大小。有了这个扩展，每个 import 语句都会被评估，它会告诉您该导入的开销，就文件大小而言。因为在这里…大小很重要！

<figure>[![](img/c059460f1bdc77d8336d47dc94436033.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bs0foEDd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/730/1%2An6WqZM8glgh4xHCY-YOLSQ.png) 

<figcaption>只是导入成本扩展的一个例子</figcaption>

</figure>

### 结论

我在这篇文章中展示的扩展的好处并不明显…直到你开始使用它们！即使他们中的一些只是解决一些第一世界的问题，我认为他们都为我的文本编辑器和我的日常工作增加了价值。希望你能从这篇帖子里得到一些牛逼的东西。

你用 VS 代码吗？有没有其他的编辑器，比如`vim`？你使用什么分机？在评论中分享你最喜欢的 5 个扩展吧！