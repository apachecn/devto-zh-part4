# 像老板一样向左推—第 5.9 部分—错误处理和日志记录

> 原文：<https://dev.to/shehackspurple/pushing-left-like-a-boss-part-5-9-error-handling-and-logging-5apm>

### 这个系列，还有我的博客，都动了！[来看看](https://wehackpurple.com/g8tg)！

所有错误都应该被捕获并优雅地处理；屏幕上不应该出现堆栈跟踪或数据库错误。这不仅是为了让我们看起来像专业人士，也是为了让攻击者在制造攻击时不会得到额外的信息来对付我们。当错误发生时，应用程序应该永远不会失败到未知状态，它应该总是回滚它正在执行的任何事务，并“关闭”它可能已经打开的任何事务。这样的情况也应该总是被记录，以便如果发生事故，事故响应者在他们调查时有事情可做，并且审计员可以验证系统正在并且已经正确地工作。

[![In Hong Kong, at “The Peak”.](img/c942231936353932071c1897a8babadd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XSb0oMvQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/aync7rp6ql1wpvw054ps.jpeg)

### [在我的新博客上阅读其余内容！](https://wehackpurple.com/gglr)！