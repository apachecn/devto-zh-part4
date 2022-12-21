# 在 Jython 中使用 SOAP web 服务。

> 原文：<https://dev.to/angelamchunu/consuming-a-soap-web-service-in-jython-2lp9>

几个月前，我参与了一个项目，该项目需要一个自动化过程来与 API 进行通信。自动化是在 Jython 中完成的，Jython 是 Python 在 JVM 上的一个实现，它允许动态编译 java 对象。基本上，如果你想使用一种打包了工具库的脚本语言，你应该考虑 Jython。

在我选择这个之前，我经历了一些方法，我的第一个方法是使用 Python 库来使用它，但是防火墙阻止了任何 pip 安装。您可以尝试手动安装所有的依赖项，但是为什么不看着油漆变干呢？有一种绕过防火墙的方法，但只有在你有代理的情况下才有效，而我没有，所以我必须找到另一种方法。

我尝试的第二种方法是直接在 java 中使用它，并将其打包成一个 jar 文件，然后在 jython 应用程序中调用它。这将工作，但我不建议这样做，因为你是管理两个独立的应用程序。如果有东西坏了，你怎么知道你的问题在哪里？是 Jython 还是 Java？你必须得到原始的源代码，修改它，重新打包并重新测试。这种方法不容易维护，尤其是当您离开时。新开发人员只有 jar 文件，调试变得很痛苦。

有一个更简单的方法来解决这个问题。Jython 已经允许 Java 库。使用带有 Jython 语法的 Java 库是一种更简单的方法。我决定使用 SAAJ API，这就是我的做法。

我创建了一个返回 Beyonce 专辑发行日期的服务，给出了专辑名称(是的，我是一个超级粉丝)。

第一步是构造您要发送的 SOAP 消息。我做的和文档有点不同。我将 soap 信封添加到一个 xml 文件中，用一个将在代码中映射的变量替换参数(即专辑名)。

```
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:tem="http://tempuri.org/">
<soapenv:Header/>
<soapenv:Body>
<tem:GetBeyonceAlbumYear>
<!--Optional:-->
<tem:albumName>${albumName}</tem:albumName></tem:GetBeyonceAlbumYear>
</soapenv:Body>
</soapenv:Envelope> 
```

使用 apache commons 库中的 StrSubstitutor 方法，变量的映射相对容易。看看下面映射变量的代码:

```
def mapVariableToRequestFile(albumName):
  mapping = HashMap()
  mapping.put("albumName",albumName)
  xmlFile = File(pathToXmlRequest)
  fileStream = FileInputStream(xmlFile)
  template = IOUtils.toString(fileStream,"UTF-8")
  sub = StrSubstitutor(mapping)
  request = sub.replace(template)
  return request 
```

下一步是创建将要发送的实际消息。该库根据请求的字节创建消息，我们可以很容易地从 mapVariableToRequestFile 返回的请求中获得字节。有时需要显式强制转换，Jython 返回的类型不一定是您需要的类型。为了使用 String 类的 getBytes()方法，我必须将请求转换为字符串，因为它返回的是 unicode 对象。

```
def createSOAPMessage(request):
  messageFactory = MessageFactory.newInstance()
  stream = ByteArrayInputStream(String(request).getBytes())
  soapMessage = messageFactory.createMessage(None,stream)
  soapMessage.saveChanges()
  return soapMessage 
```

最后一步是发送实际的消息。在我的例子中，我没有使用任何身份验证(不需要证书)，因此使用了库的连接方法。

```
def sendSoapRequest():
  request = mapVariableToRequestFile("Lemonade")
  soapMessage = createSOAPMessage(request)
  soapConnectionFactory = SOAPConnectionFactory.newInstance()
  soapConnection = soapConnectionFactory.createConnection()
  soapResponse = soapConnection.call(soapMessage,soapEndpointUrl) 
```

通过写入 system.out 并按如下方式打印出来，可以很容易地看到响应。

```
soapResponse.writeTo(System.out)
System.out.println() 
```

这种方法不像在 java 中那样，不强迫你做任何错误处理。你可以像一个优秀的开发人员一样，自己添加异常类型，并相应地进行导入和处理。

为我的问题找到一个解决方案对我来说是一场噩梦，希望它能为其他人消除这个问题。