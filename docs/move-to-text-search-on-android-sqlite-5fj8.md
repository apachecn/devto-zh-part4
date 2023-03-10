# 移动到 Android SQLite 上的文本搜索

> 原文：<https://dev.to/serveme/move-to-text-search-on-android-sqlite-5fj8>

我们有一家名为 Zaki 的初创公司，这是一家阿拉伯烹饪社交媒体，我们在 Zaki 上的最佳功能之一是用户可以添加食谱并稍后返回。关于阿拉伯世界的一件重要事情是，互联网成本很高，而且并不总是可用，所以我们选择离线制作最喜爱的食谱。

在此功能的早期，将这些喜爱的食谱存储在 SQLite 中并显示在列表上已经足够了，用户很高兴拥有它，但之后用户开始将越来越多的食谱添加到他们的收藏夹中，这将寻找食谱的过程变成了一种痛苦。

为了解决这个问题，我们需要让最喜爱的食谱搜索起来容易，对吗？让我们深挖一下，看看是否有那么简单。

# **现状**

首先，我们必须知道当前的情况是什么，以及我们选择如何将 SQLite 上的数据存储为数据库上的 JSON。

我们没有关系或外键，这有助于使查询超快。

我们正在使用 ORMLite，这是一个在 Room 进入 Android 之前创建的 ORM(我不推荐任何人使用它，现在 Room 更好)。

如何在 SQL 上搜索

任何有数据库背景的人都会告诉你

Select * from recipes_table 其中 recipe_content 类似于“%searchWord%”

是一个非常糟糕的解决方案有人问我，即使我对 recipe_content 列有索引，是的，即使你有那个索引。因为以通配符开始并以通配符结束将使您的索引没有任何好处，并使查询变慢

如果使用常规索引，就会出现这种情况。但是如果您使用的是全文索引，那将非常适合您。

那么 SQLite 的包中有全文索引吗？抱歉，他们没有这个，但取而代之的是，它有一个基于他们所谓的虚拟表的 FTS 模块，你可以在这里了解更多信息。这意味着常规表不能解决这个问题，您需要转移到虚拟表

# **解**

# **T3】FTS 版 T5】**

根据前面的解释，我们的解决方案是 FTS，它有三个版本 [3 4](https://www.sqlite.org/fts3.html) 和 [5](https://www.sqlite.org/fts5.html) 你可以根据你支持的最低 android SDK 在它们之间进行选择

我选择 4，它对 Android 11 有很好的支持,这大约是 100%的 Android 市场。

ORMlite 在查询级别上不直接支持 FTS，所以我们需要为此创建一个自定义代码。顺便说一下，房间版本 2.1 已经完全支持 FTS

# ***迁移***

现在，我们已经准备好实施解决方案，并且知道实施的所有细节，一切都很好。但是等等，我们仍然有迁移问题。正如我之前告诉你的，我们有一个已经有用户数据的表格，这些数据在某些情况下对用户很重要，这是让用户继续使用应用程序的原因。

*getDAO* ( **上下文**)。executeRaw( **"使用 fts3 创建虚拟表`recipes_table`(`addedDate`VARCHAR DEFAULT ' Fri 2016 年 1 月 1 日 00:00:00 GMT+00:00 '，`_id` BIGINT，`recipe_content`VARCHAR)；"**)；

*getDAO* ( **上下文**)。executeRaw( **"插入到`recipes_table1` (docid、`_id`、`addedDate`、`recipe_content`)从`recipes_table` "** 中选择`_id`、`_id`、`addedDate`、`recipe_content`)；

*getDAO* ( **上下文**)。execute raw(**" DROP TABLE`recipes_table`；"**)；

*getDAO* ( **语境**)。executeRaw( **"将表格`recipes_table1`重命名为`recipes_table`；"**)；

简单地说，我们创建一个与原始表具有相同结构的虚拟表，使用 select 语句从原始表中插入数据，删除原始表，最后修改新表名以替换原始表

如果你让 FTS 把一个简单的查询变成一个非常慢的查询，这是 FTS 在 SQLite 上不好的地方。您只能使用以下匹配关键字来使用这种类型的查询:

**SELECT * FROM recipes_table，其中 recipe_content 与“cake%”匹配**

或者

**SELECT * FROM recipes_table 其中 docid=recipe_id**

这将解释为什么我们将 old _id 列放入 docid

最后，一定要在一个线程上执行迁移代码，而不是在主线程上，因为这可能会花费很多时间并导致 ANR。我更喜欢使用专用的闪屏，就像本文中[的第二种方法一样。](https://android.jlelse.eu/the-complete-android-splash-screen-guide-c7db82bce565)

# **奖金**

如果您使用 ORMLite，您可以使用下面的代码片段来匹配查询

SelectArg selectArg = **new **SelectArg();

selectarg . set meta info(**“recipe _ content”**)；

**查询**= s+**“*”**；

selectArg.setValue( **查询**)；

query builder favoritePostLongQueryBuilder = database helper。 *getHelper* (getActivity())。*getPostFragment*(get activity())。queryBuilder()。select columns(**【docid】****【id】****【添加日期】****【配方内容】**)；

favoritepostlongquerybuilder . where()。raw( **“配方 _ 内容匹配？”**，选择 arg)；

favoritepostlongquerybuilder . order by(**“添加日期”**、 **false** )。准备()

# **结论**

Android 有很好的工具来帮助开发者创建非常棒和复杂的解决方案。

FTS 是一个非常好的离线内容搜索解决方案。

将数据从旧模式迁移到新模式非常复杂，您需要测试并确保一切正常。

最后，您可以在搜索和自动完成功能中使用 FTS。

# **感谢您的时间，我喜欢听到更多您的来信，如果您觉得这篇文章对您有好处，请告诉我，它将支持我继续写作。**

# **关于我**

我是 Alaa Alsalehi，专业后端和移动开发人员。我有一家食品食谱内容初创公司，拥有超过 25 万活跃用户。