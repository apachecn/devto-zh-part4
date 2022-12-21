# 通过 Azure 函数使用 JSON 和用户机密配置

> 原文：<https://dev.to/cesarcodes/using-json-and-user-secrets-configuration-with-azure-functions-3f7g>

在我的[上一篇文章](https://dev.to/cesarcodes/dependency-injection-in-azure-functions-1bg9)中，我谈到了我们如何扩展`FunctionsStartup`类并覆盖它的`Configure`方法来注册服务。在这篇文章中，我将分享如何使用用户秘密和 JSON 文件配置。

如果要使用用户机密，必须先从 NuGet 添加`Microsoft.Extensions.Configuration.UserSecrets`包。这也应该在您的项目文件中添加一个 GUID。此时，您可以通过 Visual Studio 或。NET Cli，就像平常一样。需要注意的一点是，下面的代码示例调用了`AddUserSecrets()`方法。如果您不打算使用用户机密，跳过这一步并删除对该方法的调用(第 16 行)。

为了利用 JSON 文件，您需要继续创建 JSON 文件，下面的代码示例应该可以工作。