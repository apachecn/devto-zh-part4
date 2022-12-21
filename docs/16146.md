# 使用 PHP 中的参数绑定理解 SQL 注入和预防

> 原文：<https://dev.to/mukherjee96/understanding-sql-injection-and-prevention-using-parameter-binding-in-php-27n5>

排灯节快乐🎆乡亲们！当我第一次学习 PHP 时，我并没有真正意识到 PHP 中关于 web 安全的良好实践。这些年来，在我的好奇心和追求完美的愿望的驱使下，我花了几个小时的时间来研究如何在 PHP 中安全地与数据库交互。

如果你自己尝试这样做，你会意识到遍历 PHP 文档的体验并不那么方便，而且有多种方法可以做同样的事情，坦白地说，这对初学者来说非常困惑。🙄

因此，在本文中，我决定汇编我所学到的知识，并以比较的方式向您展示防止 PHP 中大多数 SQL 注入攻击的两种方法(MySQLi 和 PDO ),并逐步解释它们的用法。🎉

### 这就是我要覆盖的内容:

*   SQL 注入
    *   简短解释
    *   简单的例子
*   阻止 SQL 注入
    *   在 MySQLi 中使用参数绑定
    *   在 PDO 中使用参数绑定
*   MySQLi vs PDO

在我的下一篇文章中，我将通过真实的例子深入研究参数绑定。

[继续阅读“使用参数绑定防止 PHP 的 SQL 注入”](https://geekyminds.co.in/prevent-sql-injection-with-parameter-binding/)

帖子[使用参数绑定](https://geekyminds.co.in/prevent-sql-injection-with-parameter-binding/)阻止 PHP 的 SQL 注入最早出现在 [GeekyMinds](https://geekyminds.co.in) 上。