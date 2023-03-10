# 为什么在应用程序开发中使用 PostgreSQL 函数

> 原文：<https://dev.to/vbilopav/why-use-postgresql-functions-for-your-application-development-bik>

这部分自述文件来自我的[postgrest.net](https://github.com/vbilopav/postgrest.net)POC 项目。它将 PostgreSQL 数据库直接转换成 RESTful API。NET 核心，从而将整个应用程序/业务逻辑转移到 PostgreSQL

我发现当使用 PostgreSQL JSON 参数作为输入和结果时，有很大的灵活性来提高生产率。

尤其是即将推出的 PostgreSQL 新支持

然而，很多人根本不愿意使用 SQL，更不用说了。我认为这是一个错误。

下面是您为什么想要构建这样的应用程序的好处和原因:

## 好处

自 SQL-92 以来，发生了很多变化。

现代 SQL 语言是[图灵完成](https://en.wikipedia.org/wiki/Turing_completeness)演算引擎。它是唯一一种成功的第四代程序设计语言，带有第五代的元素。

相比之下，C#(以及 Java、JavaScript、Python 等)是先进的第三代语言。

因此，使用现代 SQL 是非常有意义的，比如为您的整个应用程序后端逻辑使用 PostgreSQL 实现。

好处如下:

### 生产力

SQL 也是一种声明性语言，它对用户(或本例中的程序员)抽象(或隐藏)硬件和算法。

这意味着我们用 SQL 编程是通过告诉机器**做什么**而不是**如何**做我们想要的。机器会为我们计算出**如何**(在大多数情况下)。理想情况下，作为程序员和开发人员，我们所要做的就是声明我们想要的结果。

可以说这是函数式编程的最终形式——不是声明一个函数来返回我们想要的结果——我们只是简单地声明我们想要的结果，让机器来决定如何去做。就这么简单。但这需要练习。

为了能够实现如此高级的概念，SQL 需要对程序员抽象或隐藏不重要的细节，例如整个硬件、操作系统和算法。所以没有处理器，没有内存，没有文件系统和文件，没有进程，没有线程，当然还有锁。还有，没有数据结构字典，哈希表，链表，什么都没有。实际上唯一剩下的就是——你的数据和你的业务逻辑。

SQL 让您专注于您真正需要的东西、您的数据和您的业务逻辑。

所以，是的，SQL，假设程序员接受了适当的培训——SQL 可以让整个开发团队和单个程序员变得非常有效率。
我强烈建议观看技术讲座[现代 SQL 数据库如何提出你做梦也想不到的算法，作者是 Lukas Eder](https://www.youtube.com/watch?v=wTPGW1PNy_Y)

生产力可以给你在市场上带来巨大的竞争优势。

### 可维护性和可变性

可维护性和可变性是两种可以互换的属性。同样，如果生产力水平较高，维护和更换率也会较高。

然而，当使用 PostgreSQL JSON 字段时，我们可以在不停止或重新部署任何东西的情况下更改结果。
例如下面的端点:

```
create function rest__get_my_data_set(_query json) returns json as
$$
declare _company json;
declare _company_id integer;
begin
    select to_json(c), c.id into _company, _company_id
    from (
        select id, name
        from companies c
        where user_id = (_query->>'user_id')::int --_query parameter is query string serialized to json
    ) c;
    return json_build_object(
        'company', _company,
        'sectors', (
            select coalesce(json_agg(s), '[]') from (
                select id, name from sectors where company_id = _company_id order by company_id
            ) s
        )
    );
end
$$ language plpgsql; 
```

我们可以用新版本安全地替换它，新版本插入一个日志语句，并向结果(地址)添加新字段:

```
create function rest__get_my_data_set(_query json) returns json as
$$
declare _company json;
declare _company_id integer;
begin
    select to_json(c), c.id into _company, _company_id
    from (
        select id, name, address
        from companies c
        where user_id = (_query->>'user_id')::int --_query parameter is query string serialized to json
    ) c;

    -- this will create log as normal .net core log.information would
    raise info 'selected company: %', _company;

    return json_build_object(
        'company', _company,
        'sectors', (
            select coalesce(json_agg(s), '[]') from (
                select id, name, address from sectors where company_id = _company_id order by company_id
            ) s
        )
    );
end
$$ language plpgsql; 
```

就是这样。

现在我们有了一个日志声明，它将帮助我们了解正在发生的事情，我们正在返回到一些新的字段。我们可以添加聚合或 SQL 允许的任何东西，实际上是任何东西。

维护问题的反应时间减少到最低限度。你可以以后再做软件变更官僚- **客户不能等。**

### 表演

仅仅使用 PostgreSQL 函数并不意味着执行计划会被自动缓存。

执行计划是 SQL 引擎在您告诉它您想要什么时为您的结果声明预先选择的算法。这是第五代语言的要素——它抽象(隐藏)了算法，因此程序员可以专注于业务逻辑。

执行计划是在执行前即时确定的。对于重复出现的相同语句，可以进行缓存。目前，PostgreSQL 函数的执行计划只有在以下情况下才可以缓存:

*   函数写在`plpgsql`里。

*   该功能在单个会话中运行超过 5 次，

*   通用计划并不比非通用计划差很多，

*   不使用动态 SQL，

*   这些计划不会在会话之间共享。

然而，除了缓存您的算法或执行计划之外，仅仅通过节省网络操作(带宽+延迟)就可以获得很多性能提升。因为大多数操作都发生在服务器上，不需要太多的网络操作。在上面的例子`rest__get_my_data_set`中，通常会有两个查询被发送到服务器，并对网络延迟产生影响。

当然，一些反模式，比如 N+1 查询，肯定是要避免的。

此外，拥有高内聚性的 SQL 代码(彼此靠近，更容易找到)——IIT 更容易确定性能瓶颈，也更容易调优索引。

### 安全

几乎所有的数据库系统都有自己的安全性。通过使用 PostgreSQL 函数，您可以充分利用 PostgreSQL 基于角色的安全性，并在您的应用程序周围添加另一个安全层，将您的应用程序安全性提升到另一个级别。

以下是关于如何利用 PostgreSQL 来保护您系统免受未授权访问和 SQL 注入的完整说明:
[如何编写超级超级安全、SQL 注入防弹的 PostgreSQL 查询](https://dev.to/vbilopav/how-to-write-super-uber-mega-secure-sql-injection-bullet-proof-postgresql-queries-3n43)