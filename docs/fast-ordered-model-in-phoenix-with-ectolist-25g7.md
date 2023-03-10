# 基于 ecto_list 的凤凰城快速有序模型

> 原文：<https://dev.to/popo63301/fast-ordered-model-in-phoenix-with-ectolist-25g7>

[![](img/b8c9025bc358cc70d269f0f4f806c569.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IITwdIWS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bzuet43fkt64iwibkfb7.gif)

## 为什么？

我正在用一个系列/视频系统编码一个付费视频服务。一个系列具有根据定义的顺序排序的几个视频。我很惊讶我花了这么长时间来构建这个功能，而且社区也没有提供解决方案。当我意识到我可能会再次遇到这个问题时，比如说，如果我建造了一个特雷罗克隆体，我想我必须做些什么。

ecto_list 来了！

## 什么？

Ecto_list 既是一个库，也是一个教程，包含一组代码片段，可以帮助您快速构建有序模型。不涉及现场直播！

## 它是如何工作的？

有两种方法可以保存项目的顺序:

*   在物品本身(如`act_as_list`红宝石) :即视频
*   它们所属的型号:即系列。

Ecto_list 采用后一种选择。为什么？因为我们希望避免使用几个 SQL 查询来只更新一个商品的所有订单，而且，**我们假设无论如何都要预加载所有商品**。这就是为什么在有许多项目的模型中保存订单是有意义的。

这个库超级好理解。目前有一个函数可以根据定义的顺序神奇地对项目进行排序:`EctoList.ordered_items_list/2`。剩下的就是在源代码的正确部分添加正确的代码片段。

## 结论

这是我的第一个图书馆。🎉).仍然有改进的空间，所以如果你认为我们可以添加一些有趣的功能，请随意贡献。

### 链接:

*   https://github.com/popo63301/ecto_list/
*   https://github.com/popo63301/ecto_list_demo