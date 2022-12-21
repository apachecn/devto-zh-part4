# Drupal 中来自 XML 源的内容迁移

> 原文：<https://dev.to/innoraft12/content-migration-in-drupal-from-xml-source-1jg6>

我们最近的一篇文章谈到了 Drupal 8 中的迁移 API、迁移的基础知识以及从 CSV 源迁移各种字段类型的数据。一些框架和 CMS 如 Wordpress 等允许数据以 XML 格式导出(或者 JSON 格式，如果某些扩展可用的话)。通常情况下，我们会找到另一家代理公司为我们提供需要迁移到 Drupal 的站点 XML 数据。在这篇文章中，我们看到了如何将内容从 XML 源迁移到 Drupal。

注意:这不是先决条件，但是 XPath 选择器的基本知识会很有帮助。

需要满足的主要要求/依赖关系如下:

自定义模块-它包含安装模块时将导入的脚本，并且必须具有以下依赖项:Migrate、Migrate Plus、Migrate Tools。
创建自定义模块的主要目的是，当内容成功迁移且不需要进一步更新时，删除自定义模块将删除导入的迁移脚本，而不会影响站点上的任何其他工作流。
源 XML 文件-该文件可以是外部的(必须可以通过 HTTP 访问)或本地存储的(在私有文件目录中)。

文章来自 inn raft:[阅读更多](https://www.innoraft.com/blogs/content-migration-drupal-xml-source)