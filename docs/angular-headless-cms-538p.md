# 角状和无头 CMS

> 原文：<https://dev.to/itnext/angular-headless-cms-538p>

#### 建一个有棱角有内容的博客

我用 Angular 和一个 headless CMS 构建了一个博客应用程序。Contentful 为开发人员和非开发人员提供了管理 web 应用程序内容的能力。我使用 Contentful 来管理我网站上的内容。——我不是 Contentful 赞助的，任何无头 CMS 都可以替代。参见备选方案[1]

[现场演示🌌](http://blog-demo-5c8d8.web.app)

[下载源代码🚀](https://github.com/Everduin94/angular-headless-cms)

<figure>[![](img/97095a7cb41710d14ae06ff65f94bb17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ORDbr9Gd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AotCYYCOh2oqJBfhMEh20TA.gif) 

<figcaption>App 概述</figcaption>

</figure>

### 总结

*   什么是无头 CMS
*   令人满意的基础知识
*   角度:访问和显示内容
*   主办；主持

### 什么是无头 CMS

当我学习构建 web 应用程序时，我听说了内容管理系统(CMS)；比如 Wordpress 或者 Drupal。我心想，如果开发人员已经知道如何建立一个网站，他们为什么还要使用这个呢？当时，我不理解需求或用例。

后来，当我准备创建自己的副项目并将其交付给非开发人员时，我暂停了。

> 非开发人员如何更新我的 web 应用程序的内容？

我已经花了大量的时间来开发构建前端应用程序的技能。我现在需要学习像 WordPress 这样的东西来构建应用程序吗？

> **无头 CMS 已进入聊天。**

像 Contentful 一样，Headless CMS 本质上是 web 应用程序，它从开发人员和非开发人员那里抽象出数据库/后端。

无头 CMS 不提供前端。—它们确实为开发人员提供了从后端获取内容的 API，并为非开发人员提供了向后端输入内容的用户界面。

我们将构建一个 Angular 应用程序来显示数据，并使用 Contentful 来创建/管理这些数据。

### 知足常乐

我们将涵盖内容丰富的基础知识。—有关全面介绍，请参见此处的[【2】](https://www.contentful.com/developers/docs/tutorials/)。

<figure>[![](img/d88e6a2d53b510b79dc0c75f18e76509.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jp41kVPA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2Ag1Z09E_629IfmUBmn7knKw.png) 

<figcaption>内容丰富概述</figcaption>

</figure>

作为一个例子，让我们在**的五个步骤**中建立一个具有单一内容类型的新空间。

<figure>[![](img/cba78161a1cd219546d18d8f239b3320.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2Z6edxy1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/471/1%2ALYo_ucicO5L8lWmtlCDzVw.png) 

<figcaption>左上角菜单:创建空间</figcaption>

</figure>

#### 1。创建一个空间

入门就像注册一样简单。—一旦注册，我们就可以创建自己的空间。打个比方，我们可以把一个空间想象成我们自己的个人数据库。

<figure>[![](img/d98dd17265112dbfa3fa6448f45207bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NEOOhdKY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/659/1%2A5vB1AVPOwdrQAlec8Fyk_w.png) 

<figcaption>创建内容类型</figcaption>

</figure>

#### 2。创建内容类型

我们将向我们的空间添加一个内容类型。我们可以把内容类型想象成数据库中的表定义。—内容类型不是实际的内容，只是设计。

<figure>[![](img/59ffd537f4f6738e5007a60f7b7afb61.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_KcxM8HY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/746/1%2AuSJXh9sjUtGySyiaDqnHXw.png) 

<figcaption>给内容类型添加字段</figcaption>

</figure>

#### 3。创建字段

现在，我们将向内容类型添加字段。将字段想象成数据库表中的列。—在我们的示例中，我们正在创建一篇博客文章，它将包含诸如标题、描述、正文等字段

<figure>[![](img/2940d61ef0e05c8fd19e1a32fe6a8a87.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--edZk7YrL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/847/1%2AUgdiAOygoT977hy3RLw39w.png) 

<figcaption>创建新内容</figcaption>

</figure>

#### 4。创建内容

我们添加的每一条内容都类似于数据库中的一个新行。我们将填写前面在 blogPost 内容类型中定义的每个字段。

<figure>[![](img/ac88ee0ab858aa06c62846dc46e4ae3c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OXYIwFc7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/679/1%2AAuuH61fWsLUtdPSLJuMPpw.png) 

<figcaption>空间 ID &内容交付访问令牌</figcaption>

</figure>

#### 5。访问 API

在下一节中，我们将使用我们的空间 ID 和内容交付访问令牌来检索我们的内容丰富的数据，并将其显示在我们的应用程序中。

### 角度:进入&显示内容

为了开始使用 Contentful API，我们将以下对象添加到我们的 environment.ts 文件中。我们输入内容丰富的基础知识的第 5 步中的信息。