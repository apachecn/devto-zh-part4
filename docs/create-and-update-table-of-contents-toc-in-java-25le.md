# 用 Java 语言在 Word 中创建和更新目录

> 原文：<https://dev.to/eiceblue/create-and-update-table-of-contents-toc-in-java-25le>

目录(TOC)为您的文档提供了一个快速参考点，并向读者简要概述了在哪里可以找到哪些内容。

在本文中，我们将向您展示如何使用 [Free Spire 在 Java 应用程序中创建和更新 Word 文档中的目录(TOC)。Doc for Java](https://www.e-iceblue.com/Introduce/free-doc-for-java.html) 库。

下面的示例说明如何创建具有默认外观的目录，该目录包括用内置样式“标题 1”、“标题 2”、“标题 3”以及用制表符前导右对齐的页码格式化的所有文本。

```
import com.spire.doc.*;
import com.spire.doc.documents.*;
import com.spire.doc.fields.*;

import java.awt.*;

public class TableofContents {
    public static void main(String[] args){
        //instantiate a Document object
        Document doc = new Document();
        //add a section
        Section section = doc.addSection();

        //add a paragraph
        Paragraph para = section.addParagraph();
        TextRange tr = para.appendText("Table of Contents");
        //set font size and text color
        tr.getCharacterFormat().setFontSize(11);
        tr.getCharacterFormat().setTextColor(Color.blue);       
        //set the space after the paragraph
        para.getFormat().setAfterSpacing(10);

        //add a paragraph
        para = section.addParagraph();
        //add a table of contents with default appearance by specifying lower heading level and upper heading level. The heading level range must be from 1 to 9.
        para.appendTOC(1, 3);

        //add a new section
        section = doc.addSection();
        //add a paragraph
        para = section.addParagraph();
        para.appendText("Heading 1");
        //apply Heading 1 style to the paragraph
        para.applyStyle(BuiltinStyle.Heading_1);
        section.addParagraph();

        //add a paragraph
        para = section.addParagraph();
        para.appendText("Heading 2");
        //apply Heading 2 style to the paragraph
        para.applyStyle(BuiltinStyle.Heading_2);
        section.addParagraph();

        //add a paragraph
        para = section.addParagraph();
        para.appendText("Heading 3");
        //apply Heading 3 style to the paragraph
        para.applyStyle(BuiltinStyle.Heading_3);
        section.addParagraph();

        //update Table of Contents
        doc.updateTableOfContents();

        //save the resultant document
        doc.saveToFile("createTableOfContents.docx", FileFormat.Docx);
    }
} 
```

[![Create Table of Contents](img/ccecc98d24c3db82f719a81652ce5d73.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--AkGYzkp8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fhxycap8biiem988t8bu.png)

我们还可以创建一个自定义的目录，并通过使用 TOC 开关来确定在目录中显示哪些条目。

| 序列号 | 开关 | 描述 |
| --- | --- | --- |
| one | \o | 从包含大纲级别的样式(最常见的是标题样式)格式化的段落构建目录。 |
| Two | \t | 从使用除内置样式以外的样式格式化的段落构建目录。 |
| three | \u | 从其格式包括在段落设置中直接应用的大纲级别的段落构建目录。 |
| four | \c | 列出用 SEQ(序列)域编号的数字、表格、图表或其他项目。 |
| five | \a | 列出使用标题命令(“引用”>“插入标题”)添加标题的项目，但省略标题标签和编号。 |
| six | \f | 从 TC 字段构建一个表。 |
| seven | \l | 从 TC 字段构建目录，这些字段将条目分配到指定的级别之一。 |
| eight | \b | 仅从由指定书签标记的文档部分收集条目。 |
| nine | \s | 在页码前包括一个数字，如章节号。 |
| Ten | \d | 当与\s 开关一起使用时，指定分隔序列号和页码的字符。 |
| Eleven | \p | 指定分隔条目及其页码的字符。 |
| Twelve | \n | 省略目录中的页码。 |
| Thirteen | \w | 保留表格条目中的选项卡条目。 |
| Fourteen | \x | 保留表格条目中的手动换行符。 |
| Fifteen | \z | 在 Web 版式视图中隐藏制表符前导符和页码。 |
| Sixteen | \h | 将目录条目作为超链接插入。 |

有关 TOC 开关的更多信息，请点击此处查看[。](https://support.office.com/en-us/article/field-codes-toc-table-of-contents-field-1f538bc4-60e6-4854-9f64-67754d78d05c?ocmsassetID=HP010255529&CTT=5&origin=HA102110133&CorrelationId=3d50c19b-ff43-4773-a0cd-374096aaa38b&ui=en-US&rs=en-US&ad=US)

下面的示例显示如何创建一个自定义目录，该目录包括所有用内置样式标题 1、标题 2 和标题 3 格式化的文本，但省略标题级别 1-3 的页码。

```
import com.spire.doc.*;
import com.spire.doc.documents.*;
import com.spire.doc.fields.*;

import java.awt.*;

public class TableofContents {
    public static void main(String[] args){
        //instantiate a Document object
        Document doc = new Document();
        //add a section
        Section section = doc.addSection();

        //add a paragraph
        Paragraph para = section.addParagraph();
        TextRange tr = para.appendText("Table of Contents");
        //set font size and text color
        tr.getCharacterFormat().setFontSize(11);
        tr.getCharacterFormat().setTextColor(Color.blue);
        //set the space after the paragraph
        para.getFormat().setAfterSpacing(10);

        //create a custom table of contents that omits page numbers from heading levels 1-3.
        TableOfContent toc = new TableOfContent(doc, "{\\o \"1-3\" \\n 1-3}");
        para = section.addParagraph();
        para.getItems().add(toc);
        para.appendFieldMark(FieldMarkType.Field_Separator);
        para.appendText("TOC");
        para.appendFieldMark(FieldMarkType.Field_End);
        doc.setTOC(toc);

        //add a new section
        section = doc.addSection();
        //add a paragraph
        para = section.addParagraph();
        para.appendText("Heading 1");
        //apply Heading 1 style to the paragraph
        para.applyStyle(BuiltinStyle.Heading_1);
        section.addParagraph();

        //add a paragraph
        para = section.addParagraph();
        para.appendText("Heading 2");
        //apply Heading 2 style to the paragraph
        para.applyStyle(BuiltinStyle.Heading_2);
        section.addParagraph();

        //add a paragraph
        para = section.addParagraph();
        para.appendText("Heading 3");
        //apply Heading 3 style to the paragraph
        para.applyStyle(BuiltinStyle.Heading_3);
        section.addParagraph();

        //update Table of Contents
        doc.updateTableOfContents();

        //save the resultant document
        doc.saveToFile("customTableOfContents.docx", FileFormat.Docx);

    }
} 
```

[![Custom Table of Contents](img/2876fcd97261d46c02314d5087193ebd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kYiET90t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zm9ktur08b5qzh4pepzs.png)