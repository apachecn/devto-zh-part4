# 德米特(LoD)法

> 原文：<https://dev.to/mangelsnc/la-ley-de-demeter-lod-1eo5>

[![](img/fd4e4e95a1dc7e5b5071f0ad0c5478a1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dKFp-s9C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/3512/1%2A8fGo9dwe87JbM4FSCVuUOg.jpeg)

本篇文章的题目是[*【Demeter】*或*【demetra】*](https://es.wikipedia.org/wiki/Dem%C3%A9ter)，希腊农业女神，其名字可以翻译成*母神或代孕母亲】也许这最后一个译本启发了波士顿东北大学的人们，当他们用它来命名我们所关注的法律。*

 *Demeter 法案的目的是避免我们班过于拥挤，避免他们太了解合作伙伴的执行情况。您可以用一句话轻松定义:

> # Only talk to friends, don't talk to strangers

∞这究竟是什么意思？因为很简单，这就是说**给定的对象，后者应该只知道(和接触)其直接合作者的公开方法。**

如果我们要有更理论的观点，鲍勃·马丁将德米特定律定义为清洁码:

> # For all classes C. and all methods M attaches to C, all objects M sends messages to must be instances of classes associated with the following classes:
> 
> # 1\. The argument class of m (including c).
> 
> # 2。 Instance variable class of c language.
> 
> # (Objects created by M, or functions or methods called by M, and objects in global variables are considered as independent variables of M)

为了简化这一切，我们来看一个 Demeter 链的示例:

在这一小部分代码中，我们可以看到在第 15 和 16 行中执行 Demeter 法律的呼叫链。

让我们看看 16 号线。在这条线路上，我们访问了一个由构造函数传递给我们的依赖项，从此处，我们访问了一个名为* notification _ service *的服务，而从该服务中，我们访问了一个电子邮件子服务，最后访问了* sendwelcomeemail 方法。*如果这一切都清楚的话，那就是我们已经和许多陌生人谈过了。

> # It must be emphasized that this law only applies to objects that define behaviors (services, command handlers …), but not to objects that represent data structures, such as DTOs (entities)

## 我们是否避免违反德米特定律？

如果我们发现自己违反了 Demeter 法律，则首先要接受我们项目的基础存在体系结构问题，这可能会带来相当大的变化。

在上例中，一个非常正确的解决方案是避免向构造函数传递像容器这样大的依赖项，而只传递它需要的服务(“t0”ORM“”和“*notification _ service”*)，以便它可以访问直接参与者的方法。

解决 Demeter 定律的一种方法**不推荐**是使用**快捷方式**使我们能够通过一次呼叫访问所需的功能。这实际上只是掩盖了问题，因为 Demeter 链仍处于隐藏状态，并被分为多个类别。

让我们看一个例子:

这里我们看到的代码与以前一样，只是稍加修改:我们注入了必要的依赖项，此外，我们还在类和**类**类中添加了快捷方式方法。下面我们可以看到这些类的定义摘录，采用了新的方法:* *问题仍然存在，我们只是在地毯下面扫了一下。* *此外，我们可能无法随时选择修改这些服务，因为这些服务可能属于我们无法修改其代码的第三方。

## **结论**

有人说德梅特定律是近东救济工程处亟需的延伸——《坚实原则》——中的第五个 Beatle，当然，关注它的遵守告诉我们很多我们正在构建的架构类型及其脆弱性或坚固性。

如果我们发现问题，则必须尝试通过良好的做法来解决问题，而不是掩盖一个可能最终影响我们系统的问题。

回头见！*