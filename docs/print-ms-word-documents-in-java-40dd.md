# 用 Java 打印 MS-Word 文档

> 原文：<https://dev.to/eiceblue/print-ms-word-documents-in-java-40dd>

java 开发人员经常需要从 java 代码中打印 Word 文档。在本文中，我将介绍如何使用 [Spire 将 Word 文档发送到联网的物理打印机或虚拟打印机，如 Microsoft Print to PDF。Java 的 doc](https://www.e-iceblue.com/Introduce/doc-for-java.html)。

打印涉及到两个重要的类，即 PrinterSettings 和 PrintDocument 类。查看下表，了解它们的定义。

| 班级 | 定义 |
| --- | --- |
| 打印机设置 | 指定如何打印文档的信息，包括打印它的打印机。 |
| 打印文档 | 定义将文档发送到打印机的可重用对象。 |

## 例 1:打印到物理打印机

```
import com.spire.doc.Document;
import com.spire.ms.System.Drawing.Printing.PrintDocument;
import com.spire.ms.System.Drawing.Printing.PrinterSettings;

public class PrintWord {

    public static void main(String[] args) {

        //load a Word document
        Document document = new Document();
        document.loadFromFile("C:\\Users\\Administrator\\Desktop\\DocoumentToPrint.docx");

        //create a PrinterSettings object
        PrinterSettings printerSettings = new PrinterSettings();

        //specify printer name
        printerSettings.setPrinterName("\\\\192.168.1.104\\HP LaserJet P1007");

        //set copies to print
        printerSettings.setCopies((short) 1);

        //set the page range to print
        printerSettings.setFromPage(2);
        printerSettings.setToPage(4);

        //get PrintDocument object
        PrintDocument printDocument = document.getPrintDocument();

        //apply printer settings
        printDocument .setPrinterSettings(printerSettings);

        //execute print
        printDocument .print();
    }
} 
```

## 例 2:打印到虚拟打印机

```
import com.spire.doc.Document;
import com.spire.ms.System.Drawing.Printing.PrintDocument;
import com.spire.ms.System.Drawing.Printing.PrinterSettings;

public class PrintToPdf {

    public static void main(String[] args) {

        //load a Word document
        Document document = new Document();
        document.loadFromFile("C:\\Users\\Administrator\\Desktop\\DocumentToPrint.docx");

        //create a PrinterSettings object
        PrinterSettings printerSettings = new PrinterSettings();

        //specify virtual printer name
        printerSettings.setPrinterName("Microsoft Print to PDF");

        //print to file
        printerSettings.setPrintToFile(true);

        //specify path and name of the printed file
        printerSettings.setPrintFileName("output/PrintToPDF.pdf");

        //get PrintDocument object
        PrintDocument printDocument = document.getPrintDocument();

        //apply printer settings
        printDocument.setPrinterSettings(printerSettings);

        //execute print
        printDocument.print();
    }
} 
```