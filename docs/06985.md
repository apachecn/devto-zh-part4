# 面向初学者的 Vim/Vi 教程

> 原文：<https://dev.to/dharanr/vim-vi-tutorials-for-beginners-2neh>

大多数人厌倦了使用 Vim，我也面临同样的问题。但是，在实际情况下，这没什么大不了的。问题是，我们应该了解使用它的基本知识。

首先，启动你的同伴终端，

打开您想要编辑的任何文件。在我的例子中，我使用下面的命令打开 main1.java

[![Alt Text](img/b977640f13e9d90fa14c0405ffd888da.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eteZabYb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y2nxz0lfm0bq66aggexz.png)

要编辑文件或在文件中插入任何字符，我们需要通过按

[![Alt Text](img/5bc685a3f07d4be9eef7f85a89b66b1f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rZGc20UQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y07cpfetzc1rgksvvtff.png)

现在，编辑你需要的任何东西，通过使用箭头键前后移动光标，使用退格键删除内容

现在，是保存内容的时候了…不，不要用 ctrl+s——那个家伙不会在这里工作..这里是关于我们处于什么模式，现在，我们处于插入模式，所以，我们必须进入命令模式，使我们的命令工作，为此，按

[![Alt Text](img/3b33f5dd99f63982079f08d41207cbd5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SO972BoO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oy3tti0uwzmrwc3y94uq.png)

现在是时候退出了，对吧？然后按

[![Alt Text](img/9a4ed48718b3fcbe4336f12869bd735a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MBJa-lbs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/mipkvtiwpanph7t5tge5.png)

要复制和粘贴终端中的内容，我们使用不同的术语，即“拉”和粘贴。

请注意,“yank”会将内容复制到终端自己的剪贴板——它不允许您复制到外部(要将内容复制到系统的剪贴板，请阅读关于+clipboard)

对于猛拉，通过按

[![Alt Text](img/5644f877d4ac20ed51ae550250559c25.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZV4UkgoB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xfqzbuhqrk77son6lz8p.png)

所以，我们应该注意到终端在它的底部显示了“视觉模式”这个术语。现在只需简单地拖动鼠标或使用向下箭头来选择多行，以做一些操作。现在，按下

[![Alt Text](img/21503dd16aecb974c32049b2f4b2c83a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yYXT51XJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ebgauxgpbi3hshpmk5is.png)

一些捷径会让这个过程变得很酷。他们是

[![Alt Text](img/6cba43f63ce8e5ba81d0a0401b33f969.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pt3n0JXA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/swt3tzq3hji7mdsulcxy.png)

现在让我们转到 vimdiff，使用下面的命令比较我们经常使用它来解决合并冲突的文件。

[![Alt Text](img/99434c0bbf044c591024c2594ced678d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GVN68ke---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/4dl9oc9fw5ih9y355f8x.png)

[![Alt Text](img/b3f52e7d24414ea0ead7fabd786930f8.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3H9LS3Gh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ltzw8cmb2opiid6sgux9.png)

从上面的图片，我们可以很容易地发现文件之间的差异，粉红色的点是修改过的区域，蓝色的是遗漏的行(删除或添加)

访问此窗口的快捷方式

[![Alt Text](img/6dceab227677aa7da185cafeb854d23f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6SCUSv7B--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/h51eop3k6a2jje8por8x.png)

现在，要解决变更冲突——只需将光标移动到您想要变更和使用的地方

[![Alt Text](img/621826f2f5959c124e5ee0b7a39ba070.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kJRIT-yI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6qx7cn5eig0hvgv72hgw.png)

即。，
例如，如果光标位于第一个窗口上描绘粉色高亮的行上，

[![Alt Text](img/a8153842ceb32a03aaa29773d4c7f4c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--guLHNGYW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/79iranijimw85sx8ywbg.png)

为了您的方便，最好使用简单的“拉”和粘贴，就像我之前告诉你的那样。最后，不要忘记保存并关闭

[![Alt Text](img/31464e081b0bb2c9ddcf96299a1cf833.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G7x7xoQ2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m7xw6n8kf691m739u146.png)

vim 中仍然有许多可用的功能，但我现在所说的已经足够管理 vim 中的文件了…
祝您愉快！！

还拍我我的介质页面:
[https://Medium . com/@ rdharan/vim-basic-cheat sheet-for-初学者-297190fc8331](https://medium.com/@rdharan/vim-basic-cheatsheet-for-beginners-297190fc8331)