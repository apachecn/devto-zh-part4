# 使用 ItextPDF 在 Java Spring 中创建基于 HTML 模板的 PDF

> 原文：<https://dev.to/aswzen/creating-a-pdf-based-on-html-template-in-java-spring-using-itextpdf-42e5>

Maven 使用了

```
<dependency>
    <groupId>org.jsoup</groupId>
    <artifactId>jsoup</artifactId>
    <version>1.12.1</version>
</dependency>
<dependency>
    <groupId>org.xhtmlrenderer</groupId>
    <artifactId>flying-saucer-pdf-itext5</artifactId>
    <version>9.0.1</version>
</dependency>
<dependency>
    <groupId>com.itextpdf</groupId>
    <artifactId>font-asian</artifactId>
    <version>7.1.7</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>com.itextpdf</groupId>
    <artifactId>itext-asian</artifactId>
    <version>5.2.0</version>
</dependency> 
```

使用的代码

```
package id.com.hutchison.report.tools.controller;

import com.itextpdf.text.DocumentException;
import com.itextpdf.text.pdf.PdfAction;
import com.itextpdf.text.pdf.PdfReader;
import com.itextpdf.text.pdf.PdfStamper;
import com.itextpdf.text.pdf.PdfWriter;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.OutputStream;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.logging.Level;
import java.util.logging.Logger;
import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.xhtmlrenderer.pdf.ITextRenderer;

/**
 *
 * @author TMPCSigit
 */
@Controller
@RequestMapping("/api/v1/report")
public class ReportController {

    private static final SimpleDateFormat filenameDate = new SimpleDateFormat("ddMMyyyyHHmmss");
    private static final SimpleDateFormat readableDate = new SimpleDateFormat("dd-MM-yyyy HH:mm:ss");

    @RequestMapping(value = "/export", method = RequestMethod.GET, produces = MediaType.APPLICATION_PDF_VALUE)
    public ResponseEntity<byte[]> getExport() {
        ITextRenderer renderer = new ITextRenderer();
        ByteArrayOutputStream boas = null;
        try {
            String inputFile = "files/templates/autodebit.html";
            String outputFile = "files/generated/autodebit_"+filenameDate.format(new Date())+".pdf";

            String html = new String(Files.readAllBytes(Paths.get(inputFile)));
            final Document document = Jsoup.parse(html);
            document.outputSettings().syntax(Document.OutputSettings.Syntax.xml);
            document.body().select(".DOC_GENERATED_DATE").html(readableDate.format(new Date()));

            renderer.setDocumentFromString(document.html());
            renderer.layout();

            try (OutputStream os = Files.newOutputStream(Paths.get(outputFile))) {
                renderer.createPDF(os);
                os.close();

                PdfReader reader = new PdfReader(outputFile);
                boas = new ByteArrayOutputStream();
                PdfStamper stamper = new PdfStamper(reader, boas);
                stamper.setPageAction(PdfWriter.PAGE_OPEN, new PdfAction(PdfAction.PRINTDIALOG), 1); 
                stamper.close();
            } catch (DocumentException ex) {
                Logger.getLogger(ReportController.class.getName()).log(Level.SEVERE, null, ex);
            }
        }   catch (IOException ex) {
            Logger.getLogger(ReportController.class.getName()).log(Level.SEVERE, null, ex);
        }
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_PDF);
        headers.setCacheControl("must-revalidate, post-check=0, pre-check=0");
        ResponseEntity<byte[]> response = new ResponseEntity<>(boas.toByteArray(), headers, HttpStatus.OK);
        return response;
    }
} 
```

HTML 模板

```
<html>
<head>
    
    <style type="text/css">
        body{
            font-family: Arial;
            padding: 0px;
            margin: 0px auto;
        }
        table{
            width: 690px;
            font-family: Arial;
            border-collapse: collapse;
            /*border:1px solid black;*/
            background-color: #fff;
        }
        table td{
            font-size: 11px;
            /*border:1px solid black;*/
            font-family: Arial;
        }
    </style>
</head>
<body>
    <table>
        <tr>
            <td colspan="7" style="height:15px"></td>
            <td colspan="4" rowspan="4" style="padding:10px;text-align:center"><img style="height:60px;" src="../assets/logo.png"></td>
        </tr>
        <tr>
            <td colspan="5"><b>Informasi Auto Debit Isi Ulang</b></td>
            <td></td>
            <td></td>
        </tr>
        <tr>
            <td colspan="5"><i>Auto Debit Recharge Information</i></td>
            <td><i>generated at: <span class="DOC_GENERATED_DATE">{{DATE}}</span></i></td>
            <td></td>
        </tr>
        <tr>
            <td colspan="7" style="height:15px"></td>
        </tr>
        <tr>
            <td><b>Tanggal Pilihan</b></td>
            <td colspan="5" style="background-color:#EEEEEE"></td>
            <td><b>Nama Pelanggan</b></td>
            <td colspan="2" style="background-color:#EEEEEE"></td>
        </tr>
        <tr>
            <td><i>Invoice Data</i></td>
            <td colspan="5" ></td>
            <td><i>Customer's Name</i></td>
            <td colspan="2" ></td>
        </tr>
        <tr>
            <td><b>Nomor Tagihan</b></td>
            <td colspan="5" style="background-color:#EEEEEE"></td>
            <td><b>Alamat</b></td>
            <td colspan="2" style="background-color:#EEEEEE"></td>
        </tr>
        <tr>
            <td><i>Invoice Number</i></td>
            <td colspan="5" ></td>
            <td><i>Address</i></td>
            <td colspan="2" ></td>
        </tr>
        <tr>
            <td><b>Jumlah</b></td>
            <td colspan="5" style="background-color:#EEEEEE"></td>
            <td><b>Nomor HP</b></td>
            <td colspan="2" style="background-color:#EEEEEE"></td>
        </tr>
        <tr>
            <td><i>Amount</i></td>
            <td colspan="5" ></td>
            <td><i>Mobile Number</i></td>
            <td colspan="2" ></td>
        </tr>
        <tr>
            <td colspan="9"><hr></td>
        </tr>
        <tr>
            <td></td>
            <td><b>Tanggal Tagihan</b></td>
            <td><b>Nomor Tagihan</b></td>
            <td><b>Jumlah</b></td>
            <td><b>No. Category</b></td>
            <td><b>Call Plan</b></td>
            <td><b>Tgl Pembayaran</b></td>
            <td><b>Tgl Input</b></td>
            <td><b>Status</b></td>
        </tr>
        <tr>
            <td></td>
            <td><i>Invoice Date</i></td>
            <td><i>Invoice Number</i></td>
            <td><i>Amount</i></td>
            <td><i>Num Category</i></td>
            <td><i>Call Plan</i></td>
            <td><i>Payment Date</i></td>
            <td><i>Upload Date</i></td>
            <td><i>Status</i></td>
        </tr>
        <tr>
            <td>Previous Month</td>
            <td>XXX</td>
            <td>XXX</td>
            <td>XXX</td>
            <td>XXX</td>
            <td>XXX</td>
            <td>XXX</td>
            <td>XXX</td>
            <td>XXX</td>
        </tr>
        <tr>
            <td>This Month</td>
            <td>XXX</td>
            <td>XXX</td>
            <td>XXX</td>
            <td>XXX</td>
            <td>XXX</td>
            <td>XXX</td>
            <td>XXX</td>
            <td>XXX</td>
        </tr>
    </table>
</body>
</html> 
```