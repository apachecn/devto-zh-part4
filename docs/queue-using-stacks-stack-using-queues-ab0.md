# 使用堆栈的队列和使用队列的堆栈

> 原文：<https://dev.to/espurrr/queue-using-stacks-stack-using-queues-ab0>

# 堆栈和队列:什么❔

两者都是非原始的线性数据结构。两者的主要区别是堆栈使用 LIFO(后进先出)访问策略，而队列使用 FIFO(先进先出)访问策略。

为了快速演示这两种数据结构:

[![Alt Text](img/7d2674d708be63a85e7850b22b661287.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Cecv15B9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/socv0kv20ymmfiwsrdid.png)

在这篇文章中，我们不会深入探讨这两种数据结构的实现。而是说一个计算机科学/软件工程毕业生很常见的面试问题。

## 问:如何用栈实现一个队列，用队列实现一个栈？😮

别担心，这是花生！🙃如你所知，对于这两种数据结构，我们主要考虑两种操作。*插入*和*删除*和
换句话说，对于栈应该是*推入*和*弹出*，而对于队列应该是*入队*和*出列*。让我们一次看一个..

### **队列使用堆栈(实际上是*两个* )**

#### 入队

[![Alt Text](img/5eb3d19d7fdf99a665aa67c47e14dbbf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--znwMajbK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/er70jjsesvaf3ri24ikg.png)

**步骤 1 :**
[![Alt Text](img/d1c30895afa9dd45ed044ac673a66a6a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XRW1Oo4F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/imi0y32tkndb0a9eg5ag.png) 
从栈 1 中弹出元素，推入栈 2，直到栈 1 为空。

**步骤 2 :**
[![Alt Text](img/cabd3187953dcbef3a432c28ac711735.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4WRBtwfR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t9dsfzzsptbid85q9ht2.png) 
现在，将待入队的元素推入堆栈 1

**步骤 3 :**
[![Alt Text](img/19b38e27aa15b771feb696fc7e0830b7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b6bvaA-X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6bkk2chny8ck7g8olcmj.png) 
然后，从栈 2 中弹出元素，并将其推回栈 1，直到栈 2 为空。

**第四步:**
[![Alt Text](img/f3bd433b109e75c2c49647c47865e14d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JZlYESEH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nuauuv68sumlia9ex7fh.png) 
好了！

#### 出列

第一步:
首先，我们需要检查堆栈 1 是否为空(在我们的例子中是*而不是*)。如果是这样，错误信息应该显示为“下溢”。因为没有可以丢弃的元素。

**第二步:**
[![Alt Text](img/102021a4f6ca371f67da1a8c4724388c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--24jFh6dE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3p6qxt2037mzre49mabm.png) 
从 stack 1 中弹出一个元素并返回。

*注意要考虑:*我们在这里让入队算法代价很高。这意味着，我们为入队和出队做了大量的工作，只是弹出了顶部的元素。您可以自己尝试让出列算法变得昂贵。

### **堆栈使用队列(再有*两个* )**

#### 推送

[![Alt Text](img/99e117fff5b11df6d22c584a154309a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c0PbYiHC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/copwplt9rs4jnbr23jp0.png) 
所以我们有一个堆栈，它已经正确地推入了 1，2 个元素，现在我们必须将 3 个元素推入其中。

**步骤 1:**
[![Alt Text](img/e75eea82acd39f27617275c714e4d1fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VhjMSiSE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/11vtho0r14ef9mlfm8to.png) 
将待推入的元素入队到队列 2。

**第二步:**
[![Alt Text](img/e62dfe8811a8ed048be498453dc32226.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2c32w4hE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1dpbtdg4b63t3agjjxt6.png) 
逐个将元素从队列 1 中出列，入队到队列 2 中。

**第三步:**
[![Alt Text](img/e1a56b7d043ada2bdef0881bb55e315d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sqKXIRP5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/slwmdasmhirzuzkcwtcl.png) 
现在，只需互换两个队列的名称。

**第四步:**
[![Alt Text](img/dcd3bf380529b9082470a66bd2cb5d0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VLyM2yXw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n0j9335ydz0gb6sr0mnf.png) 
就是这样！

*另一种方法:*看看 [@mvthanoshan](https://dev.to/mvthanoshan) 对此的看法..【https://dev.to/mvthanoshan/comment/19hpc】T5
T6】

#### 流行

**第一步:**
检查两个队列是否都为空。如果是这样，它应该发出“下溢”警报。

**步骤 2:**
[![Alt Text](img/0601147d2671bf906b70f0cdb23c7f56.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7QqwC0j4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kvvnq4baefuozndrd7s4.png) 
将一个元素从队列 1 中出队并返回。

*注意考虑:*这里也一样我们做了插入法，推贵。你可以在实践中反过来试试。

Tada！🎉你离给面试官留下深刻印象又近了一步。