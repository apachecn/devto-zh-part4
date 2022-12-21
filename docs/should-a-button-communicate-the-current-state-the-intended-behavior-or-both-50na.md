# 按钮应该传达当前状态、预期行为还是两者都传达？

> 原文：<https://dev.to/ben/should-a-button-communicate-the-current-state-the-intended-behavior-or-both-50na>

当我放大 Chrome 中的一个窗口时，我会看到这样一个图标:

[![magnify](img/1164093c34d3a9f8655e22c3ced4e160.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--erMVksTr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cl.ly/bbba80903198/Image%25202019-06-16%2520at%25209.27.59%2520PM.png)

当我把一个窗口的大小缩小到正常的 100%以下时，我得到了这个:

[![de-magnify](img/b4e9733799235d433b02902e8c9a2729.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--w_xpR6vu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cl.ly/db245bd6525e/Image%25202019-06-16%2520at%25209.29.02%2520PM.png)

单击这些图标中的任何一个都会给我提供修改放大级别的选项。

在 DEV 上，当我跟踪某人时，我会看到这个按钮。点击它让我“不关注”他们。

[![following](img/4c7713fec059e52e996e41993b896573.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dxAJLaL8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cl.ly/61b539840d5d/Image%252525202019-06-16%25252520at%252525209.30.17%25252520PM.png)

我们的 UI 不一致，因为我们通过提供一个指示预期动作的悬停状态来区别对待主页的“保存”按钮。

[![unsave gif](img/9c039e2b0a66cd11683c1dd9f42c8cf8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iL8WOGAj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://im4.ezgif.com/tmp/ezgif-4-b7df09d2da74.gif)

当我们试图使这种行为一致时，我很好奇你认为什么是基本正确的行为。这种情况在网络上有不同的处理方式，但我很好奇是否有人觉得有真正的最佳实践。

所有关于该主题的评论都非常感谢！