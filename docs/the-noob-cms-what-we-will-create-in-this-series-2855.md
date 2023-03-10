# Noob CMS:我们将在这个系列中创造什么？

> 原文：<https://dev.to/th3n00bc0d3r/the-noob-cms-what-we-will-create-in-this-series-2855>

干得好，伙计们，你们已经成功地设置了你们的环境。现在我们继续。

**工具和环境**

用相同的工具(VsCode、MySQL Workbench、phpMyAdmin、MariaDB、Nginx)搭建不同的环境，目的是为了说明不要把操作系统当成中国的长城。所有的操作系统都可以产生相同的输出，不管它的优点和缺点是什么，因此这个想法应该被用来阻止你成为一个全栈开发者。随着我们的发展，无论环境如何，我们都会使用相同的工具。这将清楚地为如何用不同的方法实现同样的事情奠定基础。

**技术**

现在，在我基于 [PHP vs NodeJs](https://dev.to/th3n00bc0d3r/php-vs-node-3oa) 的讨论之前，我打算跳过 PHP，但是现在由于辩论已经失败成两个政党，一个 PHP 和第二个 NodeJs，我已经把 PHP 7 包括在内了。首先，我们将覆盖 PHP 7 并生产一个产品，然后我们将在 NodeJS 和 Angular 上复制相同的产品。据我的理解，PHP 已经有将近十年的历史了，因此在编程概念在 PHP 上易于理解的背景下更加精炼，可能因为这是你接触的第一件事，或者 PHP 上的概念被进一步转化为 NodeJS 和 Angular 中稍微高级的概念，尽管我们将在这两种技术中生产相同的产品。现在为了简单起见，我将使用 MariaDB 作为数据库，它只不过是 MySQL 的一个更好的版本，所以不要担心。

[![](img/c41fae90bd84bf6598cd1a00a55fad71.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6P63QDXE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/6dm2l8lie6p7ewvkasdk.jpg)

**项目概述(NOOB CMS)**

Noob CMS 将是一个开源项目，用户可以使用电子邮件、用户名和密码进行注册。密码将使用 MD5 加密进行加密，以确保安全。用户将有关于我们的基本配置文件，并将能够上传自己的显示图片。用户可以创建类别，但不能创建同名的类别。这些类别将作为用户能够撰写新闻文章的部分。用户将能够删除和编辑这些文章。这些文章将在用户个人资料下和公共墙上提供，其他用户可以在那里评论和分享这些文章。

**可选注意事项**

这些考虑将是可选的，取决于我能在观众面前传达什么，因此需要观众的反馈。我们最后可能会谈到的一些功能包括实时聊天和私人消息。我们可能考虑的一部分是收集分析数据，并将其存储到数据库中以供其他查看目的。

预期产出:一个工作网站，一个安卓应用，一个 IOS 应用

在我们开始之前，我会专门等待一些反馈，所以不要让我等。

[Noob 指数](https://dev.to/th3n00bc0d3r/noob-guides-index-4mne)