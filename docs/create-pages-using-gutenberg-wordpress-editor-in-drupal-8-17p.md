# 在 Drupal 8 中使用 Gutenberg (WordPress 编辑器)创建页面

> 原文：<https://dev.to/webwash/create-pages-using-gutenberg-wordpress-editor-in-drupal-8-17p>

[https://www.youtube.com/embed/xlH9LLLTzF8](https://www.youtube.com/embed/xlH9LLLTzF8)

新教程发布时获得通知。[订阅我们的简讯](https://www.webwash.net/newsletter/)。

**📽视频片段:**

*   下载并安装古腾堡( [01:15](https://www.youtube.com/watch?v=xlH9LLLTzF8&t=75s) )
*   在页面内容类型上配置古腾堡( [02:01](https://www.youtube.com/watch?v=xlH9LLLTzF8&t=121s)
*   如何使用古腾堡编辑器( [03:21](https://www.youtube.com/watch?v=xlH9LLLTzF8&t=201s) )
*   将图像上传到古腾堡( [06:55](https://www.youtube.com/watch?v=xlH9LLLTzF8&t=415s) )
*   创建可重复使用的块( [11:02](https://www.youtube.com/watch?v=xlH9LLLTzF8&t=662s)
*   使用古腾堡云( [15:42](https://www.youtube.com/watch?v=xlH9LLLTzF8&t=942s) )
*   了解 Gutenberg 数据如何存储在 Drupal 中( [20:31](https://www.youtube.com/watch?v=xlH9LLLTzF8&t=1231s) )

> 想学 Drupal？查看我们的免费课程。

古腾堡是 WordPress 5.0 的新编辑器。这是一种新型的编辑器/页面生成器。您不是在单个文本区域中编写文本，而是使用块来构建页面。块可以是简单的东西，比如一个段落或一幅图像。或者更复杂的块，如“媒体&文本”或在列中添加。

编辑器本身是用 Javascript 写的，更确切的说是 React。这使得它可以在 Drupal 中使用。但是我确信要让它在 Drupal 中工作还需要额外的工作。

在本教程中，您将学习安装和配置 [Gutenberg](https://www.drupal.org/project/gutenberg) 模块，并学习如何在页面内容类型上使用它。

如果你热衷于在不配置 Drupal 站点的情况下使用编辑器，请访问[https://drupalgutenberg.org/demo](https://drupalgutenberg.org/demo)。

## 入门

在我们开始之前，去下载并安装古腾堡模块。运行以下 Composer 命令:

```
composer require drupal/gutenberg
```

一旦你下载了模块，请继续扩展并安装它。[![](img/1b9066ca70513e9420eb6a8d1cc2d788.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uoqte-sc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uy6rhbbfyllc0rykewwf.jpeg)

## 在页面上配置古腾堡内容类型

现在我们已经安装了模块，让我们在页面内容类型上配置它。

1.转到“结构”，“内容类型”，然后从“操作”菜单中单击“编辑”。[![](img/b5d2158b40dc6ef8cf020e2121fda091.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--JQ_Qsvj2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4e2ig90o9799kjjh3o4n.jpeg)

2.点击垂直标签上的“古腾堡体验”并勾选“启用古腾堡体验”。
[![](img/534d0c73cbd4facf3509c75696305bc5.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--OcQC9ZVe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wuyguoh1f2jy59s6z2sx.jpeg)

3.单击“允许的块”以展开可用块的列表。您可以控制哪些块可供编辑人员使用。

[![](img/26f5e289f21ebff33be8345caf93a889.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L8FNtZXZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6516jpxvhb6bsujfn1ci.jpeg)

4.完成所有配置后，点击“保存内容类型”。

这是让它在任何内容类型上工作所需的所有设置工作，非常简单。

## 如何使用古腾堡

现在转到内容，“添加内容”并点击“基本页面”。您现在应该会看到一个非常不同的节点编辑表单。

[![](img/4705b87837cec41efe31f4cf6cb6003b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--F2a5sj9l--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c47spv2bfvqw1f3lbw72.jpeg)

### 如何添加内容

Gutenberg 编辑器与 Drupal 8 附带的文本编辑器非常不同。您可以创建和管理块(古腾堡块)，而不仅仅是写入一个大的文本字段。

注意:我不是指添加到区域中的 Drupal 块。

要查看所有可用的区块，请将鼠标悬停在“开始书写或键入..”上或者单击加号(+)图标。

[![](img/8a14333f57d87e5d4081170e0e1de91d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5yHxe7nl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3cdmdi0adpgpdj9ti2ve.gif)

比方说，你想添加一个标题和一个段落。你可以创建一个标题块，然后是一个段落块。

[![](img/228de4041fe08b704d12dd959a88ec94.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_mv5-3rA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dos63int7tok9bojce58.gif)

您可以通过键入“/”并使用其名称进行搜索来更快地添加块，而不是单击(+)图标。任何块都可以这样添加。

[![](img/b352b9566a804c75e0ae4c69b2afb058.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--j-FVlwIu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/da4hkno51ima4ttpdcwi.jpeg)

此外，如果你粘贴了一堆文字，然后段落块将自动使用。

### 块配置

每个块都有自己的配置。以段落块为例，你可以改变字体大小、背景颜色、文本颜色等等。您可以通过单击右侧的 Block 选项卡来访问设置。

[![](img/fcc3ebb21b3c2db9d8b624ee707952d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vQHjGfz8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0duw81f0wtasnp3n37oj.gif)

## 与 Drupal 的托管文件系统集成

除了提供编辑器之外，Gutenberg 模块还与 Drupal 的托管文件系统进行了很好的集成。

以“媒体和文本”块为例。您可以上传文件(也可以通过拖放，这很好)并在右边添加文本。

[![](img/a829255ef6f2f02888d3979294410f2b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ixpP_zm1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4unz3sv3iw750ig61srt.gif)

如果你点击“媒体库”，你可以重复使用任何已经上传的图片。

[![](img/80e2954012c9fcff759d95aadceaddc7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kcds9GSX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/dhe5n6pxd706698t3z7x.jpeg)

## 将 Drupal 块添加到编辑器中

您可以将 Drupal 块添加到编辑器中(现在我正在讨论添加到区域中的 Drupal 块)。

只需使用名称搜索任何块，或者在“Drupal 块”一节中查找。

[![](img/d8bb28fe64bd7dedfcb8c6be8277d2a6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WZ5a0FSL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/as9u2ov2s3pqnne81llc.jpeg)

以下是如何添加“最近内容”阻止视图的示例。

[![](img/3d537b4ca1120987fb855a9578e62f5e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sOopXn9U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qonxfhx7wuwxndjkwlod.gif)

古腾堡有很多不同的街区，我建议你花时间四处游玩，学习如何使用它们。

## 创建可重用块

Gutenberg 允许您创建可重复使用的块。这些块可以添加到任何页面，如果你修改块，它会改变所有页面。

例如，让我们创建一个将在多个页面上重用的列表块。

1.在古腾堡建造街区。

2.单击 3 个垂直点，这将打开“更多选项”下拉列表。

3.然后点击“添加到可重用模块”。

[![](img/1aeda2fd124f0c5f28989109b549a8ea.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0fk-ZC4Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1ocf4i2h7vhycw8ewfjw.gif)

### 添加可重用块到页面

要重用某个块，请单击(+)，您应该会看到该块的可重用部分。

[![](img/b11e7754b13b0c076fb8aabb843cefe3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O1QFtfaX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4ub9eykc877pfu1qbql2.gif)

要编辑可重用模块，请将其添加到页面，然后单击编辑按钮。

[![](img/57beb0647e7bd80d0008b11623ddd2de.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c_VnB2tW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/cthlkeybthf5hzjynedx.jpeg)

注意:这将改变所有使用它的页面上的块。

## 管理自定义字段

您可能已经注意到，编辑器接管了整个节点编辑页面。如果对带有自定义字段的内容类型使用 Gutenberg，会发生什么情况？

这些字段被添加到页面底部名为“更多设置”的字段集中:

[![](img/cc6cd48eb8410e0ffed20538ac9e0c1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dDVPaOYJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/k8yw8aqn3djgr6v44xdn.jpeg)

## 古腾堡云

构建古腾堡模块的团队还创建了一个名为[古腾堡云](https://gutenbergcloud.org/)的网站。它允许你与他人分享古腾堡区块。

[![](img/e854f87297a348eabdc18efd6fe8ec3b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HBH2AAQg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/j1ej9yb3p8doac293pc1.jpeg)

在自己的站点中使用块很容易。你只需要启用模块附带的“古腾堡云”子模块。

1.转到扩展并启用“古腾堡云”。
[![](img/6223d8661b58aee9c958a9dfe95d42cc.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--_gBrtXhp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/617w3ddc4he4q7ciqihv.jpeg)

2.然后，转到配置，“古腾堡云块管理器”。

[![](img/0d125c31a1aaef97e9b81b6d71704345.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--N2wErm0Z--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gfram10qwtnh6ywiks18.jpeg)

在这个页面上，你可以在自己的站点上使用“古腾堡云”中的块。

当您启用阻止时，不会将任何内容下载到站点中。Gutenberg 块是用 React 编写的，所以它只是 JavaScript。该块的代码通过 CDN 提供。

3.要使用已启用的块，只需从“云块”部分选择它们。或者按名称搜索它们。

[![](img/b3f1a8a3495554df2142a5b2f6a15cf6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WdeePS-y--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hp3yv0pws09c4h8tuko3.gif)

## 古腾堡数据是如何存储的？

所以你现在可能在想:Drupal 中的数据是如何存储的？当您添加一个块时，它存储在哪里？

简而言之，它存储在 body 字段中。

如果您查看`node__body`表和`body_value`列，您会看到所有的块都存储为标记。

[![](img/3ffc79741735c5b991a501a24591c940.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TuF-KHOC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2sf31rxbpiqydi7f26vl.jpeg)

## 总结

设置古腾堡编辑器很容易。只需选中内容类型上的“启用古腾堡体验”复选框，您就可以启动并运行编辑器了。老实说，我很惊讶它这么容易配置。

*原载于 2019 年 4 月 4 日[网洗](https://www.webwash.net/?p=8697)。*