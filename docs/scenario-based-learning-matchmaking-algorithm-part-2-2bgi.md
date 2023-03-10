# 基于场景的学习——匹配算法——第 2 部分

> 原文：<https://dev.to/ganeshmani/scenario-based-learning-matchmaking-algorithm-part-2-2bgi>

定期更新，订阅每周简讯，[每周更新](http://eepurl.com/gvbr7j)。

这是基于场景学习的第二部分。基于场景的学习-匹配算法-node . js-第 2 部分

您可以从第一部分了解更多关于该系列的背景知识。

[https://cloud nweb . dev/2019/07/scenario-based-learning-a-new-learning-perspective-node-js-part-1/](https://cloudnweb.dev/2019/07/scenario-based-learning-a-new-learning-perspective-node-js-part-1/)

首先，在本文中，我们将看到一个有趣的问题场景，您可能会在几个业务需求中遇到。

也就是说，你有没有想过，一个 **Swiggy** 、**优步-Eats、**和**餐厅应用**是如何工作的？。

他们如何根据我们的位置显示餐厅？。在本文中，我们将学习如何开发这样的应用程序

### 配对算法是如何工作的？

有许多应用程序，如 PUBG、优步、食品配送应用程序、酒店预订应用程序、婚姻网站都使用配对的概念。

匹配只不过是将一个简档与另一个具有不同标准或需求的简档进行匹配。

#### 例子

例如，像 PUBG 这样的游戏根据玩家级别匹配档案，像 OYO、Swiggy 和优步这样的应用程序根据位置、以前的历史和评级等匹配档案。

在本文中，我们将看到一个简单的匹配算法，它是基于位置的匹配配置文件。

### 问题场景/用户故事

这是一个问题场景，

你必须开发一个应用程序，用户注册他的详细信息，如电子邮件和位置细节。

另一方面，餐馆可以注册他们的位置和城市细节。

此外，在用户仪表板中，您需要根据用户位置显示所有附近的餐馆。

最重要的是，为了使它更有趣和更有挑战性，使用 GraphQL 开发 API。

注意:如果你是 GraphQL 的新手，我推荐你阅读这篇文章 [graphql](https://cloudnweb.dev/2019/06/graphql-with-apollo-server-and-express-graphql-series-part-1/)

[https://academind . com/learn/node-js/graph QL-with-node-react-full-app/](https://academind.com/learn/node-js/graphql-with-node-react-full-app/)

### 解决办法

#### 演示

[https://www.youtube.com/embed/uunUJx0ab98](https://www.youtube.com/embed/uunUJx0ab98)

完整源代码:[https://github.com/ganeshmani/foodmonster](https://github.com/ganeshmani/foodmonster)

主要地，这个演示源代码只包含问题场景的 API。

如果你想建立前端仪表板，你可以这样做，并作出公关，这是非常赞赏。:-)

### 应用程序的业务逻辑

神奇的事情发生在 **MongoDB** 数据库查询中。 [MongoDB 地理空间查询](https://docs.mongodb.com/manual/geospatial-queries/)。我们将用户位置存储在数据库中。

```
 const userSchema = new Mongoose.Schema({
    name : {
        type : String,
        required : true
    },
    email : {
        type : String,
        required : true
    },
    password : {
        type : String,
        required : true
    },
    salt : {
        type : String
    },
    location : {
        type : {
            type : String,
            enum : ['Point'],
            required : true
        },
        coordinates : {
            type : [Number],
            required : true
        }
    }
});
```

主要是 location 用类型**点**在数据库中存储经纬度

同样，我们将餐馆的纬度和经度存储在数据库中。

```
const restaurentSchema = new Mongoose.Schema({
    name : {
        type : String,
        required : true
    },
    city : {
        type : String,
        required : true
    },
    location : {
        type : {
            type : String,
            enum : ['Point'],
            required : true
        },
        coordinates : {
            type : [Number],
            required : true
        }
    }
});
```

同时使用用户纬度和经度搜索附近的餐馆。我们正在使用查询 **geoNear** 创建 MongoDB 数据库，该查询基于纬度和经度获取所有餐馆的详细信息。

[https://docs . MongoDB . com/manual/reference/operator/aggregation/geo near/](https://docs.mongodb.com/manual/reference/operator/aggregation/geoNear/)

最后，搜索结果将是，

[![](img/437105e1290a93bb453f9427637169c5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SMDzrKmE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/07/restarurent-1024x617.png)

### 高级问题场景

### 场景#2

除了基于位置的搜索结果，在搜索结果的顶部添加用户收藏夹、餐馆评级。

[![](img/49f73b81adcb8baa24f1da2157658464.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ika74ggv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cloudnweb.dev/wp-content/uploads/2019/07/search-1024x541.png)

### 场景#3

主要是，学习应用程序背后的算法是理解软件行业问题的关键一步。

了解更多关于稳定婚姻算法的知识，并用任何编程语言实现它。

[https://algorithm ia . com/algorithm/matching/stablemarriage algorithm/docs](https://algorithmia.com/algorithms/matching/StableMarriageAlgorithm/docs)

[https://en . Wikipedia . org/wiki/Stable _ marriage _ problem #/media/File:Gale-Shapley . gif](https://en.wikipedia.org/wiki/Stable_marriage_problem#/media/File:Gale-Shapley.gif)

[https://towards data science . com/gale-Shapley-algorithm-simpled-explained-CAA 344 e 643 c 2](https://towardsdatascience.com/gale-shapley-algorithm-simply-explained-caa344e643c2)

要了解关于 Node.js 和 MongoDB 的更多信息，互联网上有许多可用的资源。

[https://www.youtube.com/embed/fBNz5xF-Kx4](https://www.youtube.com/embed/fBNz5xF-Kx4)

[https://www.youtube.com/embed/-56x56UppqQ](https://www.youtube.com/embed/-56x56UppqQ)

我们将在接下来的文章中看到更多的问题场景，与他人分享这篇文章，以便他们能够通过练习这些问题来学习。

注意:如果你有一个想与其他开发者分享的问题场景。它将通过这个系列出版。详情请联系我

在那之前，快乐编码:-)