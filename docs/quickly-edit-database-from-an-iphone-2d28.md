# 从 iPhone 快速编辑数据库

> 原文：<https://dev.to/tungtono/quickly-edit-database-from-an-iphone-2d28>

TablePlus iOS 使从像 iPhone 一样小的设备上管理数据库变得很容易。

如果你的 iPhone 上还没有安装 TablePlus iOS，在这里 获取 [**。**](https://tableplus.com/ios)

以下指南是在 iPhone 6 (4.7 英寸)上演示的。

#### 1。内嵌编辑

TablePlus 在类似电子表格视图中打开数据库。在像这样的小屏幕上，你可以在不需要的时候隐藏左侧边栏来节省更多的屏幕空间。只需将其边界线拖到左侧。

使用表格视图，您可以通过双击直接选择和编辑任何数据单元格。

[![Inline Edit](img/65df2716d8752013a5de29f3a8961ca5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--USoP2FOa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h0rcv6k0pvqqpz5l3ebg.gif)

如果值很长，并且您需要更多空间来处理它，您可以使用文本编辑器。要使用文本编辑器:

*   点击并按住您需要的数据单元格
*   从弹出菜单中选择文本编辑器
*   在更大的文本编辑视图中编辑长值
*   轻按编辑器外部以关闭它

[![Text Editor](img/11a96cb3be5885b1ff8e1a14f17f49f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pv4ogXId--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nkjq507g6uwp900qxolv.gif)

#### 2。右侧边栏

当你有一个有很多列的表格时，右边的边栏将帮助你很容易地查看和编辑一个特定的记录，并且在滚动时你不会把它误认为另一行。要使用它，请拖动数据表右侧的边界线，然后在不再需要它时将其拖出。

[![Right Sidebar](img/11a96cb3be5885b1ff8e1a14f17f49f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pv4ogXId--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nkjq507g6uwp900qxolv.gif)

#### 3。高级数据过滤器

要快速筛选出您想要查看的记录，请使用。默认情况下，过滤器隐藏在数据表上方，要使用它，您只需向下拖动上边界线。

[![Filters](img/6983006874dcf3fb62b2479af13f88a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0QTDlks2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/udtgn3jtg8ghwio93ph5.gif)

#### 4。代码审查

在小屏幕上工作的主要问题是，我们可能会在不知道的情况下意外地做出更改，比如删除操作。当我们发现的时候，可能已经太晚了。TablePlus 有一个编辑模式，使用起来既方便又安全。您所做的更改将被挂起，您可以先检查它，选择提交到服务器或取消它。

[![Code review](img/bcce7233376f6fee291daa704a697d0a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D9hYGa_d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hhfbshbamtr4f1sz8360.gif)

此外，您可以使用 Cmd + Z 的经典组合轻松地撤销和重做所有这些更改，或者按住标题栏并选择撤销，一切都在控制之中。

#### 5。多个选项卡和多个工作区

当您同时使用多个数据库时，很容易打开另一个表，或者切换到另一个连接并同时激活它们。

[![Multiple Tabs](img/11a96cb3be5885b1ff8e1a14f17f49f0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pv4ogXId--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nkjq507g6uwp900qxolv.gif)