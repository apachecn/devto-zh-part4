# CoverFlow

> 原文：<https://dev.to/connectsteven/coverflow-37e4>

使用收藏视图推送动画，如 tinder

CustomFlowLayout 控制器是 UICollectionViewFlowLayout 的子类，具有自定义动画和布局。

**用途**

1.  将本机 UICollectionView 添加到您的视图控制器 Xib 中。

2.  创建自定义 Collectionview 单元格。

3.  在视图控制器中注册您的自定义集合视图单元格。

4.  在 ViewController 的 viewDidLoad()方法中设置集合视图和 alloc 的自定义布局流，并在下面的细节点 2 中解释。

想知道我们是如何在左右滑动时添加喜欢和不喜欢功能的吗？简单查看一下这里的代码: **[CoverFlow](https://github.com/Mindinventory/CoverFlow)**