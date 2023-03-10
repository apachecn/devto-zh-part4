# 加倍你的链表，加倍你的乐趣！

> 原文：<https://dev.to/danmurphy79/double-your-linked-list-double-your-fun-cje>

双向链表可能不是最性感的复杂数据结构——我们在看你，哈希表——但是当你浏览互联网或使用 Spotify 这样的音乐播放器时，你很可能已经遍历过这些复杂数据结构中的一种。

[![](img/2f4a14938cc546c0779bb317495ab441.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QtStyted--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lz4554rjt4cgx3w9o881.jpeg)

与链表非常相似，双向链表由内存中不连续的节点组成，这些节点通过“指针”连接起来，告诉计算机链的下一部分在内存中的什么位置。它们都有一个头，指向列表中的第一个节点，还有一个尾，指向列表的末尾，但是单链表中的节点只引用链中跟在它们后面的节点，而双链表中的节点既引用后面的节点，也引用前面的节点。

[![](img/83f80d440f2d27c98410ba4534b7549d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l3zjG28j--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fxl837r8131di7icmg4o.jpeg)

这使得双向链表在时间复杂度上有优势，但是与单向链表相比，在占用的内存量上却处于劣势。因为它们需要不止一个，而是两个对其他节点的引用，所以双向链表中的节点会占用更多的内存空间，但是，如果您知道链中节点的“地址”,这些额外的信息会使查找、插入和删除等任务更加有效。因为单个链表必须从头开始遍历，所以所有操作至少是线性时间复杂度，但是双链表可以从头或尾开始遍历，并且删除和插入特定节点的时间复杂度是恒定的，因为唯一需要改变的引用是我们的目标节点两侧的节点——由于我们的目标节点提供的信息，我们已经知道将它们指向哪里。这些列表有助于在音乐播放器或网络浏览器上提供前进和后退按钮，为应用程序提供撤销和重做功能，并在应用程序的文件菜单中提供“最近使用的”文件列表。