# 用 Java 在 Word 中添加、读取和删除文档属性

> 原文：<https://dev.to/eiceblue/add-read-and-remove-document-properties-in-word-in-java-2pod>

每个 Word 文档都可以包含内置属性和自定义属性，如作者、类别、标题、主题、关键字(标签)、注释等。在本文中，我们将了解如何为 Word 文档创建这些属性，并学习如何使用 [Free Spire 在 Java 中以编程方式从 Word 文档中读取和删除这些属性。Java 库的文档](https://www.e-iceblue.com/Introduce/free-doc-for-java.html)。

# 创建文档属性

1.创建内置文档属性

```
import com.spire.doc.Document;
import com.spire.doc.FileFormat;

public class SetDocumentProperties {
    public static void main(String[] args){
        //Load Word document
        Document document = new Document("Input.docx");

        //Create built-in properties
        document.getBuiltinDocumentProperties().setTitle("Creating document properties");
        document.getBuiltinDocumentProperties().setSubject("Word document properties");
        document.getBuiltinDocumentProperties().setAuthor("James");
        document.getBuiltinDocumentProperties().setCompany("Codex");
        document.getBuiltinDocumentProperties().setManager("Chris");
        document.getBuiltinDocumentProperties().setCategory("Word manipulation");
        document.getBuiltinDocumentProperties().setKeywords("Document properties, Word");
        document.getBuiltinDocumentProperties().setComments("For internal use only");

        //Save the resultant document
        document.saveToFile("SetBuiltInProperties.docx", FileFormat.Docx_2013);
    }
} 
```

[![](img/7b36ed63e2cd749e9294f8fb9945050f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vLWE-B-U--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/77xfnyw43sqr2v475a8i.png)

2.创建自定义文档属性

```
import com.spire.doc.Document;
import com.spire.doc.FileFormat;

public class SetDocumentProperties {
    public static void main(String[] args){
        //Load Word document
        Document document = new Document("Input.docx");

        //Create custom properties
        document.getCustomDocumentProperties().add("TrackingID", "AB01");
        document.getCustomDocumentProperties().add("Checked by", "Wilson");

        //Save the resultant document
        document.saveToFile("SetCustomProperties.docx", FileFormat.Docx_2013);

    }
} 
```

[![](img/e757b74cc23ec8939e5bd484fbe69a4f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qlvj7Ovh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/usvyi03lj6ck850rte39.png)

# 读取文档属性

1.读取内置文档属性

```
import com.spire.doc.Document;
import com.spire.doc.FileFormat;

public class SetDocumentProperties {
    public static void main(String[] args){
        //Load Word document
        Document document = new Document("SetBuiltInProperties.docx");

        //Read built-in properties
        System.out.println("Title: " + document.getBuiltinDocumentProperties().getTitle());
        System.out.println("Subject: " + document.getBuiltinDocumentProperties().getSubject());
        System.out.println("author: " + document.getBuiltinDocumentProperties().getAuthor());
        System.out.println("company: " + document.getBuiltinDocumentProperties().getCompany());
        System.out.println("manager: " + document.getBuiltinDocumentProperties().getManager());
        System.out.println("category: " + document.getBuiltinDocumentProperties().getCategory());
        System.out.println("keyWords: " + document.getBuiltinDocumentProperties().getKeywords());
        System.out.println("comments: " + document.getBuiltinDocumentProperties().getComments());
    }
} 
```

[![](img/5a58559dfed100b7268af254e580ba5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---Uh2cUKh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3ck0i7xoiaskz7kdvquj.png)

2.读取自定义文档属性

```
import com.spire.doc.Document;
import com.spire.doc.DocumentProperty;

public class SetDocumentProperties {
    public static void main(String[] args){
        //Load Word document
        Document document = new Document("SetCustomProperties.docx");

        //Get the first custom property 
        DocumentProperty property = document.getCustomDocumentProperties().get(0);
        //DocumentProperty property = document.getCustomDocumentProperties().get("TrackingID");

        //Read name and value of the first custom property
        System.out.println("Name: " + property.getName());
        System.out.println("Value: " + property.getValue());
    }
} 
```

[![](img/4c1c8d25821792b59175ddd9fcc5a4d1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VwRZlKj7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0zmdccbd56vdau1gzksp.png)

# 删除文档属性

1.删除内置属性

```
import com.spire.doc.Document;
import com.spire.doc.FileFormat;

public class SetDocumentProperties {
    public static void main(String[] args){
        //Load Word document
        Document document = new Document("SetBuiltInProperties.docx");

        //Remove built-in properties by setting the value to null
        document.getBuiltinDocumentProperties().setTitle("");
        document.getBuiltinDocumentProperties().setSubject("");
        document.getBuiltinDocumentProperties().setAuthor("");
        document.getBuiltinDocumentProperties().setCompany("");
        document.getBuiltinDocumentProperties().setManager("");
        document.getBuiltinDocumentProperties().setCategory("");
        document.getBuiltinDocumentProperties().setKeywords("");
        document.getBuiltinDocumentProperties().setComments("");

        //Save the resultant document
        document.saveToFile("RemoveBuiltInProperties.docx", FileFormat.Docx_2013);
    }
} 
```

2.删除自定义属性

```
import com.spire.doc.Document;
import com.spire.doc.FileFormat;

public class SetDocumentProperties {
    public static void main(String[] args){
        //Load Word document
        Document document = new Document("SetCustomProperties.docx");

        //Remove specific custom property by name
        document.getCustomDocumentProperties().remove("TrackingID");
        document.getCustomDocumentProperties().remove("Checked by");

        //Save the resultant document
        document.saveToFile("RemoveCustomProperties.docx", FileFormat.Docx_2013);
    }
} 
```