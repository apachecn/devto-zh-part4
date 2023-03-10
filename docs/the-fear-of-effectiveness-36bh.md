# 对效率的恐惧

> 原文：<https://dev.to/stealthmusic/the-fear-of-effectiveness-36bh>

2003 年，我在一家大型德国工业公司开始了我的第一次实习，当时我是一名软件开发人员。我已经准备好统治世界，或者至少编写一些有价值的代码。这是一件大事，我很激动。

[![I'm gonna rule the world](img/0b7d50390641e8328ad457942b5182eb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SBl3dVPL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/10qrtofs28pq1a4gaqxt.jpg)

当我得知我必须用 VBA (Visual Basic for Applications)编程来自动化大型 Excel 表格时，这种情况突然停止了。更糟糕的是，我的主要任务是将大量的文件输入 Excel。当我说“一大堆纸”时，我指的是字面上的意思。我们申请公开招标，得到了数千页的印刷说明书。每一页都有一个表格，上面列有需要报价的项目。所以我和两个实习生被雇来把每一个项目输入 Excel，这通常需要 2-3 周，然后交给项目经理。

VBA 实际上只用于计算每个部分的中间和。

[![So exciting](img/786b8d627b47e10b41ecaa1fb94f8588.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a-hjlKNr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f2cq7hbbhhkiwk7zz54r.jpg)

所以在一两个这样的规格之后，我想知道这个小软盘是什么，大多数盒子里都有纸。

我问了其他实习生，但他们不知道。我问了项目经理，他们告诉我，这是一些特殊的文件格式，他们不能用 Excel 读取，他们没有任何其他程序可以读取它。但它可能包含规范。

所以看起来他们雇佣了三个实习生好几年，而不是购买实际上可以存储文件的应用程序。

我决定不再浪费时间了。相反，我对格式进行了逆向工程，并在 Excel 中实现了一个解析器，它可以在几分钟内而不是几周内直接创建一个格式良好的 Excel 表。

我很乐意向其他实习生演示。这种反应出乎我的意料。他们告诉我不要用它，因为它会毁了我们的工作。

我没有听，去找了项目经理。他看了看生成的 Excel 表格，发现了几个编码错误(德语元音字母未被正确解码)，并认为我的程序不正确，我必须手动完成，因为这样更准确。

[![Yeah...but no](img/1dd37de1572373a7e11fbab47a5fc47f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8EamXpBK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hx8kt6sg77o0vcnk1lje.jpg)

显然，我没有再听。在那里的其余时间，我使用我的程序，手动修改变音和其他问题，其余时间在网上阅读材料。当一个项目经理来的时候，我打开了一个我一直在后台准备的 Excel 表格，假装我在工作。

[![Boredom](img/753e8f18a187a43aedc38d12fe81e247.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9NoNZqcV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j14zhi01ozz7efysbzhs.jpg)

最终，我再也无法忍受这种无聊，在内部申请了另一份实习。

你的第一份实习或者第一份工作是什么样的？