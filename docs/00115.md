# 使用 C#验证文档中的数字签名

> 原文：<https://dev.to/usmanaziz/verify-digital-signatures-in-documents-using-c-4g25>

**文档中的数字签名**看起来与纸质签名相似，然而，由于是基于证书的**电子签名**它们以加密的形式包含签名者的身份。这些证书是由可信的授权证书颁发机构颁发的，它们标识了被颁发证书的人。这就是为什么数字签名的文档可以在任何时候被验证。在本文中，我将向您展示如何通过使用 [**GroupDocs 以编程方式验证 **PDF** 、 **Word** 和 **Excel** 、**文档**中的数字签名。签名为。NET**](https://products.groupdocs.com/signature/net) **API** 与 **C#** 。

### 验证数字签名 PDF 文档的步骤

为了演示，我使用一个 PDF 文档进行数字签名验证。但是，同样的代码也适用于 MS Word 和 Excel 文档格式。

**1。**下载 *[GroupDocs。签名为。NET](https://downloads.groupdocs.com/signature/net)T5 或者使用 [NuGet](https://www.nuget.org/packages/GroupDocs.Signature/) 安装。*

**2。**在代码中添加以下名称空间。

```
using GroupDocs.Signature;
using GroupDocs.Signature.Domain;
using GroupDocs.Signature.Options; 
```

Enter fullscreen mode Exit fullscreen mode

**3。**使用 [*签名*](https://apireference.groupdocs.com/net/signature/groupdocs.signature/signature) 类的实例加载数字签名的 PDF 文档。

```
using (Signature signature = new Signature("sample.pdf"))
{
    // Your code goes here.
} 
```

Enter fullscreen mode Exit fullscreen mode

**4。**实例化 *[数字验证选项](https://apireference.groupdocs.com/net/signature/groupdocs.signature.options/digitalverifyoptions)* 对象，并指定验证选项。

```
DigitalVerifyOptions options = new DigitalVerifyOptions("certificate.pfx")
{
    Comments = "Test comment"
}; 
```

Enter fullscreen mode Exit fullscreen mode

**5。**调用 _Signature _class '实例的 *[Verify](https://apireference.groupdocs.com/net/signature/groupdocs.signature/signature/methods/verify)* 方法，并向其传递 _ DigitalVerifyOptions _ 函数。

```
// verify document signatures
VerificationResult result = signature.Verify(options); 
```

Enter fullscreen mode Exit fullscreen mode

**6。**检查来自*的验证结果[验证结果](https://apireference.groupdocs.com/net/signature/groupdocs.signature.domain/verificationresult)T5】对象。* 

```
if (result.IsValid)
{
    Console.WriteLine("\nDocument was verified successfully!");
}
else
{
    Console.WriteLine("\nDocument failed verification process.");
} 
```

Enter fullscreen mode Exit fullscreen mode

### 完整代码

```
using GroupDocs.Signature;
using GroupDocs.Signature.Domain;
using GroupDocs.Signature.Options;

using (Signature signature = new Signature("sample.pdf"))
{
    DigitalVerifyOptions options = new DigitalVerifyOptions("certificate.pfx")
    {
        Comments = "Test comment"
    };
    // verify document signatures
    VerificationResult result = signature.Verify(options);
    if (result.IsValid)
    {
        Console.WriteLine("\nDocument was verified successfully!");
    }
    else
    {
        Console.WriteLine("\nDocument failed verification process.");
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，您可以确定 PDF 文档中的数字签名是否符合指定的标准。最后，您可以将文档标记为有效或无效。

干杯！