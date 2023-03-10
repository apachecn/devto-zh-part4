# 使用 VIM 宏快速浏览文件

> 原文：<https://dev.to/connorbode/use-vim-macros-to-move-quickly-through-a-file-ikc>

所以让我们回顾一下 VIM 中的一些移动命令。

*   `hjkl`为左上右下
*   `wbe`用于句子中词与词之间的跳转。

现在让我们假设你正在跳过一个很大的段落。整件事只有一行，但有 200 个字那么长。你想编辑第 155 个单词，但你不一定知道它是第 155 个单词，你只知道它在段落的很远处。

你怎么去那里？

嗯，如果你像我一样，你可能会开始敲`w`，一个字一个字地跳。但之后你会意识到“该死，这要花很长时间”(155 次按压，但你没有意识到)。

下一步是什么？嗯，你可以通过输入`10w`跳过 10 个单词。你可以通过输入`10w10w10w10w10w10w`来重复这个操作，但是这也需要很长时间。

那么解决办法是什么呢？

**宏！！**

宏可以用任何一个字母命名。首先，让我们选择一个宏名。我们会选择`a`。

1.  通过键入`q`开始录制宏，然后键入宏名`a`
2.  键入宏
3.  通过键入`q`结束录制

对于我们刚刚遇到的情况，我们想要重复`10w`，我们将输入`qa10wq`。

然后，为了使用宏，我们键入`@a`。

最后，最好的部分，我们可以输入`@@`来重复前面的宏。

当然，这可以用于任何你想重复的动作。

* * *

希望这个技巧是有帮助的。点击这里或在 [twitter](https://twitter.com/connorbode) 上关注我，获取更多快速提示。