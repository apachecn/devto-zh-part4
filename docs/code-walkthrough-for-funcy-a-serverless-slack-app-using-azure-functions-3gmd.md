# 教程:为 Slack 构建一个无服务器的 API 后端(第 2 部分)

> 原文：<https://dev.to/azure/code-walkthrough-for-funcy-a-serverless-slack-app-using-azure-functions-3gmd>

在[之前的博客](https://dev.to/azure/funcy-a-serverless-slack-app-using-azure-functions-4m84)中，我们探索了如何在 [Azure Functions](https://azure.microsoft.com/en-in/services/functions/?wt.mc_id=servsept_devto-blog-abhishgu) 上部署 Slack slash 命令的无服务器后端。正如所承诺的，现在是时候遍历代码，看看这个函数是如何实现的。代码在 GitHub 上有[供你查找。](https://github.com/abhirockzz/funcy-azure-functions/)

> 如果你有兴趣学习使用 Azure 功能进行无服务器开发，只需[创建一个免费的 Azure 账户](https://azure.microsoft.com/en-us/free/?wt.mc_id=servsept_devto-blog-abhishgu)就可以开始了！我强烈推荐查看文档中的[快速入门指南、教程和代码示例](https://docs.microsoft.com/en-in/azure/azure-functions/?wt.mc_id=servsept_devto-blog-abhishgu)，如果您喜欢，可以使用[指导学习路径](https://docs.microsoft.com/en-in/learn/paths/create-serverless-applications/?wt.mc_id=servsept_devto-blog-abhishgu)，或者下载[无服务器计算指南](https://azure.microsoft.com/en-in/resources/azure-serverless-computing-cookbook/?wt.mc_id=servsept_devto-blog-abhishgu)。

### 结构

代码结构如下:

*   功能逻辑驻留在`Funcy`类中(包`com.abhirockzz.funcy`
*   并且，其余的东西包括分别用于 GIPHY 和 Slack 的模型类，即 POJOs 包`com.abhirockzz.funcy.model.giphy`和`com.abhirockzz.funcy.model.slack`

### 功能入口点

`handleSlackSlashCommand`方法定义了我们函数的入口点。它用`@FunctionName`注释进行了修饰(还定义了函数名- `funcy`)

```
@FunctionName(funcy)
public HttpResponseMessage handleSlackSlashCommand(
        @HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
        final ExecutionContext context) 
```

Enter fullscreen mode Exit fullscreen mode

它是通过 HTTP 请求(来自 Slack)触发的。这由`@HttpTrigger`注释定义。

```
@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) 
```

Enter fullscreen mode Exit fullscreen mode

请注意，该函数:

*   响应 HTTP `POST`
*   调用不需要显式授权

> 有关详细信息，请查看[函数运行时 Java API](https://docs.microsoft.com/java/api/overview/azure/functions/runtime?view=azure-java-stable&WT.mc_id=servsept_devto-blog-abhishgu) 文档
> 
> *   [T2`@FunctionName`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.functionname?view=azure-java-stable&WT.mc_id=servsept_devto-blog-abhishgu)
> *   [T2`@HttpTrigger`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.httptrigger?view=azure-java-stable&WT.mc_id=servsept_devto-blog-abhishgu)
> *   [T2`HttpMethod (Enum)`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.httpmethod?view=azure-java-stable&WT.mc_id=servsept_devto-blog-abhishgu)

#### 方法参数

`handleSlackSlashCommand`由两个参数组成- `HttpRequestMessage`和`ExecutionContext`。

`HttpRequestMessage`是 Azure Functions Java 库中的一个 helper 类。它的实例是在运行时注入的，用于访问 HTTP 消息体和消息头(由 Slack 发送)。

`ExecutionContext`提供了一个函数运行时的钩子——在本例中，它用于获得`java.​util.​logging.Logger`的句柄。

> 有关详细信息，请查看[函数运行时 Java API](https://docs.microsoft.com/java/api/overview/azure/functions/runtime?view=azure-java-stable&WT.mc_id=servsept_devto-blog-abhishgu) 文档
> 
> *   [T2`HttpRequestMessage`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.httprequestmessage?view=azure-java-stable&WT.mc_id=servsept_devto-blog-abhishgu)
> *   [T2`ExecutionContext`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.executioncontext?view=azure-java-stable&WT.mc_id=servsept_devto-blog-abhishgu)

### 从松弛斜线命令到 GIPHY GIF - core 逻辑

从最终用户的角度来看，这段旅程是短暂而甜蜜的！但这里有一个幕后发生的事情的要点。

Slack 请求的类型是`application/x-www-form-urlencoded`，首先被解码，然后被转换成 Java `Map`以便于使用。

```
 Map<String, String> slackdataMap = new HashMap<>();
    try {
        decodedSlackData = URLDecoder.decode(slackData, "UTF-8");
    } catch (Exception ex) {
        LOGGER.severe("Unable to decode data sent by Slack - " + ex.getMessage());
        return errorResponse;
    }

    for (String kv : decodedSlackData.split("&")) {
        try {
            slackdataMap.put(kv.split("=")[0], kv.split("=")[1]);
        } catch (Exception e) {
            /*
            probably because some value in blank - most likely 'text' (if user does not send keyword with slash command).
            skip that and continue processing other attrbiutes in slack data
             */
        }
    } 
```

Enter fullscreen mode Exit fullscreen mode

> 参见[https://api.slack.com/slash-commands#app_command_handling](https://api.slack.com/slash-commands#app_command_handling)

GIPHY API 密钥和松弛签名密钥是该函数工作所必需的。这些是通过环境变量来预期的——如果它们不存在，我们很快就会失败。

```
 String signingSecret = System.getenv("SLACK_SIGNING_SECRET");

    if (signingSecret == null) {
        LOGGER.severe("SLACK_SIGNING_SECRET environment variable has not been configured");
        return errorResponse;
    }
    String apiKey = System.getenv("GIPHY_API_KEY");

    if (apiKey == null) {
        LOGGER.severe("GIPHY_API_KEY environment variable has not been configured");
        return errorResponse;
    } 
```

Enter fullscreen mode Exit fullscreen mode

#### 签名验证

发送到我们函数的每个 Slack HTTP 请求都在`X-Slack-Signature` HTTP 头中包含一个签名。它是通过使用标准的`HMAC-SHA256`密钥散列，结合请求体和松弛应用程序签名秘密而创建的。

该函数还计算一个签名(基于配方[),并确认它与 Slack 发送的签名相同——否则我们不继续。这是在`matchSignature`方法中完成的](https://api.slack.com/docs/verifying-requests-from-slack#a_recipe_for_security)

```
private static boolean matchSignature(String signingSecret, String slackSigningBaseString, String slackSignature) {
    boolean result;

    try {
        Mac mac = Mac.getInstance("HmacSHA256");
        mac.init(new SecretKeySpec(signingSecret.getBytes(), "HmacSHA256"));
        byte[] hash = mac.doFinal(slackSigningBaseString.getBytes());
        String hexSignature = DatatypeConverter.printHexBinary(hash);
        result = ("v0=" + hexSignature.toLowerCase()).equals(slackSignature);
    } catch (Exception e) {
        LOGGER.severe("Signature matching issue " + e.getMessage());
        result = false;
    }
    return result;
} 
```

Enter fullscreen mode Exit fullscreen mode

一旦签名匹配，我们就可以确信我们的函数确实被 Slack 调用了。

#### 调用 GIPHY API

使用用户发送的搜索标准(关键字)调用 [GIPHY 随机 API](https://developers.giphy.com/docs/) ，例如`/funcy cat` - `cat`是关键字。这由`getRandomGiphyImage`负责，是一个使用 [Apache HTTPClient 库](https://hc.apache.org/)的简单 HTTP GET。使用 [Jackson](https://github.com/FasterXML/jackson) 将 JSON 响应整理成一个`GiphyRandomAPIGetResponse` POJO。

请注意，`org.apache.http.impl.client.CloseableHttpClient`(下面代码片段中的`HTTP_CLIENT`)对象是延迟创建的，每个函数只有一个实例，也就是说，每次调用一个函数时，不会创建新的对象。

> 有关详细信息，请参考 Azure 功能操作指南中的[这一节](https://docs.microsoft.com/azure/azure-functions/manage-connections?WT.mc_id=servsept_devto-blog-abhishgu#static-clients)

```
....
private static CloseableHttpClient HTTP_CLIENT = null;
....
private static String getRandomGiphyImage(String searchTerm, String apiKey) throws IOException {
    String giphyResponse = null;
    if (HTTP_CLIENT == null) {
        HTTP_CLIENT = HttpClients.createDefault();
        LOGGER.info("Instantiated new HTTP client");
    }
    String giphyURL = "http://api.giphy.com/v1/gifs/random?tag=" + searchTerm + "&api_key=" + apiKey;
    LOGGER.info("Invoking GIPHY endpoint - " + giphyURL);

    HttpGet giphyGETRequest = new HttpGet(giphyURL);
    CloseableHttpResponse response = HTTP_CLIENT.execute(giphyGETRequest);

    giphyResponse = EntityUtils.toString(response.getEntity());

    return giphyResponse;
}
....
GiphyRandomAPIGetResponse giphyModel = MAPPER.readValue(giphyResponse, GiphyRandomAPIGetResponse.class); 
```

Enter fullscreen mode Exit fullscreen mode

#### 将响应发送回 Slack

我们提取图片所需的信息- `title`(例如猫感恩节 GIF)及其`URL`(例如[https://media 2 . gi phy . com/media/v2 caxwlfw 4 a5y/gi phy-downsized . GIF](https://media2.giphy.com/media/v2CaxWLFw4a5y/giphy-downsized.gif))。这用于创建一个代表返回给 Slack 的 [JSON 有效负载的`SlackSlashCommandResponse` POJO 实例。](https://api.slack.com/slash-commands#responding_immediate_response)

```
 String title = giphyModel.getData().getTitle();
    String imageURL = giphyModel.getData().getImages().getDownsized().getUrl();

    SlackSlashCommandResponse slackResponse = new SlackSlashCommandResponse();
    slackResponse.setText(SLACK_RESPONSE_STATIC_TEXT);

    Attachment attachment = new Attachment();
    attachment.setImageUrl(imageURL);
    attachment.setText(title);

    slackResponse.setAttachments(Arrays.asList(attachment)); 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们创建一个`HttpResponseMessage`的实例(使用 fluent builder API)。函数运行时负责将 POJO 转换成 Slack 期望的有效 JSON 有效负载。

```
return request.createResponseBuilder(HttpStatus.OK).header("Content-type", "application/json").body(slackResponse).build(); 
```

Enter fullscreen mode Exit fullscreen mode

> 有关 [`HttpResponseMessage`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.httpresponsemessage?view=azure-java-stable&WT.mc_id=servsept_devto-blog-abhishgu) 接口的详细信息，请查看[函数运行时 Java API](https://docs.microsoft.com/java/api/overview/azure/functions/runtime?view=azure-java-stable&WT.mc_id=servsept_devto-blog-abhishgu) 文档

在我们结束之前，让我们来看一下可能的错误场景以及如何处理它们。

### 错误处理

#### 一般错误

根据[松弛建议](https://api.slack.com/slash-commands#responding_with_errors)，代码中的异常被处理，并向用户返回重试响应。`SlackSlashCommandErrorResponse` POJO 表示请求用户重试操作的 JSON 有效负载，并以`HttpResponseMessage`对象的形式返回。

```
return request.createResponseBuilder(HttpStatus.OK).header("Content-type", "application/json").body(slackResponse).build(); 
```

Enter fullscreen mode Exit fullscreen mode

> 用户在空闲时收到一条`Sorry, that didn't work. Please try again.`消息

#### 用户错误

用户可能没有将搜索标准(关键字)与斜杠命令一起包括，例如`/funcy`。在这种情况下，用户在 Slack - `Please include a keyword with your slash command e.g. /funcy cat`中得到显式响应。

为了使这成为可能，检查请求数据(`Map`)是否存在特定属性(`text`)——如果不存在，我们可以确定用户没有发送搜索关键字。

```
 ....

    HttpResponseMessage missingKeywordResponse = request

            .createResponseBuilder(HttpStatus.OK)

            .header("Content-type", "application/json")

            .body(new SlackSlashCommandErrorResponse("ephemeral", "Please include a keyword with your slash command e.g. /funcy cat")).build();

   ....

   if (!slackdataMap.containsKey("text")) {

        return missingKeywordResponse;

    }

   .... 
```

Enter fullscreen mode Exit fullscreen mode

#### 
  
超时

如果调用超时(如果函数耗时超过 3000 毫秒，就会超时)，Slack 将自动发送一个明确的响应- `Darn - that slash command didn't work (error message: Timeout was reached). Manage the command at funcy-slack-app.`

### 资源

下面提到的资源是专门用来开发这篇博文中展示的演示应用程序的，所以你可能会发现它们也很有用！

*   [Azure Functions 开发者指南(通用)](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference/?wt.mc_id=servsept_devto-blog-abhishgu)和 [Azure Functions Java 开发者指南](https://docs.microsoft.com/en-us/azure/azure-functions/functions-reference-java/?wt.mc_id=servsept_devto-blog-abhishgu)
*   [如何使用 Java 创建函数并将其发布到 Azure Functions 的快速入门](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-first-java-maven/?wt.mc_id=servsept_devto-blog-abhishgu)
*   用于 Azure 功能的 Maven 插件
*   [如何在本地编码和测试 Azure 功能](https://docs.microsoft.com/en-us/azure/azure-functions/functions-develop-local/?wt.mc_id=servsept_devto-blog-abhishgu)
*   [如何在 Azure 函数中管理连接](https://docs.microsoft.com/en-us/azure/azure-functions/manage-connections/?wt.mc_id=servsept_devto-blog-abhishgu)
*   [GitHub 上 Azure 服务的 Maven 插件](https://github.com/microsoft/azure-maven-plugins)

我真的希望你喜欢这篇文章，并从中学到了一些东西！如果你做了，请喜欢并跟随。很高兴通过 [@abhi_tweeter](https://twitter.com/abhi_tweeter) 获得反馈或发表评论。