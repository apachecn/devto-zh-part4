# 在什么时候你知道你有一个糟糕的应用程序架构？

> 原文：<https://dev.to/mckabue/at-what-point-do-you-know-you-have-a-bad-application-architecture-3f8p>

从我个人的经验来看，当你发现自己在写脚本来清理数据的时候；就像经常清理数据库，清理图像的脚本等等，你有一个不可伸缩的架构。

第二个可观察到的迹象是，当您发现自己添加数据库列来保存计算值(在应用程序运行时易于计算)或布尔字段(其状态/值依赖于其他值数据库值)时，或者当您开始累积数据库枚举来处理状态更改时。