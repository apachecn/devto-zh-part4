# 用 Django 即插即用 GraphQL

> 原文：<https://dev.to/ashraful/plug-and-play-graphql-with-django-22eg>

### [Github(PNP-graph QL)v 0 . 0 . 1-beta](https://github.com/iashraful/pnp-graphql)

> 用 Python/Django 制作 GraphQL API 的库。这就像一个闪存盘，就像你插入电脑和传输文件一样。

#### 什么是 pnp-graphql？

这个小小的库是用来在一分钟内制作 GraphQL 的。它考虑基于模型的方法。比如，你的 app 很少有**图书**、**作者**、**出版**等模式。现在这个库将为所有这些模型启用一个 GraphQL API。你可以在 API 上做查询，突变。

**示例查询为**

```
query getBooks {
  bookList(limit: 500, name: "Honey") {
    id
    name
    publication {
      id
      name
    }
    authors {
      id
      name
      address
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**例创建突变为“书”**

```
mutation CreateNewBook{
  createbook(input:{
    name: "GraphQL the awesomeness of API development",
    authors: [1,7],
    publication: 103
  }) {
    book{
      id
      name
      authors{
        id
        name
      }
      publication {
        id
        name
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**例更新突变为**

```
mutation updateBook{
  updatebook(id:2, input:{
    name: "Hello Honey Bunny??"
    authors: [1,2,3]
  }) {
    book{
      id
      name
      publication{
        name
      }
      authors {
        id
        name
      }
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

**例删除突变为“书”**

```
mutation deleteBook{
  deletebook(id: 500){
    book
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 快速启动

> 文档即将发布...

*   从 pip 安装`pip install pnp-graphql`
*   在设置中添加 PnP GraphQL 配置。

```
GRAPHENE = {
    'SCHEMA': 'pnp_graphql.schema.schema'
}

PNP_GRAPHQL = {
    'ENABLED_APPS': ['example_app']
} 
```

Enter fullscreen mode Exit fullscreen mode

*   设置`DEBUG = False`用于生产。

**就是这样:)**

**现在访问:***[http://your-IP:port/API/graphql-explorer/](http://your-ip:port/api/graphql-explorer/)*进行探索 graph QL 内置 UI explorer 进行查询。

**生产就绪 API:***[http://your-IP:port/API/graph QL/](http://your-ip:port/api/graphql/)*

#### 什么在起作用？

*   GraphQL 查询
*   突变(创建、更新、删除)
*   页码
*   API 过滤数字、字符串、日期、日期时间

#### 有什么打算？

*   证明
*   适当的错误处理
*   现场验证
*   贮藏
*   更多的.........

**贡献者最受欢迎:)**