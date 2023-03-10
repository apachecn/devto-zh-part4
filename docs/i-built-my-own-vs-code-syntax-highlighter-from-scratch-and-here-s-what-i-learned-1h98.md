# 我从头开始构建了自己的 VS 代码语法高亮器，下面是我学到的东西。

> 原文：<https://dev.to/alexantra/i-built-my-own-vs-code-syntax-highlighter-from-scratch-and-here-s-what-i-learned-1h98>

### 上下文/目的

因此...！
我是 AWS Redshift SQL 环境中的一名数据分析师。

就上下文而言，该领域的分析师倾向于使用的环境按受欢迎程度排序为:
1 TSQL/SQL Server
2 Oracle
3 PostgreSQL
4 电子表格/ Google Sheet
5 纸片
6 水晶报表
7 Lotus Notes
。
。
。
40 使用算盘
41 洞穴图纸
42 红移 AWS

所以可以说在互联网上很难找到红移的特定技术。

当我在 VS 代码扩展库中搜索红移时，什么也没有！！！！

嗯，这是一个小谎言，有一个，但这也是一个谎言，它只是被标记为红移，因为搜索引擎优化....

这对我来说并不是一个太大的问题。

红移与 PostgreSQL 和 Oracle 非常相似，所以我一直在使用基于 Oracle 的扩展。

然而，随着时间的推移，我一直使用的 Oracle 扩展变得越来越大，越来越麻烦。

这个扩展也有 60 种不同的功能，我只用了 2 种。

所以看起来我所需要的只是 SQL 语法高亮和一个代码片段库，我想我可以试着做一个我自己的语法高亮扩展，这样我就可以摆脱臃肿。

(我将只使用内部全局代码片段文件)

以下是我一路走来学到的经验。

### 第一课:没有人造出我可以偷的东西。

所以我完全认为我可以在这里取得成功。

我 100%地认为我可以使用 Oracle 扩展，去掉我需要的部分，并在晚餐前完成。

我大错特错了。

有两种方法可以突出显示语法。

方法一要求您使用正则表达式声明 JSON 包中突出显示的内容。这就是 VSCode 文档告诉你的。

方法二只需要你下载一个 TextMate 语言包，其他什么都不需要。就这样，你完了。没有开发工作什么的。

我看到的每一个扩展都使用了 TextMate 语言包方法。我能找到红移的语言包吗？没有。我能改变现有的吗？我想不出来。

所以这是第一个心理崩溃，git-hub 上的每个人都采取了简单的方法，因为他们支持的语言足够主流，有一个容易掌握的语言包。

我觉得我在为我的摩托罗拉手机找一个外壳，而我能找到的只有三星和苹果的外壳。

所以我不得不采取第一种选择，自己开发一些东西。

### 第二课:没有我想象的那么自由。

所以语法高亮不仅仅是高亮所有非自由文本。你可以这样做，但是我也可以把我所有的变量都叫做 VAR，它完成了任务，但是效率很低。

良好的语法突出显示使函数与变量具有不同的颜色。不同于字符串的注释。

拥有不同的高亮区域可以让你的眼睛捕捉到你正在寻找的代码。

我知道对于 SQL，我想突出显示以下
命令，如“select”和“where”
数据类型，如“varchar”
单引号和双引号字符串
单引号和块注释
函数，如“row_number”和“upper”
特殊单词，如 distkey
特殊字符，如“<>”
数字
活动单词，如“and”、“or”、“else”

当我写下 AWS 规范中我想突出显示的每一个对象时，这是一个非常非常长的列表，大约有 300 个东西。

现在，我只是假设我会按照自己的意愿对这些进行分组，然后应用规则。

我又错了...

因为 Visual Studio 代码可以以多种方式进行主题化，所以已经有预设的组供您匹配。这使得 themers 的工作很容易，因为他们只是说某个组将是什么颜色，然后不管你使用的是 87547868545874 编码语言中的哪一种，只要它与预定义的组匹配，它就会随着主题而变化。

这意味着我必须将我计划好的组放入我可用的组中。

这并不像我想象的那样是一场灾难，我最终使用了十个组，这接近于我最初想做的，只是不是我所期望的。

### 第三课:没有我想象的那么难。

我不是一个开发人员，我了解基本的 HTML / CSS 和 SQL，我已经把这些技能磨练到地板上。我就像那些会做他妈的牛逼信箱的木头人，但别让我做椅子或栅栏。

我不理解 Javascript、typescript 之类的东西，或者这些东西是如何工作的。开始制作 VS 代码扩展时，我观看了 YouTube 视频，下载了 gits，然后每次都哭了整整 5 分钟。你知道满眼泪水读 ReadMe.md 有多难吗？？？

但我只是一路跌跌撞撞地前进，发现以我的技能水平，这很容易做到。

*   例如，你可以使用 NPM 的软件包 Yeoman T1 来构建一个 VSCode 扩展的工作外壳，它可以立即运行和调试。
*   然后，我将我想要突出显示的内容与此处列出的[组进行匹配](https://macromates.com/manual/en/language_grammars#naming_conventions)
*   然后使用提供的 [JSON 模板](https://code.visualstudio.com/api/language-extensions/syntax-highlight-guide)
*   除了这些[正则表达式指南](https://macromates.com/manual/en/regular_expressions)
*   还有这个方便的[正则表达式编辑器](https://rubular.com/)来检查我做得对不对。

下一分钟，我正在编译扩展，它现在运行在我的工作机器上。

#### Git Hub Repo

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [朗索克](https://github.com/ronsoak)/[VSC _ 红移 _ 扩展](https://github.com/ronsoak/vsc_redshift_extension)

### 支持在红移(AWS) SQL 环境中工作的 Visual Studio 代码扩展

<article class="markdown-body entry-content container-lg" itemprop="text">

# 红移的语法高亮显示

简而言之，Visual Studio 代码中没有红移 AWS SQL 的语法突出显示，所以我自己做了一个，它不花哨，永远不会。

## 特征

语法高亮显示:

*   SQL 命令，如选择、中止、开始...
*   SQL 关键字，如 Date_trunc、Distkey、lower...
*   单行注释
*   双线注释
*   单引号字符串
*   双引号字符串
*   SQL 数据类型，如 BIGINT、BOOL、VARCHAR
*   SQL 常量，如 AND、BETWEEN、ILIKE
*   支持数字高亮显示
*   特殊字符如=，<>，=！

## 技术资料

语法突出显示是通过对应 textmate 元素来完成的。

### 关键词

与 keyword.control 匹配的模式:

> 中止、更改、分析、开始、调用、取消、关闭、注释、提交、复制、创建、解除分配、声明、删除、丢弃、结束、执行、解释、获取、授予、插入、锁定、准备、重置、撤销、回滚、选择、设置、显示、开始、截断、卸载、更新、清空

与关键字匹配的模式。其他:

> ABS，ACOS，ADD_MONTHS，APPROXIMATE，PERCENTILE_DISC，ASIN，ATAN，ATAN2，AVG BIT_AND，BIT_OR，BIT-WISE，…

</article>

[View on GitHub](https://github.com/ronsoak/vsc_redshift_extension)

#### 我是谁？

> ![Ronsoak 🏳️‍🌈🇳🇿 profile image](img/060155790f77f9a631013e9ca11dd477.png)朗索克🏳️‍🌈🇳🇿@ Ron soak![twitter logo](img/4c8a2313941dda016bf4d78d103264aa.png)我是谁？🌈。Xero 的高级数据分析师。博主[dev.to/ronsoak](https://t.co/kJy8nsYdeq)-[medium.com/@ronsoak](https://t.co/zKVBza2SVQ)。我有时会画 instagram.com/ronsoak_art/，写一些还没有人看过的科幻小说。我的推文不代表我的公司。💬好好干，好好干！2019 年 06 月 02 日 00:30[![Twitter reply action](img/44d8b042100e231770330321e5b63d65.png)](https://twitter.com/intent/tweet?in_reply_to=1134980546340085762)[![Twitter retweet action](img/93d9c70ccc54851d2e8e881b53c21dae.png)](https://twitter.com/intent/retweet?tweet_id=1134980546340085762)[![Twitter like action](img/2e93f7775eadefab8bcd34a4542cc5a7.png)](https://twitter.com/intent/like?tweet_id=1134980546340085762)