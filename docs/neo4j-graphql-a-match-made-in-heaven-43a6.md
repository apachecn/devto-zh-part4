# neo4j & graph QL——天作之合

> 原文：<https://dev.to/muddybootscode/neo4j-graphql-a-match-made-in-heaven-43a6>

在过去的 8 个月里，我一直在做我的第一份有报酬的工作，作为一名自由软件开发者。这是一个我构思、推介并卖给企业的项目，尽管事实是，到那时为止，我从未构建过任何类型的企业级软件项目。我写了一些关于我作为软件开发人员的旅程，如果你愿意，你可以在这里或者在[媒体](https://medium.com/@firsttogo98)上阅读我的一些帖子，这些帖子深入探讨了这个故事的这一部分。但是可以说，这个项目对我来说是一个巨大的飞跃。所以你可能会想，我怎么能推销这个想法，更不用说实现它了？

答案可能是由标题给出的，那就是我能够利用图形的力量，特别是图形数据库 Neo4j 和 GraphQL 每个人都喜欢的 jumped up 查询语言。当我发现 Neo4j 时，我立即对它能够表达高度关联的数据的方式以及我如何轻松地使用内置的数据可视化工具来探索数据留下了深刻的印象。我可以“看到”数据，这是我在使用 SQL 数据库或从表和外键的角度思考时从来无法做到的。当你看一个图时，你可以从字面上理解如何遍历它，这使得从 A 到 B 以及两者之间的所有点变得容易。

到了推介这个项目的时候，我能够向客户详细展示他们的数据是如何连接的，并且我将处理他们用例的复杂性。本质上，我向他们展示了这个:

[![Alt Text](img/6eb9cdbf8c0ca23edb35e29370df363e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--K65tohHP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bpi8dum2winwqpzyylzr.png)

而不是这个:

[![Alt Text](img/ab626e1dded0e09b80873b8f810defe5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GVbQ67FP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t6atyu389wpavt9636qc.png)

一种是无需培训或专业知识就能容易理解的，另一种则不然。每当客户有关于处理某些情况的问题时，我可以从我拥有的数据中提取一个简单的例子，并一步一步地引导他们，并标注关系。这是一个灌篮，我说服了一屋子反对技术的人让我数字化他们的数据，并在一个没有经验的软件开发者身上冒险。

同样，通过 [GRANDstack](https://grandstack.io/) 将 Neo4j 与 GraphQL 配对，让我能够准确地获得我想要的数据。我需要一份任务文档，以及所有相关信息吗？没问题。像这样在你的模式中构建它:

```
type Assignment {
    id: ID
    volPage: String
    book: String
    effectiveDate: String
    dateFiled: String
    docType: String
    comments: String
    wellsDrilled: String
    grantor: Operator @relation(name:"GRANTOR" direction: "IN")
    grantee: Operator @relation(name:"GRANTEE" direction: "OUT")
    totalAcres: Float @cypher(
        statement: "MATCH (this)-[r:ASSIGNMENT_INTEREST]->(:Tract) return sum(toFloat(r.acres)) "
    )
    assignmentInterests: [AssignmnentInterest]
} 
```

当你查询的时候，你会得到一个形状像这样的对象:

```
query {
  Assignment {
    id
    volPage
    book
    effectiveDate
    dateFiled
    docType
    comments
    wellsDrilled
    grantor {
      id
      name
    }
    grantee {
      id
      name
    }
    totalAcres
  }
} 
```

Neo4j-graphql-js 允许你在你的 api 中直接使用 Neo4j 的图形特性。想要链接两个节点？只需告诉您的模式存在关系。想要在关系上放置属性吗？做成一个类型:

```
type AssignmnentInterest @relation(name: "ASSIGNMENT_INTEREST"){
    id: ID!
    from: Assignment
    to: Tract
    acres: String
} 
```

想做些数学或者更复杂的东西吗？用 Neo4j 的查询语言 [Cypher](https://neo4j.com/developer/cypher-query-language/) 替你干脏活。

```
avgMineralPercentage: Float
    @cypher (
        statement: "MATCH (:Tract)<-[r:MINERAL_OWNERSHIP]-(this) return avg(r.mineralPercentage)"
    ) 
```

在单个网络请求中从一端到另一端遍历图形的能力是惊人的，有时感觉有点像拥有数据超能力。我不会在具体的例子中走得太远，这足以说明 Neo4j 和 GraphQL 的结合让我有能力解决一个困难的问题，并使它看起来简单。我将很快写更多关于具体的用例及例子。在那之前，如果你正在寻找一种方法使你的数据争论变得更容易，看看这两种技术，看看你是否和我一样认为它们是天生的一对。