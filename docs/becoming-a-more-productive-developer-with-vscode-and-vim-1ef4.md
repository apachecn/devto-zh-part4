# 使用 VSCode 和 Vim 成为更高效的开发人员

> 原文：<https://dev.to/vintharas/becoming-a-more-productive-developer-with-vscode-and-vim-1ef4>

> 这是《用 VSCode 和 Vim 提升你的编码能力》[一书的](https://www.barbarianmeetscoding.com/boost-your-coding-fu-with-vscode-and-vim)[介绍](https://www.barbarianmeetscoding.com/boost-your-coding-fu-with-vscode-and-vim/introduction/)

**Visual Studio 代码超棒**。它提供了无与伦比的用户体验，对多种语言和开发生态系统提供了强大的支持。它有很好的默认值，非常容易使用和开始。

Vim 太棒了。其模态特性和文本编辑功能使其在其他编辑器中独一无二。Vim 提供了完全不同的文本编辑能力、速度和准确性。

两者的结合不能比**令人惊讶的超级棒**差太多了(是的，我就是这么做的)。

在接下来的日子里，随着您阅读这些页面并进行练习，您将会极大地提高您的 Visual Studio 编辑技能。通过结合 Vim 和 Visual Studio 代码的优势，您将能够使您的大脑和计算机之间的接口变得非常薄，使您的想法无缝地具体化为代码。

以下是我们将在本书中涉及的内容:

*   Vim 是什么？为什么在 VSCode 中使用 Vim？
*   如何在 VSCode 中安装 Vim？
*   Vim 中的基本生存技能
*   通过核心 Vim 运动快速移动
*   使用 Vim 操作符和动作以思维的速度进行编辑
*   维姆的秘密语言
*   插入文本 a la Vim
*   在可视模式下选择文本
*   利用寄存器复制和粘贴
*   用 Vim Surround 包围事物
*   使用潜行和 EasyMotion 插件，移动速度更快
*   创建自定义快捷方式，让您更加高效
*   使用 Vim 为多个游标增压
*   用宏创建可重用的编辑单元
*   将 VSCode 与 Neovim 集成在一起，以实现最大的出色性能

在这本书的结尾，你将能够进行精细的文本操作和强大的文本转换，这是你以前无法实现的。随着时间的推移，随着您练习并熟悉不同的 Vim 命令，您将会比以前更快、更熟练地进行编辑。

> 这本书没有假设任何 Vim 知识，所以如果你不熟悉 Vim 也不用担心。我将指导您了解有效使用 Visual Studio 代码和 Vim 所需的所有概念和技术。

## Vim 是什么？

[Vi](https://www.vim.org) 是一个古文编辑器。甚至比[世界的第一个时代](https://en.wikipedia.org/wiki/Vi)还要古老。它被设计成在被称为终端的装置上工作，这种终端具有非常不寻常但却富有灵感的以模态方式运行的特征。也就是说，它有一种插入文本的模式，另一种编辑文本的模式，另一种选择文本的模式，这样你就明白了要点。

Vi 的最新和最著名的化身是[Vim](https://en.wikipedia.org/wiki/Vim_(text_editor))(**V**I**IM**proven，前身为 **V** i **IM** itation)，它既支持文本界面，也支持图形界面，对 [vi](https://en.wikipedia.org/wiki/Vim_(text_editor)#Features_and_improvements_over_vi) 进行了大量改进，并在人类已知的所有平台上得到支持。

但是 Vim 的影响并不仅限于 Vim， **Vim 的想法非常了不起，它们已经超越了 Vim 编辑器本身，并传播到其他编辑器中**。今天，您几乎可以在任何编辑器和 IDE 中找到类似 Vim 的模式。就像，恰当地说，在 **Visual Studio 代码**中。

## 为什么是 Vim？Visual Studio 代码还不够吗？

**在这个时代，你为什么要关心了解一个古代编辑呢？**这对我的 Visual Studio 代码设置真的有这么大的影响吗？

事实是，Vim 提供了一种不同于我所见过的与文本交互的方式。一种在编辑代码时给你**完全不同水平的控制和流畅度的方式。**

对于一个有经验的用户来说，用 Vim **编辑文本就像变魔术一样**:

*   Vim 让你更快。
*   **Vim 让你更精准**
*   **Vim 解锁了文本编辑中完全不同级别的控制**
*   **Vim 使你的大脑和电脑之间的界面变薄**
*   做报告时看到的景象令人惊叹 :D

厉害！听起来很好，诸如此类...但是...*Vim 如何实现这一切？* **答案是模式**。

Vim 的模态特性使您的键盘能够控制编辑器的各个方面。每种模式都是一张白纸，赋予您的键盘新的功能，以闪电般的速度编辑文本，以思维的速度导航，选择文本并将其移动到您喜欢的内容，等等。

有了 Vim，您不再局限于插入文本，也不再受鼠标点击、导航或选择文本的束缚。不会。使用 Vim 一段时间后，**您将像一个代码外科医生**一样，随时随地以外科手术般的精度进行专业切割，以闪电般的速度和完全由键盘驱动的工作流的准确性浏览您的代码和代码库。

那么，在这个时代，你为什么想要学习 Vim 呢？转述强大的[德鲁尼尔](https://twitter.com/nelstrom)实用维姆的作者和最晦涩的维姆奥秘的大师:

[![Vim is for programmers who want to raise their game. In the hands of an expert, Vim shreds text at the speed of thought.](img/fb97fd7a3ba66f7672730b2c1ea3beb9.png "Vim is for programmers who want to raise their game. In the hands of an expert, Vim shreds text at the speed of thought.")](https://res.cloudinary.com/practicaldev/image/fetch/s--EtNefiOh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.barbarianmeetscoding.cimg/vim-intro-quote.jpg)

> Vim 是为那些想要提高游戏水平的程序员准备的。在专家的手中，**维姆以思维的速度**撕碎文本。

谁不想这样呢？

> ### 为什么是 VSCode 中的 Vim 而不仅仅是 Vim？
> 
> 你可能想知道...好的。如果 Vim 这么好的话...为什么不在 Visual Studio 代码中使用 Vim 而不是 Vim 呢？
> 
> 很棒的问题！事实是，将 Vim 设置为使用类似于现代文本编辑器的特性集并不是一项简单的任务。像代码完成、代码导航、编辑器内错误消息等特性，虽然 Vim 支持，但开箱即用并不完美。
> 
> Visual Studio 代码和 Vim 一起提供了一个非常好的平衡点，它平衡了 Visual Studio 代码的易设置性和超级丰富的开发用户体验与 Vim 中存在的许多令人惊叹的特性。
> 
> 尽管翻译还不完美。如果你是一个有经验的 Vim 用户，你可能会发现缺少一些功能。但总而言之，VSCodeVim 在 Vim 之外提供了非常愉快的 Vim 体验。

## 简要说明本书中使用的约定

因为 Vim 中发生的很多事情都取决于光标的位置，所以我使用了一系列图表来显示当您键入命令时光标位置的变化。由于它与其他编程书籍完全不同，我想你会发现解释它是有帮助的，这样你就可以在第一次遇到它之前做好准备。这里有一个例子:

```
wwww ==> v   v v  v   v
         word. is two words 
```

意思如下:

```
 commands you type    position of the
   /                    /   cursor changing
  /                    /    as you type
wwww ==> v   v v  v   v  
         word. is two words
           /
          /
      text in your editor 
```

所以:

*   文本`word. is two words`是编辑器中的文本，它可能会被更改或导航
*   您连续键入命令`w`(在本例中是 4 次)
*   每次您键入命令时，您都会将光标(由`v`表示)移动到一个新的位置

有时，比较两个命令在应用于同一段文本时的表现会很有帮助。在这些情况下，我使用了下面的图表:

```
wwww ==> v   v v  v   v
         word. is two words
         word. is one WORD
WWW  ==> ^     ^  ^   ^ 
```

其中底部部分的含义与我们之前讨论的顶部部分相似，但事实上光标由脱字符号`^`而不是`v`表示。

解释命令时，我们将注意以下惯例。对于操作和动作:

```
f{character}

f            - f is a literal f, expected to 
               be typed as-is.
{character}  - is a placeholder that needs to be
               substituted by something. The name
               between {} will be descriptive of
               what that something is expected to
               be. In this case a character. 
```

构造和应用文本对象时:

```
{operator}{a|i}{text-object}

{operator}    - placeholder
{a|i}         - either type the letter a or the letter i
{text-object} - another placeholder 
```

对于 ex 命令:

```
:[range]s/{pattern}/{substitute}/[flags]

:            - denotes the beginning of an ex-command.
[range]      - the [] denote that this part is optional.
               The name will be descriptive as in the 
               case of placeholders.
s            - command to be typed as-is.
{pattern}    - again this is a placeholder.
{substitute} - another placeholder.
[flags]      - another optional part. 
```

## 自由反馈！

如果你发现任何图表或解释令人困惑，有任何问题，想提供反馈，甚至如果你喜欢这本书，想说称赞！不要犹豫，在 twitter.com/Vintharas 的推特上问我。我的 DMs 是开放的，我将永远非常乐意回答你的问题。