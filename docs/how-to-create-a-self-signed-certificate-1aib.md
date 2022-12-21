# 如何创建自签名证书

> 原文：<https://dev.to/suntong/how-to-create-a-self-signed-certificate-1aib>

### 前缀

所有以前的 [squid 缓存服务器](http://www.squid-cache.org/)系列都谈到了 SSL 碰撞，并包括创建自签名证书的步骤。也就是说，它已经被过度讨论了-

*   你可以在互联网上找到很多其他的博客/文章来讨论这个问题。另外，
*   stackoverflow 上有一个[问题，有一千多张 up 票。](https://stackoverflow.com/questions/10175812/)

那为什么还要另一个呢？

*   首先，许多这样的博客/文章只是谈论它，给出一些命令但没有详细解释参数(就像我们在本系列中覆盖的前两篇文章)。例如，评论 *`-nodes`并不意味着“节点”*在解释被添加到上述接受的答案之前已经获得了超过四百张赞成票。
*   你会发现，在互联网上的所有博客/文章中，他们每个人的做法都有一点点不同。那么为什么会有差异呢？再说一遍，它们中有过时的吗？
*   最重要的是，被接受的答案并不是唯一的答案，如果你向下滚动，你会意识到有比被接受的答案更多的事情需要谨慎对待。举个例子，
*   现代浏览器现在会对格式良好的自签名证书抛出一个安全错误...，所以很多开发者的教程和书签突然就过时了。"一条评论很容易被遗漏，因为它隐藏在所有其他答案中，并且位于答案的最下方，你必须滚动很长一段距离才能找到它。

这是我收集事实的博客 [squid 缓存服务器](http://www.squid-cache.org/)系列的一部分，扩展了创建自签名证书部分，它结合了来自 Linode 的[创建自签名 TLS 证书](https://www.linode.com/docs/security/ssl/create-a-self-signed-tls-certificate/)的图片&和来自上面 [stackoverflow 问题](https://stackoverflow.com/questions/10175812/)的答案。

### 什么是自签名 TLS 证书？

自签名 TLS 证书适用于个人使用或组织内部使用的应用程序。如果您打算在网站上使用您的 SSL 证书，一旦您完成了本指南中概述的过程，请参阅我们的(linode.com)指南为 [NGINX](https://www.linode.com/docs/web-servers/nginx/enable-tls-on-nginx-for-https-connections/) 启用 TLS。

### 创建证书-入门

再次转贴自 linode 的文章，

1.  切换到`root`用户，并切换到您想要创建证书和密钥对的目录。该位置将根据您的需求而有所不同。这里我们将使用`/root/certs`:

    ```
    su - root
    mkdir /root/certs && cd /root/certs 
    ```

2.  创建证书:

    ```
    openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out MyCertificate.crt -keyout MyKey.key 
    ```

系统会提示您将网站或组织的识别信息添加到证书中。因为自签名证书不会公开使用，所以不需要这些信息。如果此证书将被传递给证书颁发机构进行签名，则信息需要尽可能准确。

下面是这个命令中使用的 OpenSSL 选项的分类。有许多其他选项可用，但这些将创建一个一年有效的基本证书。有关更多信息，请参见终端中的`man openssl`。

*   `-newkey rsa:4096`:创建一个 4096 位 RSA 密钥，用于证书。`RSA 2048`是 OpenSSL 最新版本的默认设置，但是为了确保密钥大小，您应该在创建时指定它。
*   `-x509`:创建自签名证书。
*   `-sha256`:使用 265 位 SHA(安全哈希算法)生成证书请求。
*   `-days`:确定证书签发的时间长度(以天为单位)。对于自签名证书，该值可以根据需要增加。
*   `-nodes`:创建一个不需要密码的证书。如果排除此选项，每次重新启动使用它的应用程序时，您都需要在控制台中输入密码。

1.  限制密钥的权限，以便只有`root`可以访问它:

    ```
    chmod 400 /root/certs/MyKey.key 
    ```

2.  将您的证书和密钥备份到外部存储。这是重要的一步。不要跳过它！

### 创建证书-全九码

那很清楚。可惜现在却陷入了那些*【突然过时】*开发者的教程和书签，因为*“现代浏览器现在抛出一个安全错误”*。所以以下是以上 stackoverflow 问题的总结，据我所知。

创建自签名证书(不链接回受信任的锚点)包括:

1.  创建您自己的权威(即成为 CA)
2.  为服务器创建证书签名请求(CSR)
3.  用您的 CA 密钥签署服务器的 CSR

但是细节决定成败:

*   以上可能需要几个步骤，但也可以在一个命令中完成。
*   过时文章中的一些命令使用了`SHA-1`哈希算法，这在许多浏览器中会在控制台中抛出警告，因为现在“SHA-1 证书”被认为是不安全的。所以记得使用`-sha256`来生成基于 SHA-256 的证书。
*   但是，有一种说法是，使用`SHA-2`不会给自签名证书增加任何安全性(2018 年 5 月更新)。如果这与你有关，请在查看详情[。](https://stackoverflow.com/a/26462803/2125837)
*   *“将提示您添加识别信息”*，因此使用`-subj '/CN=localhost'`(将`localhost`替换为您想要的域)来隐藏关于证书内容的整个问题列表。但是，请注意，由于以下原因，现在不推荐使用`CN`。
*   现代浏览器现在会为格式良好的自签名证书抛出一个安全错误，如果它们缺少一个`SAN` (Subject Alternate Name)，因此许多开发人员的教程和书签突然过时了。有一段时间，OpenSSL 无法提供命令行方式来指定`SAN`，您必须首先创建一个简短的独立 conf 文件。然而，许多例子都是这样做的，
*   截至 2019 年，所有都可以在一个单一命令中完成，包括`SAN`。
*   注意，使事情复杂化的是，浏览器遵循 CA/Browser 论坛策略；而不是 IETF 的政策。这就是用 OpenSSL(通常遵循 IETF)创建的证书有时无法在浏览器下验证(浏览器遵循 CA/B)的原因之一。它们是不同的标准，有不同的发布政策和不同的验证要求。
*   因此，将 DNS 名称放在`SAN`而不是`CN`中很重要，因为 IETF 和 CA/Browser 论坛都规定了这种做法。他们还规定`CN`中的 DNS 名称是不推荐的(但不是禁止的)。如果您将一个 DNS 名称放在`CN`中，那么它必须包含在`CA/B`策略下的`SAN`中。所以你不能避免使用主题别名`SAN`。
*   自签名证书提供了与可信机构签名的证书相同的加密级别([每年花费约 1000 ~ 2000 美元](https://www.websecurity.symantec.com/buy-renew?ef_id=Cj0KCQiAwc7jBRD8ARIsAKSUBHL3u_wl0kyXRrvQM3Fj2H9j9T6FogZsQVjmxinnw6KIf9xVvUP9O64aAj0KEALw_wcB:G:s&s_kwcid=AL!6100!3!225648135095!e!!g!!symantec%20ssl%20certificate&gclid=Cj0KCQiAwc7jBRD8ARIsAKSUBHL3u_wl0kyXRrvQM3Fj2H9j9T6FogZsQVjmxinnw6KIf9xVvUP9O64aAj0KEALw_wcB))，但是它不会经过任何第三方的验证，除非您将它导入浏览器一次。

### 创建证书- [实际命令](https://stackoverflow.com/a/41366949/2125837)

截至 2019 年，以下命令可满足您的所有需求，包括 SAN:

```
openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 -nodes -keyout example.key -out example.crt -extensions san -config <(echo "[req]"; echo distinguished_name=req; echo "[san]"; echo subjectAltName=DNS:example.com,DNS:example.net,IP:10.0.0.1) -subj /CN=example.com 
```

Enter fullscreen mode Exit fullscreen mode

此外，对于 OpenSSL ≥ 1.1.1，这可以缩短为:

```
openssl req -x509 -newkey rsa:4096 -sha256 -days 3650 -nodes -keyout example.key -out example.crt -subj /CN=example.com -addext subjectAltName=DNS:example.com,DNS:example.net,IP:10.0.0.1 
```

Enter fullscreen mode Exit fullscreen mode

它创建一个证书

*   对域`example.com`和`example.net` (SAN)有效，
*   对 IP 地址`10.0.0.1` (SAN)也有效，
*   相对较强(截至 2019 年)和
*   有效期为`3650`天(~10 年)。

它创建以下文件:

*   私钥:`example.key`
*   证书:`example.crt`

所有信息都在命令行中提供。没有烦人的交互输入。不要乱动配置文件。这个 OpenSSL 调用执行了所有必要的步骤:从私钥生成到自签名证书。

**备注#1:加密参数**

由于证书是自签名的，需要用户手动接受，因此使用短的过期时间或弱的加密技术没有意义。

在未来，你可能想使用比`4096`位更多的 RSA 密钥和比`sha256`更强的哈希算法，但截至 2019 年，这些都是合理的值。它们足够强大，同时受到所有现代浏览器的支持。

**备注#2:参数“`-nodes`”**

理论上你可以省略掉参数`-nodes`(这意味着“没有 DES 加密”)，在这种情况下`example.key`会用密码加密。但是，这对于服务器安装几乎没有用处，因为您要么必须将密码也存储在服务器上，要么必须在每次重启时手动输入密码。

**再版 3:明华**

在 MinGW bash 的 Windows 上，您应该在命令前加上前缀`MSYS_NO_PATHCONV=1`:

```
MSYS_NO_PATHCONV=1 openssl ... 
```

Enter fullscreen mode Exit fullscreen mode

或者，在普通的`cmd.exe` Windows 命令提示符下运行命令。

**备注#4:参见**

*   [在命令行上直接向 openssl 提供 subject altname](https://security.stackexchange.com/a/198409/133603)
*   如何通过命令行向 SSL 证书添加多个电子邮件地址？
*   [关于 MSYS_NO_PATHCONV 的更多信息](https://stackoverflow.com/a/34386471/19163)

将两个(私钥`.key` &证书`.crt`)组合成一个`.pem`文件:

`cat server.crt server.key > cert.pem`

此外，为了使用自签名证书，您必须

*   在服务器上安装服务器证书
*   在客户端安装 CA 证书

我们已经在以前的文章中讨论过了。

### 创建证书-参数说明

以下是[选项](https://stackoverflow.com/a/10176685/456814)的更详细描述，来自[文档](https://www.openssl.org/docs/apps/req.html):

```
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days XXX 
```

Enter fullscreen mode Exit fullscreen mode

> ```
> req 
> ```
> 
> PKCS#10 证书申请和证书生成实用程序。
> 
> ```
> -x509 
> ```
> 
> 此选项输出自签名证书，而不是证书请求。
> 这通常用于生成测试证书或自签名根 CA。
> 
> ```
> -newkey arg 
> ```
> 
> 此选项创建新的证书申请和新的私钥。论点
> 有几种形式。 **rsa:nbits** ，其中 **nbits** 为位数，
> 生成一个 rsa 密钥 **nbits** 大小。
> 
> ```
> -keyout filename 
> ```
> 
> 这给出了要写入新创建的私钥的文件名。
> 
> ```
> -out filename 
> ```
> 
> 默认情况下，它指定要写入的输出文件名或标准输出。
> 
> ```
> -days n 
> ```
> 
> 当使用 **-x509** 选项时，指定认证
> 证书的天数。默认值为 30 天。
> 
> ```
> -nodes 
> ```
> 
> 如果指定了此选项，那么如果创建了私钥，它将不会被加密。

文档实际上比上面的总结更详细。

### 创建证书-面向公众

如果你想获得一个真正的证书，在公共互联网上任何人都可以识别，那么程序如下。

1.  生成私钥
2.  使用私钥创建 CSR 文件
3.  将 CSR 提交给 CA (Verisign 或其他，等等。)
4.  在 web 服务器上安装从 CA 收到的证书
5.  根据证书类型向认证链添加其他证书

查看详情[此处](https://stackoverflow.com/a/29406186/2125837)。