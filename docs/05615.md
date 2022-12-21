# 为什么改变让我放弃？

> 原文：<https://dev.to/am2/why-does-alter-let-me-drop-d9b>

[![](img/a877a9d178974b0cef6b2af17de4e78a.png)](https://www.youtube.com/watch?v=m3f0aW_-6gc) 

我最近和一个同事聊天，讨论为什么对表、过程等没有离散的`DROP`权限。对话实际上来自于一个关于 [SQL Slack](http://sqlslack.com) 的 [#SQLHelp](https://sqlcommunity.slack.com/messages/C1MS1RA4B) 问题，这个问题的大意是“如果我想让一个用户`CREATE`和`ALTER`对象，而不是`DROP`它们，该怎么办？”

答案是，如果你能`ALTER`一个对象，那么你也有权限`DROP`它(如果你*真的*想要阻止`DROP`命令，你可以使用 DDL 触发器强制回滚)。我从未过多考虑过*为什么*权限是这样的问题——事实上，我经常忘记没有`DROP`权限，当我试图授予或拒绝该权限时，不得不重新学习它。

## 跌落具有破坏性

掉东西是破坏性的。任何曾经摔过盘子或汽车的人都知道这一点。但是删除一个表、过程或数据库会使它完全消失。取消某些东西的唯一方法是从备份中恢复它，或者从源代码控制中重新部署(你**在源代码控制中有你所有的代码&模式，对吗？).回到最初问题的前提(改变对象但不完全让它消失是可以的)，我问…这甚至有关系吗？你在阻止什么？丢弃一个对象比改变它更糟糕吗？**

 **## 涂改也是破坏性的

改变一个物体和丢弃它具有同样的破坏性。让我们想一想改变存储过程会完全破坏代码的最简单的方法。如果你从一个存储过程中移除一个参数，并且它正被调用代码使用，它将开始失败，并显示一个错误:

```
CREATE OR ALTER PROCEDURE dbo.Widget_Get 
    @WidgetID INT
AS ...;
GO
EXEC dbo.Widget_Get @WidgetID = 1, @ObsoleteParam = 'Andy'; 
```

> Msg 8144，16 级，状态 2，程序 dbo。Widget_Get，Line 0 [Batch Start Line 0]
> 
> 过程或函数 Widget_Get 指定的参数过多。

类似地，添加一个新的必需参数，如果调用代码没有使用*也将失败，并显示一个错误:* 

```
CREATE OR ALTER PROCEDURE dbo.Widget_Get 
    @WidgetID INT, 
    @Active bit 
AS ...;
GO
EXEC dbo.Widget_Get @WidgetID = 1; 
```

> Msg 201，16 级，状态 4，程序 dbo。Widget_Get，Line 0 [Batch Start Line 0]
> 
> 过程或函数“Widget_Get”需要参数“@Active”，但未提供该参数。

但这就是无辜的错误会破坏东西的原因。

#### 如果有人恶意中伤怎么办？

也许心怀不满的 Doug 知道你在源代码控制中没有那个过程，想销毁`dbo.Widget_Get`的生产代码，让它难以恢复。如果他不能删除程序，他仍然可以销毁生产代码…这将是超级容易的。心怀不满的道格可以删除所有代码而不丢弃程序:

```
CREATE OR ALTER PROCEDURE dbo.Widget_Get 
    @WidgetID INT
AS RETURN 0;
GO
EXEC dbo.Widget_Get @WidgetID = 1; 
```

> 命令成功完成。

#### 好吧，但是表呢？

如果你能改变一张桌子，但不能让它掉下来，你能把它弄坏到什么程度？你可以截掉它。`TRUNCATE TABLE`的权限包含在`ALTER TABLE`里……所以如果你想恶意的话就把数据扔了吧。但是可能有外键约束会使截断表变得困难。不然你怎么会有破坏性？

让我们看一个例子。`dbo.Person`表可能是数据库的核心。我可以想象有几十个，甚至几百个 fk 指向`PersonID`，让不满的道格很难截断这张表:

```
CREATE TABLE dbo.Person(
     PersonID       int          NOT NULL,
     PersonType     nchar(2)     NOT NULL,
     Title          nvarchar(8)      NULL,
     FirstName      nvarchar(60) NOT NULL,
     MiddleName     nvarchar(60)     NULL,
     LastName       nvarchar(60) NOT NULL,
     Suffix         nvarchar(10)     NULL,
     ModifiedDate   datetime     NOT NULL,
     CONSTRAINT PK_Person_BusinessEntityID 
          PRIMARY KEY CLUSTERED (PersonID)
); 
```

心怀不满的道格想要打破这个*快*。他必须在 HR 出现在他的办公桌前带他出去之前把东西弄碎。更新列不够快。他可以放下所有的柱子！

```
ALTER TABLE dbo.Person
     DROP COLUMN PersonType, 
                 Title, 
                 FirstName, 
                 MiddleName, 
                 LastName, 
                 Suffix, 
                 ModifiedDate; 
```

现在数据都没了。剩下的就是那些讨厌的。

### 好的，但是权限呢

删除对象也会删除权限。所以有人可能会说，阻止删除至少会保留权限——大概这样您就可以恢复对象定义，而不必恢复权限？对我来说，这似乎是一个稻草人的论点。如果您从某个地方恢复对象定义(源代码管理？后援？)，您可以从同一位置恢复权限。权限非常重要——它们真的应该放在源代码控制中。

## 总结

是的，`DROP`权限包含在你`GRANT ALTER`的时候。这是因为有人可以有意或无意地完全摧毁一个物体，而不需要让它掉落。在某种程度上，放弃只是改变它的最极端的版本。

帖子[为什么涂改让我掉？](https://am2.co/2019/08/why-does-alter-let-me-drop/)最早出现在[的安迪·M·马龙-安](https://am2.co)。**