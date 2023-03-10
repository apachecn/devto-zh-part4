# 使用 GroupDocs 签署并发送任何文档。签名(API)✍️

> 原文：<https://dev.to/grazhevskaja/sign-and-send-any-document-with-groupdocs-signature-5h68>

我想和大家分享一个**在线免费 app，可以签署和发送任何流行类型的文档**-**[group docs。签名](https://products.groupdocs.app/signature)** 。
与其他在线签约应用相比，我喜欢的是:

*   它是免费的。🙌
*   不报名。👏
*   简单的三步工作流程:**删除-签名-发送**。👍

它是唯一一个可以通过一个界面签署任何类型文件的在线应用程序:Word、Excel、PowerPoint、PDF 或其他。条形码和二维码签名-是另一个很酷的功能！😎

以下是我在发票上盖章的方式:

[![](img/78e1fdde2150292bd4fbe39ae28d7dc0.png)](https://i.giphy.com/media/J1p0AIuUkSuTICkWWc/source.gif)

这是签字的发票:

[![](img/844e2a6316ffc9146c9c189a2dc72dc0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--m0yIhmjt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/ysooz1H.png)

## 我想在我的系统中有签名功能！👨‍💻

**GroupDocs。签名 app** 基于 **[GroupDocs 打造。签名 API](https://products.groupdocs.com/signature)T5。您可以使用 GroupDocs。如果您想在您的站点或系统中添加签名功能，请使用签名 API。支持该 API。NET 和 Java 任你选择。**

让我给你展示一个如何用 GroupDocs 添加二维码签名的简单例子。签名 API 在。NET:

```
using (var signature = new Signature("document.pdf"))
{
    // Setup key and passphrase
    string key = "1234567890";
    string salt = "1234567890";

    // Create data encryption object
    IDataEncryption encryption = new SymmetricEncryption(SymmetricAlgorithmType.Rijndael, key, salt);

    // Create custom signature object
    var documentSignatureData = new DocumentSignatureData()
    {
        ID = Guid.NewGuid().ToString(),
        Author = Environment.UserName,
        Signed = DateTime.Now,
        DataFactor = 11.22M
    };

    // Setup QRCode signature options
    var options = new QrCodeSignOptions()
    {
        Data = documentSignatureData,
        EncodeType = QrCodeTypes.QR,
        DataEncryption = encryption,
        // Set signature size
        Height = 100,
        Width = 100
    };

    // Sign and save document
    signature.Sign("document_signed.pdf", options);
} 
```

**GroupDocs。Signature API** 支持比你在上面的应用中看到的更多的特性。

您还可以:

*   从任何来源加载文档:亚马逊 S3 存储、Azure Blob 存储、FTP、URL、流等。
*   放多个签名。
*   搜索签名。
*   验证签名。
*   用密码保护签名文件。
*   在一次运行中保存签署的文档到各种类型。
*   用元数据签名(作者、日期)。
*   用加密数据签名。
*   设置签名属性:颜色、大小、调整等。

**GroupDocs。Signature API** 有一个 **30 天试用期**供你试用。

* * *

*[GroupDocs](https://groupdocs.com) 是 [Aspose](https://aspose.com) 的女儿，后者是世界知名的文件格式 API 和应用程序供应商。*