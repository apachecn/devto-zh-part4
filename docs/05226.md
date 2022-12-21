# 如何编写超级超级安全、sql 注入防弹的 PostgreSQL 查询

> 原文：<https://dev.to/vbilopav/how-to-write-super-uber-mega-secure-sql-injection-bullet-proof-postgresql-queries-3n43>

SQL 注入是现代软件开发中的大问题。

即使你有一个训练有素、熟悉最佳网络安全实践的团队，也总有一些东西(由于意外或软件缺陷)可能走漏的危险。

我将在这里描述如何使您的 PostgreSQL 查询安全，因为它们可能存在任何潜在的 SQL 注入漏洞。

我要展示的并不是什么新东西。它实际上是人们用来获得最大安全的组织模式，主要是在军队和任何需要这种信息安全措施的组织中。

它被称为**“最低特权安全”**。

想象一下，你正派遣一名士兵在敌后执行一项秘密任务。你要做的是给他完成任务所需的最基本的信息。所以，如果他落在敌人手里——他不能透露比他知道的更多。原理也叫**“需要知道的基础”。**

同样的原理可以应用于任何现代数据库。让我们看看如何在 PostgreSQL 中实现这一点。

假设我们有以下从表值中返回值的查询:

```
select "value" from "values" where id = @id; 
```

现在，如果您为您的 [@id](https://dev.to/id) 变量输入未初始化，这可能会导致 SQL 注入。攻击者可能会访问您数据库，窃取或删除您的数据。

那么我们能做什么呢？

为了保护我们的查询，我们至少需要两个杰出的数据库用户:

1.  一个具有更高的特权，在本例中拥有创建和选择权限。它不必是管理员或超级用户本身，对于这个例子，它只需要能够在模式上创建新的功能，选择“值”表，并给其他用户一些授权。我们可以称之为`ddl_admin`。

2.  另一个没有特权，也没有任何资助。它只能执行数据库登录，仅此而已。我们可以称之为`app_user`,这是您的应用程序现在将使用的用户。您可以使用下面的语句创建这样的最低权限用户:

```
create role app_user with
        login
        nosuperuser
        nocreatedb
        nocreaterole
        noinherit
        noreplication
        connection limit -1
        password 'app_user_password'; 
```

用户`ddl_admin`将是我们将在其上下文中运行数据库迁移和更新的用户。

因此，我们将使用它将我们的查询包装到数据库函数中，还有一些其他的东西，我会解释，就像这样:

```
create function select_value(_id text) returns text as
$$
begin
    return (
        select "value" 
        from "values" 
        where id = _id
    );
end
$$ language plpgsql security definer;   
revoke all on function select_value(text) from public;
grant execute on function select_value(text) to app_user; 
```

因此，我们将查询包装到函数中。

我们还用语句`security definer`说过，任何运行这个函数的人都将作为实际创建它的用户(定义者)来运行。也就是说，在本例中是“ddl_admin ”,用户有权选择值表。

下一条语句将撤销对 public 函数的所有授权。默认情况下，在 PostgreSQL 上创建函数时，每个人都有执行权限。所以我们必须首先撤销它。

此时，只有`ddl_admin`可以执行该功能。所以我们必须改变这一点，将 execute 授予`app_user`。这意味着现在，`app_user`只有一个授权，那就是执行这个功能:

```
select from select_value('1'); 
```

如果在`app_user`用户环境下执行，将返回预期结果。

请注意，`app_user`只能做到这一点，其他什么也做不了。因此，如果我们试图再次执行 SQL 注入，像这样，例如:

```
select select_value('');select * from some_other_table; 
```

我们将得到结果:

```
ERROR:  permission denied 
```

就是这样，SQL 注入现在在你的系统中是完全不可能的。

这是否意味着我们已经脱离险境了。嗯，不，不是真的。有几件事需要记住:

1)始终记得申请**最低资助。**例如，如果攻击者知道该函数使用了其他函数或运算符，攻击者可能会在调用您的函数之前将 search_path 更改为另一个模式，在那里创建新的函数或运算符，并让您的函数正常执行。在这种情况下，新功能将以提升的权限执行。当然，如果您的应用程序用户没有任何创建权限，这是完全不可能的。

2)有些函数可能会使用动态 SQL。动态 SQL 是包含 SQL 语句的字符串，通过调用执行函数来执行。现在，如果那个动态查询使用一些参数，当然，SQL 注入可能发生在那里。该怎么办。老实说，如果可能的话，我会尽量避免使用动态 SQL。