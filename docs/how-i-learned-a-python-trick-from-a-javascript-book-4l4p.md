# 我是如何从一本 JavaScript 书中学会 Python 技巧的

> 原文：<https://dev.to/yairmorr/how-i-learned-a-python-trick-from-a-javascript-book-4l4p>

[![](img/49a42289ddf8296efea41962e5240773.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l-tzSaRg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Anaasd4ZbU2oKvS6cDj33vQ.jpeg)

我正在 Autodesk Maya 中开发一个不错的项目管理小工具。它有几个下拉菜单，包含项目根目录中的文件夹和文件。这个工具自动执行一些步骤，帮助用户快速从一个项目切换到另一个项目，而无需浏览文件。

<figure>[![](img/3a6422fd81fadc7c38d463fee121237e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e9EGLtjE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AwfA0Hgq8oPMdhFc4Cuwlqw.png) 

<figcaption>Maya 项目浏览器，怎么看起来像是在开发</figcaption>

</figure>

在使用这个工具时，我在迭代一个循环时遇到了一个恼人的算法范围问题，这使我无法将我希望的值赋给变量。我想跟踪每个下拉菜单的索引值，但结果是所有菜单都有相同的值。

然后我想起在我最喜欢的 JavaScript 系列丛书中读到过一个非常相似的问题——*凯尔·辛波森*的*你不知道 JS* 。在他的书 [Scope & Closures](https://www.amazon.com/dp/B00IV3J2A2/ref=cm_sw_r_cp_awdb_t1_qMBjDbYMR0TFP) 中，他解释说，虽然从循环内部给函数赋值变量会捕获该变量的副本，但它仍然共享相同的作用域。这意味着我的循环中的每个函数调用都引用了最终指向相同值的变量的副本。但是我用 Python 而不是 JS 编写我的工具，因为它是 Maya 中用于编写脚本的语言。

根据我在视觉效果方面的经验，不同平台上的问题通常是相似的，解决方案通常也是相似的。但是这一点智慧能在代码世界中为我服务吗？不出意外，答案是肯定的！我认为如果这个范围问题发生在 JavaScript 中，它也可能发生在 Python 中。

Simpson 在 Scope & Closures 中对此问题的解决方案是将该变量传递给同一个函数，但将其封装在一个 IIFE 中。现在你可以把这个变量赋给生命中的一个局部变量(在循环中)。现在，局部变量为每次迭代保存一个值的副本，并将该值传递给函数，并且在一次又一次的循环迭代之后，该值不会改变。

据我所知，在 Python 中没有生命和代码块，就像在 JavaScript 中一样。为了解决这个问题，我将代码放在一个新方法的循环中，并在循环中调用这个方法。这个新方法本质上充当了一个内部作用域，类似于 JavaScript 的 IIFE，它“锁定”我需要的值，并将其安全地传递给内部的方法。

基本就是这样！下面是我如何在代码中实现它的详细解释。

我的脚本有一个主类，包含该工具的所有方法和变量。

第一种方法是 *initUI()* ，创建一个窗口并添加一些按钮和下拉菜单(Maya 的 *cmds.optionMenu()* )并存储在一个名为 *self.menus* 的字典中。