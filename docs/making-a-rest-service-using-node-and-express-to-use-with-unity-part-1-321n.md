# 使用 Node 和 Express 创建 REST 服务以便与 Unity 一起使用——第 1 部分

> 原文：<https://dev.to/cemuka/making-a-rest-service-using-node-and-express-to-use-with-unity-part-1-321n>

用 unity 做个游戏超级爽。但是在线功能呢，比如保存一些数据到数据库，比如用户信息，进度，物品，敌人...？你可能熟悉 node 和 rest api，但是在 unity 中发送请求和处理收到的数据有点麻烦。

所以我们来抛一个 unity 和 node 的博客系列。然后我们要在 heroku 上举办！

在这一部分中，我们将制作“Hello World”项目。

这里是 github 上的项目链接。

如果你从未听说过 rest api，我推荐你先看看这个[教程](https://www.youtube.com/watch?v=Q-BpqyOT3a8)了解一下它的意思。

然后，让我写一些代码。

首先打开一个终端，启动我们的节点项目文件。

```
npm init 
```

第二，安装 express。

```
npm install express 
```

我们的切入点。姑且称之为 app.js.

```
touch app.js 
```

用您最喜欢的代码编辑器打开新创建的文件(本例中我的是 vscode)并导入 express。

```
const express = require('express');
const app = express(); 
```

我们的应用程序应该监听一个端口，3000。第二个参数，一个回调，写一个消息到控制台。

```
app.listen(3000, () => console.log('started and listening.')); 
```

运行前的最后一步。当你问我们的应用程序，我们应该回应用户。在表达这个过程愚蠢地简化与下面。当我们点击`localhost:3000`或用 postman 打电话时，我们的应用程序将在某些端点上做出响应。对于 hello world 项目，让我们对主目录“/”进行响应。

如果你不明白端点或 api、http 回调协议是什么意思，我再次建议你先看看这个教程。

```
app.get('/', (req, res) => {
    res.send('Hello Unity Developers!');
}) 
```

让我们在运行之前看一下我们的代码。

```
const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send('Hello Unity Developers');
})

app.listen(3000, () => console.log('started and listening.')); 
```

打开终端并运行代码。

```
node app.js 
```

[![Alt Text](img/b79e0963d92ed4f0fc1748ed923024c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GjWlco7d--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9pwq74au402o0kr6d39w.png)

如果没有发生错误，我们应该在终端上看到我们的 console.log。

现在让我们在切换到 unity 之前测试一下。
去你的浏览器或者邮差那里测试我们的地址`localhost:3000`。

[![Postman view](img/a866dc2f3fd00e4aa75557cd97cacf7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TkIdNDNL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ujhkrh3ttkql7pugi6r7.png)

[![Browser view](img/de26dcea1a9b1bd5932e0a1253b669dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9Xmu2vex--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/y46p0r6ujitkuymvb3os.png)

好像管用！因此，我们创建了一个工作服务，当我们请求时，它会回复。我们在 unity 中做得如何？

幸运的是 unity 有一些 http 回调包装器。

让我们打开一个新的空 unity 项目，在场景中创建一个空的 GameObject，将其重命名为“client”。

[![Client GameObject](img/0122ad31eb7dc88aa63452954390286b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ePOPJbZC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/7kaleh7hm4c887lnvy9x.png)

现在在项目文件夹中创建一个 c#脚本，我将其命名为 ClientAPI。

[![ClientApi Script](img/336dc64ec44d9da12e6d0f7d64f16fb5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sQztLIfw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/jrymo529i4vvu1ilhn1m.png)

在这个脚本中，我们应该在编写任何代码之前添加一个新的名称空间。

```
using UnityEngine.Networking; 
```

然后我们将定义一个方法，返回一个在 unity 中启用类似异步功能的`IEnumerator`。如果你不确定如何使用 unity 协程，这个[资源](https://www.youtube.com/watch?v=5L9ksCs6MbE)可能是一个好的开始。

```
public IEnumerator Get(string url)
{

} 
```

要发出 web 请求，让我们定义一个新的`UnityWebRequest`

```
public IEnumerator Get(string url)
{
    using(UnityWebRequest www = UnityWebRequest.Get(url))
    {

    }
} 
```

我将在这里放一个[链接](https://stackoverflow.com/questions/212198/what-is-the-c-sharp-using-block-and-why-should-i-use-it)来解释为什么我们需要一个 using 语句。也可以查看 [unity docs](https://docs.unity3d.com/ScriptReference/Networking.UnityWebRequest.Get.html) 进行进一步阅读。

这一部分是库罗廷魔法涉及的地方。当发送请求时，我们应该使用`yield return keyword`,它将确保过程完成，直到它完成。

```
public IEnumerator Get(string url)
{
    using(UnityWebRequest www = UnityWebRequest.Get(url))
    {
        yield return www.SendWebRequest();
    }
} 
```

之后，检查错误。

```
public IEnumerator Get(string url)
{
    using(UnityWebRequest www = UnityWebRequest.Get(url))
    {
        yield return www.SendWebRequest();

        if (www.isNetworkError)
        {
            Debug.Log(www.error);
        }
        else
        {

        }
    }
} 
```

如果没有错误，那么它就成功完成了。

我们已经从 http 连接中获得了一些数据，但是我们应该将这些数据解析为一个字符串，以便进一步工作。我们将从 api 中以`www.downloadHandler.data`的形式输入数据。将解析后的数据作为字符串返回。

```
public IEnumerator Get(string url)
{
    using(UnityWebRequest www = UnityWebRequest.Get(url))
    {
        yield return www.SendWebRequest();

        if (www.isNetworkError)
        {
            Debug.Log(www.error);
        }
        else
        {
            if (www.isDone)
            {
                // handle the result
                var result = System.Text.Encoding.UTF8.GetString(www.downloadHandler.data);
                Debug.Log(result);
            }
            else
            {
                //handle the problem
                Debug.Log("Error! data couldn't get.");
            }
        }
    }
} 
```

并定义一个公共变量来提供 url。
现在让我们试一试，在`Start`上调用我们的方法。要使用协程，我们必须将其称为`StartCoroutine()`，否则它将无法工作。
看一下整个剧本。

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;

public class ClientApi : MonoBehaviour
{
    public string url;

    void Start()
    {
        StartCoroutine(Get(url));
    }

    public IEnumerator Get(string url)
    {
        using(UnityWebRequest www = UnityWebRequest.Get(url)){
            yield return www.SendWebRequest();

            if (www.isNetworkError)
            {
                Debug.Log(www.error);
            }
            else
            {
                if (www.isDone)
                {
                    // handle the result
                    var result = System.Text.Encoding.UTF8.GetString(www.downloadHandler.data);
                    Debug.Log(result);
                }
                else
                {
                    //handle the problem
                    Debug.Log("Error! data couldn't get.");
                }
            }
        }

    }
} 
```

跳回 unity 编辑器，给客户端游戏对象添加脚本。

[![Attach Script](img/1ba9776f721ad5f560830392799fbe03.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IMH5251D--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e20nmx0ms0s6tc9ov6vp.png)

接下来，输入我们的地址到网址。

[![Provide url](img/91ff511fed2ae4459af6715b9c7456b0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wEeJPWES--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9qda21jwhvundpbu14u8.png)

现在点击播放。

[![Debug.Log image](img/38a7c9c2b0aaae72d6ab0be1b4534bbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Q3k0Hm7J--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ny4vhibbua50k5u3k4o.png)

如果成功，我们应该会在控制台上看到该消息。

哇！是不是很酷？

我们的服务器和 unity 客户端非常好用。恭喜你。

这里是 github 上的项目链接。

## 奖金

让我们添加一些预告片图片，为接下来的部分做一些宣传:)

[![postman result orc](img/64ed908af27740557d76977927d08bbb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gq-BHpKi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/woc7ecq5pm6l9lhryzew.png)

[![Unity client url](img/44610216577ea083565aaa1afc45ff00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z_taZc7I--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ygtk802i0csgweurcidj.png)

[![Debug.Log result](img/77a22a2751e7d748818b574ba7a724b3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jlwLitNj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fet8pjkqq0wedo2dohty.png)