# 在多租户 AEM 项目中集成现代前端(第 3 部分)

> 原文：<https://dev.to/anfibiacreativa/integrating-a-modern-frontend-in-a-multi-tenant-aem-project-part-3-5bj9>

是我们完成这个系列的时候了。再来说说 AEM 和 maven！

## maven 插件

让我们记住一点结构。如果你没有读过它们，请阅读本系列的第一部分和第二部分的[，这样一切都更有意义。](https://dev.to/anfibiacreativa/integrating-a-modern-frontend-in-a-multi-tenant-aem-project-2l20)

为了让前端设置直接从 maven 触发任务，您需要在 maven 中安装一个插件。但是在哪里？

正如我们之前讨论的，前端模块既是一个 npm 包，也是一个拥有自己的 pom.xml 的 maven 插件。然而，我们不想在这个模块中安装 [maven-frontend-plugin](https://github.com/eirslett/frontend-maven-plugin) ，因为它没有意义。

我们的意图是，每当我们构建根项目，或者每个租户单独构建时，我们也触发前端构建(或者我们不触发！取决于我们和我们通过的档案！)

这个插件有很好的文档，所以我建议你从阅读[文档](https://github.com/eirslett/frontend-maven-plugin)开始

## 在 reactor pom.xml 中配置插件

我们需要做的第一件事是像这样更新我们的 reactor POM . XML: