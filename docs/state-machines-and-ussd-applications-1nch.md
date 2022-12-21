# 状态机和 USSD 应用

> 原文：<https://dev.to/kevinmungai/state-machines-and-ussd-applications-1nch>

# 简介

老实说，我一直对 USSD 的申请有疑问。我希望有一种方法可以让创建 USSD 应用程序变得易于管理。

通常，USSD 应用程序会从一个州转移到另一个州。必须处理几种情况。

1.  我们期望什么输入和什么类型？
2.  我们如何处理空箱子？
3.  我们能回去吗？
4.  我们想要记录当前状态吗？
5.  我们想要对我们的 ussd 状态机进行版本化吗？

# 问题陈述

这是从[非洲对话六月技术挑战](https://github.com/AfricasTalkingTalent/TechnicalChallengeJune2019)中借用的，只是第一部分。

> 用户之旅:用户拨打 USSD 代码，系统会提示输入用户名和电子邮件地址。

# 这是什么状态机？

我不会试图让这个变得复杂，但我会选择在一个`ussd.edn`文件中使用法线贴图。

有两种固定状态`:start`和`:finish`。这些告诉我们我们在哪里。我们也有他们自己的集合中的所有有效选择，这些选择是预期的用户输入，此外我们还有版本控制。

这一点的吸引人之处在于，这个地图可以由一个对编程一无所知的人来更改。

布局非常简单

| 初速电流状态 | 通过 | 次状态 |
| --- | --- | --- |
| :开始 | :初始化 | :文本 |
| :就绪 | "1" | :查询-电子邮件 |
| :查询-电子邮件 | :空的 | :查询-电子邮件 |
| :查询-电子邮件 | :非空 | :查询-用户名 |
| :查询-用户名 | :空的 | :查询-用户名 |
| :查询-用户名 | :非空 | :完成 |

```
{:fsm  {:start  {:init  {:text  "Welcome to the USSD application.\nWe are registering users.\nPress 1 to continue or just cancel.",  :next-state  :ready}}  :ready  {"1"  {:next-state  :query-email  :text  "Please enter your email address."}}  :query-email  {:empty  {:next-state  :query-email  :text  "Sorry, the text is empty.\nPlease enter an email"}  :not-empty  {:next-state  :query-username  :text  "Please enter a username."}}  :query-username  {:empty  {:next-state  :query-username  :text  "Sorry, the text is empty.\nPlease enter a username"}  :not-empty  {:next-state  :finish  :text  "Thank you for participating in the registration."}}  :finish  :finish}  :choices  #{"1"}  :version  "0.0.1"} 
```

Enter fullscreen mode Exit fullscreen mode

# 回调

当我们的 ussd 应用程序收到来自 Africastalking 服务的回调时，我们得到:

1.  会话 id
2.  电话号码
3.  网络代码
4.  服务代码
5.  文本

# 数据库的外观

```
{"ATUid_7c6bad2c84927fcd17cf8331b5c3e497"
  {:phone-number +254XXXXXXX
   :network-code XXXXXX
   :service-code *384*XXXX#
   :text ""
   :current-state :start
   :current-state-response "Welcome to the USSD Application\n"
   :past-state [{:from :start :via :init :ready}]} 
```

Enter fullscreen mode Exit fullscreen mode

通过像这样存储所有过去的状态，我们能够发现我们的用户在哪里遇到了问题，或者甚至创建一种情况，允许我们的用户返回，如果他们有某种错误的话。

# 概述

会话是否存在？我们检查当前状态，然后使用 ussd 有限状态机来获取下一个状态，然后用文本响应进行响应。

示例:

```
{:next-state  :query-email  :text  "Please enter your email address."} 
```

Enter fullscreen mode Exit fullscreen mode

如果会话不存在？我们只需将新的会话 id 添加到数据库中。

# 代码如下

[https://gist . github . com/kevinmungai/4a 8 fcf 9 a 56 a 055 DC 1 AC 6 EB 9884 db 987 f](https://gist.github.com/kevinmungai/4a8fcf9a56a055dc1ac6eb9884db987f)