# ASP.NET 核心的 10 大静态代码质量分析器

> 原文：<https://dev.to/bharatdwarkani/top-10-code-quality-static-analysers-for-asp-net-core-mp8>

**fxcopanalyzer**

微软创造的一套分析器，叫做微软。CodeAnalysis . FxCopAnalyzers，包含静态代码分析中最重要的“FxCop”规则，转换为 Roslyn 分析器。这些分析器检查您的代码的安全性、性能和设计问题，等等。

您可以将这些 FxCop 分析器作为 NuGet 包或作为 Visual Studio 的 VSIX 扩展来安装。

[https://www.nuget.org/packages/Microsoft.code analysis . fxcopanalyzer](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers)

**样品分析仪**

方法实现 StyleCop 规则。NET 编译器平台。它有助于加强代码的标准风格。

[https://www.nuget.org/packages/StyleCop.Analyzers/](https://www.nuget.org/packages/StyleCop.Analyzers/)

**安全码扫描**

检测各种安全漏洞模式:SQL 注入、跨站点脚本(XSS)、跨站点请求伪造(CSRF)、XML 外部实体注入(XXE)等。

[https://security-code-scan.github.io/](https://security-code-scan.github.io/)

**Web API 分析器**

ASP.NET 核心 2.2 和更高版本包括微软。AspNetCore . MVC . API . analyzer nu get 包含 web APIs 分析器的包。

[https://www.nuget.org/packages/Microsoft.AspNetCore . MVC . API . analyzer](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers)

**声纳分析仪**

在你的代码中发现错误和代码味道的分析器。

[https://www.sonarqube.org/](https://www.sonarqube.org/)
T3】https://www.nuget.org/packages/SonarAnalyzer.CSharp

林德哈特。analyser . missingwaitwarning

当使用依赖注入和 async-await 模式时，有可能以带有返回任务的方法的接口结束。如果在同步方法中使用这个接口方法，那么它可能会像一个“发射并忘记”的方法一样错误地运行。在这种情况下，该分析器生成一个警告。

[https://www.nuget.org/packages/Lindhart.analyser . missingwaitwarning](https://www.nuget.org/packages/Lindhart.Analyser.MissingAwaitWarning)

取代。分析器. CSharp

提供诊断分析器来警告在 C#中不正确地使用 NSubstitute。

[https://www.nuget.org/packages/NSubstitute.Analyzers.CSharp](https://www.nuget.org/packages/NSubstitute.Analyzers.CSharp)

**xUnit.net【代码分析器】**

使用 xUnit.net 的项目代码分析器，有助于发现和修复编写测试时的常见问题。

[https://www.nuget.org/packages/xunit.analyzers](https://www.nuget.org/packages/xunit.analyzers)

**微软。代码质量分析器**

代码质量分析器

[https://www.nuget.org/packages/Microsoft.代码质量分析器](https://www.nuget.org/packages/Microsoft.CodeQuality.Analyzers)

**微软。code analysis . version check analyzer**

微软。CodeAnalysis 版本检查分析器
[https://www.nuget.org/packages/Microsoft.code analysis . version check analyzer](https://www.nuget.org/packages/Microsoft.CodeAnalysis.VersionCheckAnalyzer)

更多分析者-[https://github.com/dotnet/roslyn-analyzers](https://github.com/dotnet/roslyn-analyzers)