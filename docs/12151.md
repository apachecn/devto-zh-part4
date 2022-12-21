# 以编程方式将 Word 转换为 PDF[c#]

> 原文：<https://dev.to/syncfusion/converting-word-to-pdf-programmatically-c-4no8>

让我们学习如何在不使用 Microsoft Office 或 interop 的情况下，用 C#以编程方式将 Word 文档转换为 PDF。

[sync fusion Word library](https://www.syncfusion.com/word-framework/net/word-library)(Essential DocIO)使用 [Syncfusion PDF library](https://www.syncfusion.com/pdf-framework/net/pdf-library) 逐页呈现 Word 文档的内容，并将每页的内容添加到 PDF 文档中。该转换器可以轻松集成到中。NET 框架(WinForms、ASP.NET Web 窗体和 ASP.NET MVC)、ASP.NET 核心和 Xamarin 应用程序。它在完全信任和部分信任环境下都能很好地工作，比如 Azure 应用程序。

这个转换器提供了几个选项来定制 Word 到 PDF 的转换，

*   嵌入 TrueType 字体的完整或子集字体信息。
*   替换设备中的备用字体或上传原始字体。
*   PDF 一致性级别 **PDF/A1B** 或 **PDF/X-1a:2001** 。
*   将**可访问的 Word 文档**(符合 508)转换为**可访问的**或**标记的 PDF**
*   将 Word 文档中的标题或书签导出为 PDF 书签。
*   在 Word 到 PDF 的转换过程中，将 Word 表单域导出为 PDF 表单域。
*   调整 JPEG 图像质量和分辨率，以优化转换后的 PDF 文件大小。

该转换器支持典型 Word 文档的所有元素，如文本、格式、图像、表格、超链接、字段、书签、目录、形状、页眉、页脚等。参考我们的文档以了解更多关于 [Word 到 PDF 转换](https://help.syncfusion.com/file-formats/docio/word-to-pdf)的信息。

## 在没有 Microsoft Office 或 interop 的情况下，用 C#以编程方式执行 Word 到 PDF 转换的步骤:

1.创建新的 C#控制台应用程序(。NET Framework)项目。
[![create-a-console-application-in-visual-studio](img/7039319b0f834c2bad1790945ef6134b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mYbOfk-X--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2014/05/create-a-new-csharp-console-application-project-1024x703.png) 
2。安装 [DocToPDFConverter。WinForms NuGet package](https://www.nuget.org/packages/Syncfusion.DocToPDFConverter.WinForms/) 作为对来自[NuGet.org](https://www.nuget.org/)的应用程序的引用。此软件包包含 Syncfusion 库，可在没有 Microsoft Office 或 interop 的情况下执行 Word 到 PDF 的转换。 [![Add Syncfusion Word to PDF converter NuGet package reference to the project](img/dab815c51b8d375a7031f39eea7fbe31.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zvPZPPiC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2014/05/install-syncfusion-doctopdfconverter-nuget-package-for-winforms-1024x526.png) 
3。在 Program.cs 文件中包含以下命名空间。下面的代码片段用于用 DocIO 打开 Word 文档。

```
using Syncfusion.DocIO;
using Syncfusion.DocIO.DLS;
using Syncfusion.DocToPDFConverter;
using Syncfusion.Pdf; 
```

4.使用下面的代码片段创建包含简单文本的 Word 文件。

```
//Get the path of existing Word document
string fullpath = @"...\..\DocToPDF.docx";

//Loads an existing Word document
WordDocument wordDocument = new WordDocument(fullpath, FormatType.Docx);

//Creates an instance of the DocToPDFConverter
DocToPDFConverter converter = new DocToPDFConverter();

//Converts Word document into PDF document
PdfDocument pdfDocument = converter.ConvertToPDF(wordDocument);

//Releases all resources used by DocToPDFConverter
converter.Dispose();

//Closes the instance of document objects
wordDocument.Close();

//Saves the PDF file 
pdfDocument.Save("DocToPDF.pdf");

//Closes the instance of document objects
pdfDocument.Close(true); 
```

下面的屏幕截图展示了输入的 Word 文档。
[![Input Word document to perform Word to PDF conversion](img/dbbd34639da84ee4ba9315bb3d65287f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_tNkq8Wz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2014/05/word-to-pdf-conversion-input-word-document.png) 
下一张截图显示的是 Word 文档转换成的最终 PDF 文档。
[![](img/19c713edaa575d755f1e3b29c003acc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--puHuYuMX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.syncfusion.com/wp-content/uploads/2018/12/the-converted-pdf-document-after-word-to-pdf-conversion.png) 
一个完整的 Word 到 PDF 转换的工作示例可以从[这个 GitHub 位置](https://github.com/SyncfusionExamples/convert-word-to-pdf)下载

[Word 到 PDF 转换示例](https://ej2.syncfusion.com/aspnetmvc/DocIO/DOCtoPDF#/material)的在线示例链接。

如果您想在 Xamarin 或 ASP.NET 核心应用程序或 Azure 环境中将 Word 文档转换为 PDF，请查看以下页面。

*   [在 Xamarin 和。网络核心](https://blog.syncfusion.com/post/word-to-pdf-conversion-in-xamarin-and-net-core.aspx)
*   [Azure 中的 Word 到 PDF 转换](https://www.syncfusion.com/kb/7760/is-it-possible-to-perform-word-to-pdf-conversion-in-azure-environment)

## 结论

无论您是否需要在任何。NET 平台，Syncfusion Word 到 PDF 转换器库使工作更容易。NET 开发人员。花点时间细读一下 [Word 到 PDF 的转换文档](https://help.syncfusion.com/file-formats/docio/word-to-pdf)，在那里你可以找到其他选项和特性，都有代码示例。如果您不熟悉我们的 DocIO 库，强烈建议您遵循我们的[入门文档](https://help.syncfusion.com/file-formats/docio/getting-started)。

深入了解 Syncfusion [Word 框架](https://www.syncfusion.com/word-framework/net)和 [PDF 框架](https://www.syncfusion.com/pdf-framework/net)的丰富功能。

一如既往，我们非常感谢您的反馈，所以请在我们的评论中联系我们。您也可以通过我们的[支持论坛](https://www.syncfusion.com/forums)或 [Direct-Trac](https://www.syncfusion.com/support/directtrac/incidents) 联系我们。我们很乐意为您提供帮助！

如果您已经是 Syncfusion 用户，您可以从[下载](https://www.syncfusion.com/downloads)页面下载产品设置。如果您还不是 Syncfusion 用户，您可以在我们的网站上下载 30 天的免费试用版。

如果你喜欢这篇文章，我们认为你也会喜欢:

【博客】[c#中压缩 PDF 文件的 7 种方法，VB.NET](https://blog.syncfusion.com/post/7-ways-to-compress-pdf-files.aspx)
【电子书】 [SQL Server for C#开发者简洁明了](https://www.syncfusion.com/ebooks/sql_server_for_c_sharp_developers_succinctly)
【博客】[PDF 中光学字符识别使用 Tesseract 开源引擎](https://blog.syncfusion.com/post/optical-character-recognition-in-pdf-using-tesseract-open-source-engine.aspx)
【电子书】[c#中面向对象编程简洁明了](https://www.syncfusion.com/ebooks/oop-csharp)
【博客】 [Word 到 Xamarin 中 PDF 的转换以及。网络核心](https://blog.syncfusion.com/post/word-to-pdf-conversion-in-xamarin-and-net-core.aspx)

以编程方式将 Word 转换成 PDF 的帖子[【c#】](https://blog.syncfusion.com/post/converting-word-docs-to-pdfs.aspx)最先出现在[的 Syncfusion 博客](https://blog.syncfusion.com)上。