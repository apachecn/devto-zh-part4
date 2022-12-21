# PHP 中的参数绑定——示例和讨论

> 原文：<https://dev.to/mukherjee96/parameter-binding-in-php-examples-and-discussion-3af4>

🎯在本文中，我们将研究使用 MySQLi 和 PDO 在 PHP 中进行参数绑定的四个基本例子！我从自己的项目和网络的各个角落积累了这些。如果你像我一样发现使用参数绑定很棘手，它们将帮助你开始使用 PHP。

💡如果参数绑定没有告诉你任何事情，你可能想看看我以前的文章，在那里我[解释了](https://dev.to/mukherjee96/understanding-sql-injection-and-prevention-using-parameter-binding-in-php-27n5)如何使用参数绑定来阻止 PHP 中的大多数 SQL 注入攻击。

就像上一篇文章一样，我将一步步向你展示如何使用 MySQLi 和 PDO。这样，熟悉 MySQLi 的人将能够轻松地学习 PDO，反之亦然。

### 以下是我将要讲述的内容:

*   创建 MySQLi 和 PDO 对象
*   示例 1:单参数绑定
*   示例 2:多参数绑定
*   示例 3:使用参数绑定重用查询
*   示例 4:用 SQL LIKE 实现基本搜索

[继续阅读“PHP 中的参数绑定——示例和讨论”](https://geekyminds.co.in/parameter-binding-in-php-examples-and-discussion/)

帖子[PHP 中的参数绑定——示例和讨论](https://geekyminds.co.in/parameter-binding-in-php-examples-and-discussion/)最早出现在 [GeekyMinds](https://geekyminds.co.in) 上。