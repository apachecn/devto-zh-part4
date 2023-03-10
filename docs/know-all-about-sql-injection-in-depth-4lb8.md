# 深入了解 SQL 注入

> 原文：<https://dev.to/nileshsanyal/know-all-about-sql-injection-in-depth-4lb8>

这是我个人博客的一篇文章，位于[这里](https://www.devhelperworld.in/2019/06/sql-injection.html)

**SQL 注入**是一种用于攻击 web 应用的常见攻击技术。在本帖中，你将深入了解不同类型的 SQL 注入攻击。

读完这篇关于 SQL 注入的帖子后，你会对它有一个深入的清晰概念。

[![Sql Injection](img/2931c35b4bf3932e4b87f396db6a8569.png "Sql Injection")](https://res.cloudinary.com/practicaldev/image/fetch/s--FCLHo7C2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-uA531lpW5_w/XQ2-8DvUuyI/AAAAAAAABA0/7ppyILgXJfMR5GeogR0OmcN78afUo3DkQCLcBGAs/s320/sql-injection.jpg)

这是一种攻击技术，用于通过欺骗数据库引擎来利用应用程序，使攻击者能够查看他们通常无法检索的数据。

这可能包括属于其他用户的数据，或者应用程序本身能够访问的任何其他数据。在许多情况下，攻击者可以修改或删除这些数据，从而导致应用程序的内容或行为发生持久变化。

## **有哪些不同类型的 SQL 注入**

### **联盟基础**

[![Union Based Injection](img/7395e6fcc08da3ce4547165e7883a1fe.png "Union Based Injection")](https://res.cloudinary.com/practicaldev/image/fetch/s--t7GOczAA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-aiEnVSrHtLE/XQ2_YjAHpII/AAAAAAAABA8/RvDPr-CHPtYFiuGFe4QTeB8ZPbcQuv82wCLcBGAs/s320/union-injection.png)

**UNION** 操作符用于连接多个 SQL 查询。攻击者可以使用该运算符创建一个查询，这样新查询的结果将与原始查询的结果相结合，从而允许攻击者一次执行多个查询。

### **误差基础**

[![Error Based Injection](img/26cb20bc7a87bac60ca705f14393c75f.png "Error Based Injection")](https://res.cloudinary.com/practicaldev/image/fetch/s--mmyE2hIr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1.bp.blogspot.com/-qKwp7B4sll0/XQ2_kGjGY8I/AAAAAAAABBM/TsdrbNplkYw9wAVRtFiqyz0XpmukA42ygCLcBGAs/s640/error-based-injection.jpg)

在这种注入技术中，攻击者依靠数据库服务器生成的错误消息来获取有关数据库结构的信息。在某些情况下，仅这种攻击就足以让攻击者枚举整个数据库。

### **盲目 SQL 注入**

当没有向攻击者提供详细的错误消息时，使用这种技术。通常情况下，应用程序会显示用户友好的错误消息，很少或没有技术错误消息。

这种注射分为两种。

#### **布尔基础**

在这种类型的攻击中，攻击者创建 SQL 查询，以便在执行查询后，返回的结果不是真就是假。此后，通过检查真值或假值的结果，攻击者了解到应用程序容易受到这种类型的攻击。然后他或她利用这一点来进一步开发应用程序。

例如，假设下面的网页显示 id 等于 2 的文章细节。

*http://www.example.com/post.php?id=2*

如果存在 SQL 注入弱点，则在 web 应用上执行以下请求:

*http://www.example.com/post.php?id=2+and+1=1*

应该返回相同的网页:

*http://www.example.com/post.php?id=2*

这是因为 SQL 语句“and 1=1”总是正确的。

对 web 应用程序执行以下请求:

*http://www.example.com/post.php?id=2+and+1=0*

将返回一个友好的错误或根本没有页面。这是因为 SQL 语句“and 1=0”始终为假。

#### **时间基础**

在这种类型的攻击中，攻击者以这样一种方式构建 SQL 查询，如果查询成功，数据库将被迫等待一段时间(以秒为单位)才能响应。如果查询在一段时间后生成响应，那么攻击者就知道攻击成功了。

例如，如果将以下请求发送到 web 应用程序:

*[http://www.example.com/post.php?](http://www.example.com/post.php?id=2-SLEEP(15))id = 2-睡眠(15)*

如果页面延迟一段时间后才显示，那么应用程序就容易受到基于时间的攻击。

## **最后的话**

如果你认为这篇文章有帮助，请分享和评论你的想法。谢谢大家！