# Generate a QRCode vCard using Go

> 原文：<https://dev.to/donvitocodes/generate-a-qrcode-vcard-using-go-16j7>

每次收到名片，我总会检查里面有没有二维码。QR 码非常有用，因为现在大多数手机摄像头都可以自动识别 QR 码。一旦检测到包含 vCard 数据的二维码，手机会自动启动“添加联系人”功能，并从二维码中读取详细信息。因此，您只需点击“保存”即可添加新联系人。没有必要填写名片上的联系人信息。这节省了我们生命中的几秒钟。谁想填写数据？:)

我一直很好奇如何创建这些包含 vCard 数据的 QR 码，所以我搜索了如何使用 Go 生成它们。所以几分钟后，我想出了一个解决方案，使用一个名为 go-qrcode 的现有库。使用 go-qrcode 解决方案非常简单。

下面是该示例的完整源代码。由于 Go 非常简单，所以只需要几行代码就可以完成。该示例从 vcard.txt 文件中读取数据，并将 vcard 数据传递到 qrcode 库，该库使用编码的 vcard 数据生成 qrcode 图像。

```
package main

import (
    "io/ioutil"
    "log"
    "os"

    qrcode "github.com/skip2/go-qrcode"
)

func main() {

    file, err := os.Open("vcard.txt")
    if err != nil {
        log.Fatal(err)
    }
    defer file.Close()

    b, err := ioutil.ReadAll(file)
    if err != nil {
        log.Fatal(err)
    }

    str := string(b)
    qrFname := "vcard-qr.png"

    err = qrcode.WriteFile(str, qrcode.Medium, 256, qrFname)
    if err != nil {
        log.Fatal(err)
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用 ZXing 生成器来生成 vCard 数据。以下是 vcard.txt
的样本内容

```
BEGIN:VCARD
VERSION:3.0
N:Melvin Vivas
ORG:Freelance
TITLE:Senior Software Engineer
TEL:+1234567
URL:https://www.melvinvivas.com
EMAIL:melvindave@gmail.com
ADR:Singapore
END:VCARD 
```

Enter fullscreen mode Exit fullscreen mode

要运行该示例，您需要首先下载 go-qrcode。

然后，像往常一样，运行 main.go

```
$ cd src
$ go run main.go 
```

Enter fullscreen mode Exit fullscreen mode

运行应用程序后，这将在应用程序所在的目录下生成一个 vcard-qr.png 图像文件。

[![Alt Text](img/03c38f14953826d2a0de7c35a64e57cc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z9bjeW4V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/btjqzmdnd8th62mz2k29.png)

就是这样！希望这个例子对你有用！这个例子的源代码在我的 github repo 中。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png) [【多瑙河】](https://github.com/donvito)/[【go-qrcrd-vcard 示例】](https://github.com/donvito/go-qrcode-vcard-example)

### 下面是一个生成包含电子名片数据的 qrcode 的示例。

<article class="markdown-body entry-content container-lg" itemprop="text">

# go-QR code-vcard-示例

[![](img/cfcf98bc1c73c2e6a5a676635929b65a.png)](https://github.com/donvito/go-qrcode-vcard-example./src/vcard-qr.png)

## 下载 go-qrcode 库，以便我们可以在代码中使用

```
go get -u github.com/skip2/go-qrcode/... 
```

## 在我们的代码中，我们导入了 go-qrcode 库

```
import qrcode "github.com/skip2/go-qrcode" 
```

*   **从文件中读取 vcard 数据并生成二维码图像的示例代码**

    ```
     func main() {

        file, err := os.Open("vcard.txt")
        if err != nil {
          log.Fatal(err)
        }
        defer file.Close()

        b, err := ioutil.ReadAll(file)
        if err != nil {
          log.Fatal(err)
        }

        str := string(b)
        qrFname := "vcard-qr.png"

        err = qrcode.WriteFile(str, qrcode.Medium, 256, qrFname)
        if err != nil {
          log.Fatal(err)
        }

    } 
    ```

## 以下是可用于 vcard.txt 的示例数据

```
BEGIN:VCARD
VERSION:3.0
N:name
ORG:company
TITLE:title
TEL:123
URL:https://www.website.com
EMAIL:test@email.com
ADR:address1 address2
NOTE:memo
END:VCARD 
```

## 参考

*   [go-二维码](https://github.com/skip2/go-qrcode)
*   您可以使用 [Zxing 发生器](http://zxing.appspot.com/generator/)生成 vcard 数据

</article>

[View on GitHub](https://github.com/donvito/go-qrcode-vcard-example)

博客转自我的科技博客
[https://www.melvinvivas.com](https://www.melvinvivas.com)

对了，我在找一些围棋相关的开发工作。如果你有任何要求，请联系我。:)[http://www . melvinvivas . com/looking-for-remote-software-development-work-outsource](http://www.melvinvivas.com/looking-for-remote-software-development-work-outsource)