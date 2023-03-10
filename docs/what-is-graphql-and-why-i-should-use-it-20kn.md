# 什么是 GraphQL，为什么我应该使用它？

> 原文：<https://dev.to/prashanth1k/what-is-graphql-and-why-i-should-use-it-20kn>

我为什么要用 GraphQL？还有，不使用 GraphQL 的情况。

你一定看过之前的帖子，在那里我滔滔不绝地讲述了我是如何搞乱我的 Rest APIs 的。我绝不会满足于对一个单一的技术堆栈这样做。

因此，GraphQL。

### graph QL 的需求是什么？

Google GraphQL 和 refer[docs](https://graphql.org/learn/)——有比我更聪明的人有更好的答案。

但是，由于互联网可能是一个可怕的地方，我喜欢写一些我不擅长的东西——以下是我的想法要点。

回到故事..

> GraphQL 是 API 的 SQL。

在你说..啊..“我现在什么都知道了，”，请继续阅读。

你看，网上的大多数交易都涉及到获取一些数据。

客户需要被认证、授权，然后请求数据以进行这些交易。提供数据的服务器将根据预定义的规则遵从客户端的请求。

Rest 是一种架构风格，允许您标准化和简化客户机-服务器请求。您可以控制资源(帐户、联系人等)。)和操作(获取、存储等。).

到目前为止一切顺利。你甚至可能*认为*你已经达到了目前所描述的涅槃状态，但是想象一下如果你开始一系列的请求会发生什么-

```
You: Give me contacts
Server: Here are 100 contacts with 501 fields per contact

You: Give me contacts and activities for each contact
Server: Here are 1000 contacts and 1 million activities per contact

You: Give me contact detail
Server: Here you go - 1000 fields of the contact
You: Give me address for the same contact
Server: Oh God, you again? Here's the address. And, can you please stop this madness? 
```

Enter fullscreen mode Exit fullscreen mode

您将在现实世界中看到这样的例子——所描述的问题被称为“过度获取”(获取比我需要的更多的数据)和“获取不足”(获取比我需要的更少的数据)。

过量提取的特性-

*   服务器从数据库获取更多数据(或者可能查询不总是需要的相关实体)
*   更多的数据通过管道传输
*   客户端获得的数据比需要的多，并且必须处理数据以选择性地挑选和显示数据。

还有，那些用来取东西的-

*   客户端不需要一次性请求相关实体或字段的所有数据
*   额外的网络请求和更多不必要的流量
*   更多的时间来呈现完整数据的用户界面

从上面的对话中可以看出，服务器和客户端之间的来回切换使服务器感到沮丧，更重要的是，影响了客户端的性能。

多次来回请求使得 Rest 变得“喋喋不休”。

因此，GraphQL。

(我向你保证——Matrix 和我一直用‘ergo’关系不大。)

### 但是说真的，*我*为什么要用 GraphQL 呢？

我当然不会使用 GraphQL，因为它-

*   是当今 web 开发的流行趋势
*   被每个说没有什么比这更美妙的人所崇敬
*   被打印出来，这让它变得如此神奇
*   提供了一个非常好看的 IDE，使得开发查询变得非常容易
*   让服务变得如此容易编码(它所需要的只是一个手势，所有的小东西都以完美的和谐方式排列它们自己)
*   即使在点火开关打开之前，也能大幅减少网络通信量，提高“燃烧橡胶”的速度
*   使用内置的安全规则，使得提供基于角色的授权变得非常容易
*   受到服务器和客户端技术堆栈的同等喜爱
*   为数据库指定了一个良好的抽象层
*   有订阅，这些是如此如此惊人
*   是由脸书开发的。我碰巧喜欢扎克伯格

或者，我可能会因为上面提到的几点而使用 GraphQL，这一点承认起来很尴尬(或者是彻头彻尾的愚蠢)。

我使用 GraphQL 的主要原因是

*   它减少了服务器端的工作(惊喜，惊喜！？).我不必为每一个请求编写 API，或者过度设计 API 来想象所有可能的场景。因为我的大多数服务都是数据密集型的，所以我特别喜欢这样一个事实，即我不需要维护十几个端点
*   开发迭代在客户端更容易。我可以对客户端代码进行调整，以获取更多或更少的数据
*   仅向客户端和服务器传递所需的数据

但是，另一方面-

*   GraphQL 使得编写服务器端服务变得更加困难(这只是我的看法，并不是 GraphQL 本身的错)。
*   我还发现，当客户在后台窃笑时，很难控制他们请求的查询(所述客户的开发人员，而不是客户用户。由于我是许多应用程序中唯一的开发人员，我暗自窃笑自己。我需要帮助！).

我发现从启用标准 Rest 模式开始更容易(大多数时候是搭建的)。然后，我开始为特定的事务启用 GraphQL 服务，并从那里开始。

### Err..你到底在说什么？

我们来看几个例子。

如果我需要获得一个特定的联系人，我会在 REST 中执行下面的事务-

```
GET https://api.com/contact 
```

Enter fullscreen mode Exit fullscreen mode

响应将是-

```
data: {
  contacts: [
    {
      planet: "earth",
      name: "Rama",
      age: 42
    },
    {
      planet: "mercury",
      name: "Kris",
      age: 21
    },
    {
      planet: "mars",
      name: "Joe",
      age: 63
    }
  ];
} 
```

Enter fullscreen mode Exit fullscreen mode

我可以在 GraphQL 中执行以下操作，并将其作为 POST 请求发送到服务器-

```
query  {  contacts(name:  "Rama")  {  name  age  }  } 
```

Enter fullscreen mode Exit fullscreen mode

回应会是这样的-

```
data: {
  contacts: [
    {
      name: "Rama",
      age: 42
    }
  ];
} 
```

Enter fullscreen mode Exit fullscreen mode

两次交易中的反应不言自明！

### 什么是 GraphQL 操作？我为什么要在乎？

无论我们在客户机-服务器世界里做什么，我们都试图做一件简单的事情

> 服务器和客户端共享数据和业务逻辑

用户可以查询、插入、更新或删除数据，并可以从客户端启动这些事务。这可能发生在-

*   服务器不关心客户机的“状态”。即，客户端发送请求(如果需要，包括作为请求一部分的认证)，服务器应答呼叫
*   服务器将客户的利益放在心上，与客户有一个开放的通道，并跟踪数据，以便客户在发生变化时得到变化

GraphQL 在数据库之上构建了一个层来帮助您在这两种情况下传输数据。

##### 查询

GraphQL 查询帮助您只获取您需要的记录和属性——我们在前面已经看到了例子。

您还可以在请求中包含不同的实体(将“表”作为类比)。使用 GraphQL 获取具有指定关系的记录，而不是对不同的实体发出单独的请求。

##### 突变

GraphQL 突变用于插入/更改数据。

```
mutation  createContact  {  addContact(name:  "Joe",  age:  100)  {  id  }  } 
```

Enter fullscreen mode Exit fullscreen mode

上面的查询提供了使用一个名为`createContact`的变异的指令，这个变异又使用`addContact`来添加一个名为`Joe`和年龄为`100`的联系人记录。

##### 订阅

订阅在服务器和客户端之间建立了一个通道。客户端可以订阅记录更新，并在发生更改时得到通知(通过简单的通知，或者通过服务器实际“推送”数据)。

订阅在幕后使用“网络套接字”。这是一项久经考验的技术，能够向客户端实时更新数据。

例如，客户可以使用下面的订阅来获取赢得彩票的联系人列表。

```
subscription  listenWonLottery  {  wonLottery  {  name  deals  }  } 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看流程-

1.  客户订阅联系人
2.  当一个或多个联系人赢得彩票时，他们在数据库中得到更新
3.  在数据库更新时，会向客户端触发一个通知
4.  通知包含联系人姓名和与联系人关联的交易
5.  客户端可以在其上显示带有联系人姓名的弹出窗口，并显示飞行气球的效果

### 实现 GraphQL 需要哪些不同的组件？

GraphQL 只是 API 的语言和执行查询的运行时。它没有硬连线到数据库，也没有指定需要如何执行数据库操作。

实现 GraphQL 堆栈的不同组件如下。这些组件不是 GraphQL 规范的一部分，而只是支持使用 GraphQL 的事务的堆栈的一部分。

##### 服务器

实现/技术栈决定如何将 GraphQL 合并到事务中。服务器从客户端接收 GraphQL 请求，建立与数据库的连接，将图形查询转换为数据库理解的语言，获取结果并将响应发送回客户端。

服务器还可以包括“普通”服务器所期望的功能

*   将业务逻辑应用于数据
*   在指定的网络端口上侦听支持的请求类型
*   负责为客户端请求提供服务所需的所有支持基础设施。

##### 数据库

存储数据并维护原子性、一致性等。等等。为了数据。它是一个被美化了的文件系统，支持单独的事务，不会因为网络瘫痪或者你的客户决定玩游戏而弄乱数据。

##### 客户端

可以是浏览器、移动应用或任何从服务器请求数据并消耗服务器响应来做“某些事情”的东西。我们创建 GraphQL 查询、变异等。并通过简单的 POST 请求发送给他们。

### 有哪些我应该熟悉的术语？

##### 图式

模式是 GraphQL 的构建块。它是用我们可以理解的语言描述的——可以是任何语言，但是在标准实现中使用了一个简单的选项，名为“模式定义语言”。

考虑下面的例子-

```
type  Contact  {  name:  String  deal:  Deal  }  type  Deal  {  description:  String  value:  String  } 
```

Enter fullscreen mode Exit fullscreen mode

模式定义帮助客户端开发者查看和理解数据实体之间的关系，并在此基础上构建 GraphQL 查询。

##### 解析器

解析器是一个函数，它以模式指定的形式返回数据。

例如，-

```
const resolvers = {
  Query: {
    contact(root, args) {
      return contacts.filter(contact => contact.id === args.id)[0];
    },
    deals(root, args) {
      return deals.filter(deal => deal.id === args.id)[0];
    }
  },

  Contact: {
    deals: contact => {
      return deals.filter(deal => deal.contactId === contact.id);
    }
  }
}; 
```

Enter fullscreen mode Exit fullscreen mode

我们使用解析器来编写查询、变异和订阅。

##### 对象类型/字段

在上面概述的 GraphQL 查询中

*   对象类型为“联系人”和“交易”
*   字段有`name`、`description`和`value`

### 这太酷了，我把文章都睡过去了。直截了当地告诉我——我应该使用 GraphQL 吗？

不是所有地方。

当您需要获取大量具有不同形状/多种关系的数据时，请使用 GraphQL。使用 GraphQL 简化客户机和服务器之间的数据传递。

与此同时-

1.  请记住，普通的 Rest API 实现更简单
2.  即使在数据密集型应用中，我也看不出在任何地方应用 GraphQL 有什么大的优势(也许除了架构一致性/标准化)。例如，如果您的实体之间的关系很窄，并且不总是需要相关的数据视图
3.  跟踪客户端如何使用 graph QL——疯狂的查询会导致性能问题

为什么我觉得 Rest 服务更简单？

1.  服务器上的端点设置很简单。公开一两个 API，并使用 ORM 在一两个步骤中获取/更新数据——除非您想处理字段和相关实体，否则在任何地方都看不到它们
2.  除了前一点之外，或者补充一点，工具是惊人的——这项技术是如此的古老和受支持，以至于大多数时候它只需要几次点击(或者几行代码)就可以完全设置好
3.  我在后端控制查询——更容易控制谁访问数据以及如何访问。我还发现解决潜在的性能问题更容易，因为我控制服务器上的过滤器和排序
4.  但说到底，我可能会发现休息更容易，因为我已经花了这么多时间:)

### 好，我在。我应该从哪里开始？

我发现下面的技术栈很容易理解，并且从长远来看是有用的。推荐倾向于 Vue，因为我一直在使用 Vue，并且坚信 Vue 对初学者来说更容易。

##### 启用 GraphQL 服务器端

最好自己创建一个服务器，看看 GraphQL 在后端的真实流程。

| 种类 | 被推荐的 | 评论 |
| --- | --- | --- |
| 我懂 SQL，懂 DBs | [海报](https://www.graphile.org/postgraphile/) | 参考[这个](https://techformist.com/spin-up-your-own-local-graphql-server-within-15-min/) |
| 我想了解更多关于无服务器的信息 | [AppSync](https://aws.amazon.com/appsync/) | 在亚马逊 AppSync 上注册 1 年有限免费服务器 |
| 我想使用 CMS 学习 GraphQL | [StrapiJS](https://strapi.io/) | 处于测试阶段，但适合生产 |
| 我想尝试静态网站+ GraphQL | [网格体](https://gridsome.org/) | 新的，但很有前途。 |
| 我知道 PHP | 拉勒维尔+ [灯塔](https://lighthouse-php.com/) |  |
| 我期待一段长期的关系 | 在雀巢 | 良好的服务器功能+良好的图表 |
| 我是大师 | 引导我走向觉悟，也许 | 你在这里做什么？ |

##### 在客户端使用 GraphQL

*   [视图](https://vuejs.org) + [视图-阿波罗](https://github.com/Akryum/vue-apollo)
*   [苗条](https://svelte.dev/) + [阿波罗](https://github.com/apollographql/apollo-client/tree/master/packages/apollo-boost)。

参见:[创建一个简单的待办事项](https://techformist.com/create-a-simple-to-do-app-using-svelte-and-graphql/)

### 完毕

到此结束:)