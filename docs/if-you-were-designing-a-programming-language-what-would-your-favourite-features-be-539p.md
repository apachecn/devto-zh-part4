# 如果你正在设计一种编程语言，你最喜欢的特性是什么？

> 原文：<https://dev.to/dansilcox/if-you-were-designing-a-programming-language-what-would-your-favourite-features-be-539p>

多年来我已经学习了几种不同的语言，我有一个不同语言中我最喜欢的特性的列表(*注意*我已经包括了一些不是严格意义上的语言特性，但是我认为非常棒的常用库！)-我会在下面列出我自己的想法，但我认为这也是一个有趣的问题，可以问社区！

以下是我的列表，排名不分先后:

1.  [仿制药](https://en.wikipedia.org/wiki/Generic_programming)
2.  [箭头功能](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
3.  [异步/等待](https://javascript.info/async-await)
4.  [LINQ 查询](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/linq/)
5.  [扩展方法](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/classes-and-structs/extension-methods)
6.  [属性](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/attributes/)
7.  [数据库迁移](https://en.wikipedia.org/wiki/Schema_migration)

你对这些功能有什么想法？如果你要写一种语言，你会包括它们吗？你还会补充什么，为什么？

编辑:在现代 web 开发中，一个真正有用的，有些人会说绝对关键的，是本机强类型 JSON 序列化/非序列化——即能够在对象和 JSON 表示之间轻松转换，而不必手动映射内部字段，特别是在涉及嵌套的地方。C#有一个很棒的实现，在反序列化时利用泛型实现“to”对象类型——等价的[序列化器](https://www.newtonsoft.com/json/help/html/Overload_Newtonsoft_Json_JsonConvert_SerializeObject.htm)也很不错，可以轻松支持自定义转换器，比如枚举的字符串表示等