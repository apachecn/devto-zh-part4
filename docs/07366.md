# Azure pipelines:如何恢复本地 nuget 包

> 原文：<https://dev.to/raznerd/azure-pipelines-how-to-restore-local-nuget-packages-38o>

### 为什么我要恢复本地 nuget 包？

*请注意，建议打包并[发布所有的](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/package/nuget?view=azure-devops#push-nuget-packages)包，并通过在您的 Nuget.config* 中添加 URL 作为新的 packageSource 来引用它们

这样一来，有时你可能会有本地`.nupkg`包，并希望在 Azure 管道的构建阶段恢复这些包。在我的例子中，我必须为一个客户端快速设置 Azure pipeline，而他们仍然在解决他们的私有 nuget 包存储库，所以我以这样一种方式设置它，以便他们可以在完成后添加新的包存储库 URL。

### 我会遇到什么问题？

出于本文的目的，我将使用`azure-pipeline.yml`文件方法(而不是 GUI 方法)。在您的`azure-pipeline.yml`文件中，您可能会看到这样的内容: