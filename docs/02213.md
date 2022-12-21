# 随机字符串。即时生成，还是从预填充的表中选择？

> 原文：<https://dev.to/peledzohar/random-strings-generate-on-the-fly-or-select-from-a-pre-populated-table-4mp6>

# 更新:

我写了一个随机字符串生成器的改进版本，并在这里写了博客:

[![peledzohar image](img/e7e81cb2574caa67d27f6d07d8cee115.png)](/peledzohar) [## 一个更加可控的 SQL Server 随机字符串生成器函数

### Zohar Peled 1919 年 12 月 10 日 3 分钟阅读

#sql #database](/peledzohar/a-more-controllable-random-string-generator-function-for-sql-server-2fai)

# 免责声明:

1:本文的代码示例都是 T-SQL。对于其他 SQL 方言，需要在代码中做一些更改，但原则仍然有效。

2:一篇名为如何预填充随机字符串池的文章刚刚发表在我的博客上。

这个帖子是一点点自我推销，一点点知识分享。说完了，让我们开始吧。

# 为什么我想要一个随机字符串池？

随机字符串在许多不同的用例中非常有用——从缩短的 URL 到加密密钥和 salts，我们在任何地方都使用它们。但是，为什么我们要预先填充它们的池，而不是在需要的时候即时生成它们呢？好吧，如果你只是想让你的字符串是随机的，那么尽一切办法，在飞行中产生它们。但如果你希望它们既随机又独特，那么你就需要一个资源库。为什么？因为所谓的[生日问题](https://en.wikipedia.org/wiki/Birthday_problem)。

简单来说，生日问题关注的是一群 x 人中的两个人共享生日的概率。令人惊讶的是，这种可能性比大多数人想象的要高得多。在一个只有 23 人的团队中，两个人一起过生日的几率是 50%，而当团队只有 70 人时，这一几率上升到 99.9%。

# 如何生成随机字符串？

这个问题[有 SQL Server 专家 Martin Smith 的答案](https://stackoverflow.com/a/4558578/3094533)。

我对他的代码做了一点编辑，允许数字出现在随机字符串中，并将它打包到一个用户定义的内联表函数中。为此，我必须首先创建一个视图，因为不能在用户定义的函数中使用`newid`操作符。观点很简单:

```
CREATE VIEW GuidGenerator
AS
    SELECT Newid() As NewGuid 
```

Enter fullscreen mode Exit fullscreen mode

基于马丁代码的函数是这样的:

```
CREATE FUNCTION dbo.RandomStringGenerator
(
    @Length int,
    @Count int -- Note: up to 1,000,000 rows
)
RETURNS TABLE
AS 
RETURN 

WITH E1(N) AS (SELECT N FROM (VALUES (1), (2), (3), (4), (5), (6), (7), (8), (9), (10)) V(N)),   -- 10
     E2(N) AS (SELECT 1 FROM E1 a, E1 b), --100
     E3(N) AS (SELECT 1 FROM E2 a, E2 b), --10,000
     E4(N) AS (SELECT 1 FROM E3 a, E2 b), --1,000,000
     Tally(N) AS (SELECT ROW_NUMBER() OVER (ORDER BY @@SPID) FROM E4)

SELECT TOP(@Count) (
    SELECT  TOP (@Length) CHAR(
            CASE  Abs(Checksum(NewGuid)) % 3
                WHEN 0 THEN 65 + Abs(Checksum(NewGuid)) % 26 -- Random upper case letter
                WHEN 1 THEN 97 + Abs(Checksum(NewGuid)) % 26 -- Random lower case letter
                ELSE 48 + Abs(Checksum(NewGuid)) % 10 -- Random digit
            END
            )
    FROM Tally As t0  
    CROSS JOIN GuidGenerator 
    WHERE  t0.n <> -t1.n -- force SQL Server to re-evalulate the subquery for each row in the main query.
    FOR XML PATH('')
    ) As RandomString
FROM Tally As t1 
```

Enter fullscreen mode Exit fullscreen mode

基本上，这个想法是使用堆叠的公共表表达式创建一个内联计数表。您将这个计数表用作行乘数——在内部查询中查询生成的字符串中的字符数(`@Length`参数)，在外部查询中查询要返回的字符串数(`@Count`参数)。

在内部查询中，使用`Abs(Checksum(NewGuid))`作为随机数，返回值基于字符的 ascii 码——从 65 到 90 的数字是大写字母的 ascii 码，从 97 到 122 是小写字母，从 48 到 57 是数字。您还可以使用相同的技术来确定是返回一个数字、一个大写字母还是一个小写字母。

你使用这个函数来生成随机字符串，就像这样:

```
SELECT RandomString 
FROM dbo.RandomStringGenerator(20, 100); 
```

Enter fullscreen mode Exit fullscreen mode

这将返回 100 个随机字符串的列表，每个字符串 20 个字符。

如果您想保证您的随机字符串也是惟一的，您必须用它们预先填充一个表，并在保存字符串的列上有一个惟一的约束/索引。

你可以在我的博客上的[如何预填充随机字符串池](https://zoharpeled.wordpress.com/2019/09/15/pre-populate-a-random-strings-pool/)中了解更多。这里还有一个 rextester 在线演示的链接。