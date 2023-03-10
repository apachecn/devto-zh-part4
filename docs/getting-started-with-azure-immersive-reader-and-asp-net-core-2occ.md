# Azure 沉浸式阅读器和 ASP.NET 核心入门

> 原文：<https://dev.to/kasuken/getting-started-with-azure-immersive-reader-and-asp-net-core-2occ>

Immersive Reader 是一项 Azure 认知服务，面向希望在应用中嵌入包容性功能的开发人员，旨在增强用户的文本阅读和理解能力，无论用户年龄或能力如何。
沉浸式阅读器包括朗读文本、翻译、通过设计吸引用户注意力等功能，帮助用户在教室和办公室中获得收益。

如果你想了解一下这个很棒的云服务，你可以看看下面的视频。
[https://www.youtube.com/embed/ZrO-l1IKjbw](https://www.youtube.com/embed/ZrO-l1IKjbw)
现在，让我们看看如何在您的 web 应用程序中轻松实现沉浸式阅读器。

## 在 Azure 门户上创建沉浸式读者认知服务

首先，我们必须在 Azure 门户上创建一个新的认知服务。这项服务正在预览中，现在你可以通过以下链接轻松找到:
【https://portal.azure.com/#create/Microsoft. T2】CognitiveServicesImmersiveReader

目前只有几个地点可用，但我们希望在未来我们有更多的服务地点。
[![](img/3647ae4802ee378f61156544226edb8f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OtITT7eK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/blyl8eku6oc5hg32iba4.PNG) 
我建议用“西美”。

打开资源组中的沉浸式阅读器资源，并获取“Endpoint ”(在 Overview 选项卡中)和“Keys”选项卡上“Primary Key”的值。我们稍后需要这些值。

## 创建一个 ASP.NET 核心项目

从 Visual Studio 2019(或者 2017，这并不重要)，从新的菜单项中选择一个新的 ASP.NET 核心项目。

[![](img/3eec9ceff1684a487788bb6f8cf56d47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lq49ms7p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7ie83mcunw402wv6f30q.PNG)

然后，选择模型-视图-控制器 web 应用程序的模板。

[![](img/8003e053b7eb3a9bb38bcc34b7b5e358.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L7wzaHUX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9fvz7nhgsejayvcpdkqv.PNG)

从解决方案浏览器中，右键单击 web 项目并选择“管理用户机密”,然后添加这两个具有正确值的键。

```
{
  "SubscriptionKey": YOUR_SUBSCRIPTION_KEY,
  "Endpoint": YOUR_ENDPOINT
}
~~~~

The subscription key is a string similar to this: "cd45ec999e3143f7bb2fe83ad002705f"
and the endpoint like this: https://YOUR RESOURCE NAME.cognitiveservices.azure.com/sts/v1.0/issuetoken

Now in the HomeController.cs you can retrieve these informations using the dependency injection for the Configuration: 
```

Enter fullscreen mode Exit fullscreen mode

私有只读字符串 SubscriptionKey
私有只读字符串端点；

public home controller(I configuration configuration)
{
subscription key = configuration[" subscription key "]；
端点=配置[“端点”]；
}
~~~~

为了检索沉浸式读取器的访问令牌，您可以在同一个 HomeController 文件中创建一个名为“token”的新路由。

```
[Route("token")]
public async Task<string> Token()
{
    using (var client = new System.Net.Http.HttpClient())
    {
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
        using (var response = await client.PostAsync(Endpoint, null))
        {
            var content = await response.Content.ReadAsStringAsync();

            return content;
        }
    }
}
~~~~

I prefer this approach because ASP.NET Core runs on the server-side and so basically you can hide your secrets information like the key and the endpoint.
If you write this function on a Javascript function, everyone can take this private information.

Now it's time to add the UI and some example content in our application.

Open the "Index.cshtml" file from the folder "Views" and replate the content with the following code: 
```

Enter fullscreen mode Exit fullscreen mode

阅读

# 道林·格雷的照片

```
<p>
    In the stately London home of his aunt, Lady Brandon, the well-known artist Basil Hallward meets Dorian Gray. Dorian is a cultured, wealthy, and impossibly beautiful young man who immediately captures Basil’s artistic imagination. Dorian sits for several portraits, and Basil often depicts him as an ancient Greek hero or a mythological figure. When the novel opens, the artist is completing his first portrait of Dorian as he truly is, but, as he admits to his friend Lord Henry Wotton, the painting disappoints him because it reveals too much of his feeling for his subject. Lord Henry, a famous wit who enjoys scandalizing his friends by celebrating youth, beauty, and the selfish pursuit of pleasure, disagrees, claiming that the portrait is Basil’s masterpiece. Dorian arrives at the studio, and Basil reluctantly introduces him to Lord Henry, who he fears will have a damaging influence on the impressionable, young Dorian.
</p> 
```

Enter fullscreen mode Exit fullscreen mode

@部分脚本{

```
function getImmersiveReaderTokenAsync() {
    return new Promise((resolve) =&gt; {
        const url = '/token';
        fetch(url)
            .then((response) =&gt; {
                if (response.ok) {
                    resolve(response.text());
                } else {
                    throw new Error('Server response wasn\'t OK');
                }
            })
            .catch(function (error) {
                console.log(JSON.stringify(error));
            });

    });
}

async function launchImmersiveReader() {

    const content = {
        title: document.getElementById('title').innerText,
        chunks: [{
            content: document.getElementById('content').innerText,
            lang: 'en'
        }]
    };

    const options = {
        uiZIndex: 1000000
    }

    const token = await getImmersiveReaderTokenAsync();
    ImmersiveReader.launchAsync(token, content, options);
} 
```

Enter fullscreen mode Exit fullscreen mode

}
~~~

基本上，我在页面顶部创建了一个按钮，我们的内容和下面的一些段落的标题:没有什么特别的。

在“脚本”部分，我添加了对沉浸式阅读器 SDK 的引用。目前我使用的是版本 0.0.1(稳定版),但也有一个金丝雀版本:

```
<script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js'></script>
~~~~ 
```

Enter fullscreen mode Exit fullscreen mode

~~~~

在“launchImmersiveReader”方法中，我们可以开始配置沉浸式阅读器。首先，我们创建一个名为“内容”的新对象，它有两个属性。
一个是我们内容的标题，另一个是“块”属性。
在最后一个里面，我们有另外两个属性，分别是页面内容和语言。
对于内容，您可以直接从 DOM 中检索，也可以调用 REST API 服务。

完成这部分配置后，您必须从我们的服务器端函数中检索身份验证令牌。我使用 Promise 和 Fetch 是因为我喜欢 JavaScript 中新的 async/await 方法。
更容易阅读和调试。

如果您启动您的应用程序，您可以用几行代码看到令人惊叹的结果。

[![](img/aa807e8e923e0af74a6e6df7d02f2e5a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Lgc_AmcH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zp72o2bdjqlo8886a5hr.gif)

## 资源

源代码:[https://github.com/kasuken/ImmersiveReaderCore](https://github.com/kasuken/ImmersiveReaderCore)
演示站点:[https://immersivereaderexperiments.azurewebsites.net/](https://immersivereaderexperiments.azurewebsites.net/)
官方文档:[https://azure . Microsoft . com/en-GB/services/cognitive-services/immersive-reader/](https://azure.microsoft.com/en-gb/services/cognitive-services/immersive-reader/)