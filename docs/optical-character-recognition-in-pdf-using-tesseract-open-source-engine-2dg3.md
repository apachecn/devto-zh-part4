# 使用 Tesseract 开源引擎实现 PDF 中的光学字符识别

> 原文：<https://dev.to/syncfusion/optical-character-recognition-in-pdf-using-tesseract-open-source-engine-2dg3>

光学字符识别(OCR)是一种用于将 PDF 文件或图像形式的扫描纸质文档转换为可搜索、可编辑数据的技术。纸质文档—如手册、发票、合同等。—通过电子邮件发送。这一过程通常涉及到将文档转换成许多不同颜色的扫描仪，称为光栅图像。为了提取数据和改变文档内容的用途，OCR 引擎是必要的。OCR 引擎检测图像中存在的字符，将这些字符放入单词中，然后放入句子中，使您能够搜索和编辑文档的内容。

## 宇宙魔方引擎

**[Tesseract](https://github.com/tesseract-ocr/)** 是一个光学字符识别引擎，是目前最准确的 OCR 引擎之一。它获得了 [Apache 2.0](http://www.apache.org/licenses/LICENSE-2.0) 的许可，自 2006 年以来一直由谷歌开发。

## 基本 PDF 和 Tesseract 引擎入门

[Syncfusion Essential PDF](https://www.syncfusion.com/pdf-framework/net) 通过使用 Tesseract 开源引擎支持 OCR。通过几行代码，包含光栅图像的扫描纸质文档被转换成可搜索和可选择的文档。

你可以在这里下载 OCR 处理器产品设置[。](https://www.syncfusion.com/downloads/latest-version)

### 部署要求

部署基本 PDF 和 OCR 过程需要下列程序集。

 **   Syncfusion.Compression.Base.dll
*   Syncfusion.Pdf.Base.dll
*   Syncfusion.OcrProcessor.Base.dll

 **   SyncfusionTessaract.dll(宇宙魔方引擎版本 3.02)
*   liblept168.dll([lepto nica](http://www.leptonica.com/)tessera CT 引擎使用的图像处理库)

### 在. NET 项目中引用 OCR 程序集

在. NET 项目中引用 OCR 程序集:

1.  打开您创建的应用程序的**解决方案浏览器**。右击**引用**文件夹，然后点击**添加引用**。
2.  将下列程序集作为引用添加到应用程序中:
    *   Syncfusion.Compression.Base.dll
    *   Syncfusion.Pdf.Base.dll
    *   Syncfusion.OcrProcessor.Base.dll
3.  **SyncfusionTessaract.dll**和**liblept168.dll**不应作为参考加入。它们应该保存在本地机器中，并且程序集的位置应该作为参数传递给 OCR 处理器。

### 对扫描的纸质文档执行 OCR

1.要执行光学字符识别，首先要通过生成 **OCRProcessor** 类的对象来创建 **OCR** 处理器。对于 OCRProcessor 类的**构造器来说，必须接受**宇宙魔方**二进制、**SyncfusionTessaract.dll**和**liblept168.dll**的路径。**

```
//Initializes the OCR processor by providing Tesseract binaries(SyncfusionTesseract.dll and liblept168.dll)
//to the OCR processor overload.
OCRProcessor processor = new OCRProcessor(@"TesseractBinaries"); 
```

2.使用 **PdfLoadedDocument** 类加载需要进行光学字符识别的 PDF 文档。

```
//Loads a PDF document.
PdfLoadedDocument loadedDocument = new PdfLoadedDocument("Input.pdf"); 
```

3.下一步是为 OCR 过程设置语言，并使用语言词典的输入来启动 OCR 过程。宇宙魔方支持多种语言。以下代码解释了英语的 OCR 过程以及如何提供英语词典输入。

```
//Sets OCR language to process.
processor.Settings.Language = "eng";
//Processes OCR by providing PDF document, data dictionary, and language.
processor.PerformOCR(loadedDocument, @"Tessdata"); 
```

***注*** *:* 从以下位置下载 OCR 处理器 zip 文件，可以获得宇宙魔方二进制文件SyncfusionTessaract.dll、liblept168.dll和语言包(Tess data):[https://www.syncfusion.com/downloads/latest-version](https://www.syncfusion.com/downloads/latest-version)

4.最后一步是保存 PDF 文档并处理 **PdfLoadedDocument** 对象。保存的 PDF 文档现在包含可搜索形式的内容。

```
//Saves the OCR-processed PDF document to a disk.
loadedDocument.Save("Sample.pdf");
loadedDocument.Close(true); 
```

### 对文档的一部分执行 OCR

光学字符识别也可以在文档的一部分而不是整个文档上执行。下面的文档 [链接](https://help.syncfusion.com/file-formats/pdf/working-with-ocr#performing-ocr-for-a-region-of-the-document) 提供了代码示例和说明。

## OCR 的多语言支持

从版本 3 开始，Tesseract 引擎支持多种语言，如阿拉伯语、英语、保加利亚语、加泰罗尼亚语、捷克语、中文和德语，如下表所示。

Essential PDF 也支持 OCR 处理器中的所有这些语言。默认情况下，Syncfusion 只在包中提供英语词典。其他语言的字典包可以从以下在线位置下载:

[https://github.com/tesseract-ocr/tessdata](https://github.com/tesseract-ocr/tessdata)

1.  可以下载上面链接中的字典包，解压到一个文件夹中，文件夹的位置可以传递给 **OCRProcessor** 类的 **PerformOCR()** 方法。
2.  还必须在 **OCRProcessor 中更改相应的语言代码。设置.语言**属性。例如，要用德语执行光学字符识别，应将属性设置为**处理器。Settings . Language = " deu**

下表显示了支持的语言及其语言代码的完整集合。

| **语言** | **语言代码** |
| 阿拉伯语 | 天坛星座 |
| 阿塞拜疆人 | 阿泽 |
| 保加利亚的 | bul |
| 加泰罗尼亚语 | 猫 |
| 捷克人 | 这些呢 |
| 简体中文 | 中国模式 |
| 繁体中文 | 谁之间 |
| 切罗基人 | 儿童健康研究（Child Health Research 的缩写） |
| 丹麦的 | 段 |
| 丹麦语(花体) | dan-frak |
| 德语、标准语和花体文字 | 次卵 |
| 希腊的 | 厄尔 |
| 英语 | 电子新闻采集 |
| 古英语。亦称 AＮＧＬＯ-SＡＸＯＮ | enm |
| 世界语 | epo |
| 爱沙尼亚语 | 自我提高训练法 |
| 芬兰人的 | 鳍状物 |
| 法语 | 兄弟 |
| １４００　古法语 | frm |
| 加利西亚的 | glg |
| 希伯来人 | 希伯来书 |
| 印地语 | 赫因 |
| 克罗地亚人 | 心率变异性 |
| 匈牙利的 | 破坏者 |
| 印度尼西亚的 | ind |
| 意大利的 | (英国)独立电视管理局(Independent Television Authority)ˌ(英国)工业运输协会(Industrial Transport Association)ˌ(美国)工业与贸易管理局(Industry and Trade Administration)ˌ国际纺织品协定(International Textile Agreement)ˌ入门教学字母(Initial Teaching Alphabet) |
| 日本人 | 日本 |
| 韩国的 | 侯尔 |
| 拉脱维亚的 | 厕所 |
| 立陶宛的 | 照亮 |
| 荷兰人 | 鼻泪管 |
| 挪威的 | 也不 |
| 抛光剂 | 政客 |
| 葡萄牙语 | 为了什么 |
| 罗马尼亚的 | 留宿（remain overnight 的缩写） |
| 俄语 | 上游阻力 |
| 斯洛伐克人[语] | slk |
| 语 | 白银 |
| 阿尔巴尼亚人 | sqi |
| 西班牙语 | 矿泉 |
| 塞尔维亚的 | srp |
| 瑞典的 | 女工程师学会 |
| 泰米尔人 | 苏格兰式便帽 |
| 泰卢固语 | 电话 |
| 他加禄语 | tgl |
| 泰国的 | 总碳氢分析器 |
| 土耳其的 | 高加索羱羊 |
| 乌克兰的 | ukr |
| 越南人 | 竞争 |

## 提高 OCR 准确性的提示

在将扫描的纸张转换为 TIFF 图像，然后再转换为 PDF 文档时，您可以通过选择正确的压缩方法来提高 OCR 过程的准确性:

*   当使用至少 300 点每英寸(DPI)时，Tesseract 最适合文本，因此调整图像大小是有益的。
*   压缩:
    *   对彩色或灰度图像使用(zip)无损压缩。
    *   对单色图像使用 CCITT Group 4 或 JBIG2(无损)压缩。这可以确保光学字符识别在最高质量的图像上工作，从而提高 OCR 的准确性。这在低分辨率扫描中特别有用。
*   此外，旋转的图像和倾斜的图像也会影响 OCR 过程的准确性和可读性。

有关质量改进的更多详细信息，请参考以下链接:

[https://github . com/tessera CT-ocr/tessera CT/wiki/improve quality。](https://github.com/tesseract-ocr/tesseract/wiki/ImproveQuality.)

样本可以从 [GitHub 库](https://github.com/SyncfusionExamples/optical-character-recognition-in-pdf)中检出。给它一颗星，如果有用的话。

花点时间仔细阅读[文档](https://help.syncfusion.com/file-formats/pdf/working-with-ocr)，在那里你会找到其他选项和特性，以及所有附带的代码示例。

如果您不熟悉我们的 [PDF 库](https://www.syncfusion.com/pdf-framework/net)，强烈建议您遵循我们的[入门指南](https://help.syncfusion.com/file-formats/pdf/getting-started)。

如果您对这些功能有任何疑问或需要澄清，请在下面的评论中告诉我们。您也可以通过我们的[支持论坛](https://www.syncfusion.com/forums)或 [Direct-Trac](https://www.syncfusion.com/support/directtrac/) 联系我们。我们很乐意为您提供帮助！

如果你喜欢这篇博文，我们认为你也会喜欢以下资源:

*   【电子书】 [C#简洁地](https://www.syncfusion.com/ebooks/csharp)
*   【电子书】 [PDF 简洁地](https://www.syncfusion.com/ebooks/pdf)
*   【电子书】 [Web 服务器简洁地](https://www.syncfusion.com/ebooks/webservers)
*   【博文】[用 C#压缩 PDF 文件的 7 种方法，VB.NET](https://blog.syncfusion.com/post/7-ways-to-compress-pdf-files.aspx)

帖子[使用 Tesseract Opent 源引擎在 PDF 中进行光学字符识别](https://blog.syncfusion.com/post/optical-character-recognition-in-pdf-using-tesseract-open-source-engine.aspx)首先出现在 [Syncfusion 博客](https://blog.syncfusion.com)上。**