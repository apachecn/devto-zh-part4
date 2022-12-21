# 使 ASP.NET 核心 api 控制器方法可以用单个属性查询

> 原文：<https://dev.to/jannikbuschke/make-asp-net-core-api-controller-methods-queryable-with-a-single-attribute-lff>

**TL；dr:** 在这篇文章中，我们将把`[EnableQuery]`属性应用到代表 GET 端点的 web api 控制器方法中。这使客户端能够查询基础数据源。分页，搜索/过滤，排序和选择是由一个单一的属性！

github 上有示例代码:

```
git clone https://github.com/jannikbuschke/pragmatic-cqrs-with-asp-net-core-for-spas 
```

* * *

这是关于创建自以为是的标准化 ASP.NET 核心 api 的迷你系列的第二篇文章。

### 系列概述

1.  【Odata 入门:公开一个可查询的 api
2.  用 Odata 增强 ASP.NET 核心 api 控制器
3.  [向客户展示 ASP.NET 核心验证](https://www.jannikbuschke.de/blog/expose-aspnetcore-validation/)
4.  [带有 MediatR(命令)和 Odata(查询)的 CQRS](https://www.jannikbuschke.de/blog/cqrs-with-mediatr-and-odata/)

* * *

## 应用【EnableQuery】

为了使用`[EnableQuery]`属性，我们首先需要按照本系列的[步骤 1](https://dev.to/odata-getting-started/) 中的描述配置 Odata。

之后，启用经常需要的查询属性是很简单的:我们只需将属性应用到 web api 控制器的 GET 方法:

```
using Microsoft.AspNet.OData;
using Microsoft.AspNetCore.Mvc;
using System;
using System.Linq;

namespace Sample
{
    [Route("api/[controller]")]
    public class PersonsController : ControllerBase
    {
        [EnableQuery]
        [HttpGet]
        public IQueryable<Person> Get()
        {
            return new string[] { "Alice", "Bob", "Chloe", "Dorothy", "Emma", "Fabian", "Garry", "Hannah" }
                .Select(v => new Person { Name = v, Id = Guid.NewGuid() })
                .AsQueryable();
        }

        public class Person
        {
            public Guid Id { get; set; }
            public string Name { get; set; }
        }
    }
} 
```

使用上面的代码，GET 端点`/api/Persons`接受几个 Odata 查询参数:支持`$take`、`$skip`、`$orderby`、`$filter`和`$select`。`$count`和`$expand`似乎只在真正的 Odata 控制器中起作用。

例子:

```
/api/Persons?$top=5               // first page with pagesize 5
/api/Persons?$top=5&$skip=5       // second page
/api/Persons?$orderby=name        // order alphabetically
/api/Persons?$orderby=name desc   // order alphabetically reversed 
```

## 与实体框架集成

注意，我们可以只返回一个由实体框架提供的`DbSet<T>`,而不是提供一个 inmemory 集合并对其调用`AsQueryable()`。Odata 会将参数应用于任何 T4 T2，并且实体框架在很多情况下能够创建完全合理、干净和高性能的 SQL。事实上，在过去的一年里，我没有遇到任何需要像 Dapper 这样更优化的微 ORM 的情况。从 v2.1 开始，Entity Framework 还允许编写原始 SQL 语句[https://docs.microsoft.com/en-us/ef/core/querying/raw-sql](https://docs.microsoft.com/en-us/ef/core/querying/raw-sql)，这与 Odata 配合得非常好(在这种情况下，会生成一个 SQL 子选择)。

## 添加自定义查询参数

如果 Odata 参数不能满足我们的需求，我们可以引入定制的查询参数:

```
[EnableQuery]
[HttpGet]
public IQueryable<Person> Get(string search = "")
{
    return new string[] { "Alice", "Bob", "Chloe", "Dorothy", "Emma", "Fabian", "Garry", "Hannah", "Julian" }
        .Select(v => new Person { Name = v, Id = Guid.NewGuid() })
        .AsQueryable()
        .Contains(v => v.Name.Contains(search));
} 
```

```
/api/Persons?$orderby=name&search=a 
```

Odata `$filter=contains(name,'a')`实际上已经提供了自定义搜索实现。然而，一些更复杂的过滤器更容易实现自定义参数。

# 总结

就这样。正如我们所看到的，一旦配置了 Odata，我们只需要一个属性就可以实现非常有用的查询功能。