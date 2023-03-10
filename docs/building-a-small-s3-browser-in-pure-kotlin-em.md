# 用纯 kotlin 构建一个小型 S3 浏览器

> 原文：<https://dev.to/shavz/building-a-small-s3-browser-in-pure-kotlin-em>

(x-贴自我的博客[此处](https://www.shiveenp.com/2019/07/29/S3-Browser-With-Kweb/))

## 背景

最近，我发现自己手头有了一点额外的开发时间。通常，我的大部分项目都是从伟大的想法开始的，一旦我开始着手，我就会失去动力或者生活阻碍了我，事情就像一个私人 github 回购一样被搁置了。

然而，我偶然发现了 Kweb，这是一个服务器端渲染的 web 应用构建库，但完全是用 Kotlin 编写的。Kweb 提供了一个漂亮的 [dsl](https://en.wikipedia.org/wiki/Domain-specific_language) 式界面，通过编程定义应用程序的 html 元素来构建 web 应用程序，而不是手工编写 html 和 CSS。对于那些不知道的人，Kotlin 提供了一种非常好的方法来构建[类型的安全声明构建器](https://kotlinlang.org/docs/reference/type-safe-builders.html)。

因为它都是在 kotlin 代码中声明性地创建的，所以我们得到了 access 协程、扩展和所有使 Kotlin 工作如此愉快的好东西。对于那些以前使用过基于纯代码的服务器端渲染框架的人来说，这可能会让你想起 [Vaadin](https://vaadin.com/) ，它是这个领域的行业领导者，但是你可以在 [Kweb 常见问题解答](http://docs.kweb.io/en/latest/faq.html)中找到细微和不太细微的差异。

浏览他们的代码库，我认为这将是一个很好的机会，尝试通过使用纯 kotlin 来构建一个快速简单的应用程序。为什么这么问？因为它很快，我可以在几个小时内把它打碎。

## 简单的科特林 S3 客户端

首先，我编写了一个基本的 S3 浏览类，从小处着手——我复制了 AWS 示例中关于创建新 S#客户端的代码，然后添加了在给定桶中搜索所有公钥并获取一些元数据和下载链接的功能:

```
class S3Client(private val endpoint: String, private val bucketName: String) {

    private val client = AmazonS3ClientBuilder.standard()
        .withPathStyleAccessEnabled(true)
        .withEndpointConfiguration(AwsClientBuilder.EndpointConfiguration(endpoint, "ap-southeast-2"))
        .build()

    fun listAllKeys(): List<S3Data> {
        val req = ListObjectsV2Request().withBucketName(bucketName).withMaxKeys(10)
        val keyList = mutableListOf<S3Data>()
        client.listObjectsV2(req).objectSummaries.forEach {
            keyList.add(S3Data(it.key, "$endpoint/$bucketName/${it.key}", it.size.toString().toDouble() / 1000.0, it.lastModified.toString()))
        }
        return keyList
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`listAllKeys()`函数返回给定桶中所有键的列表，然后我可以将它映射到一个定制的 S3Data 类:

```
data class S3Data(
    val key: String,
    val downloadUrl: String,
    val size: Double,
    val lastModifiedAt: String
) 
```

Enter fullscreen mode Exit fullscreen mode

## 用 Kweb 构建 UI

完成后，我开始破解 UI 界面。我想要一些快速简单，甚至粗糙的东西，只是为了证明它都像提议的那样工作。所以我设置了一个容器，其中包含一些表单字段、一个搜索按钮和一个表，用于输入 S3 地区链接和存储桶的名称。

我还需要一个表来显示所有的键(没有分页，谁会在 PoCs 中构建分页呢？🤠).为了能够保存数据，我使用了一个叫做 [KVAR](https://github.com/kwebio/kweb-core/blob/master/src/main/kotlin/io/kweb/state/KVar.kt) 的东西，它只是一个状态存储，被 Kweb 用来支持通过[观察者模式](https://en.wikipedia.org/wiki/Observer_pattern)将状态变化传播到 web 应用程序。

以下代码片段获取并初始化 S3 数据 Kvar(最初设置为空列表)，最终将其传播到表格:

```
div(fomantic.ui.main.container).new {
    div(fomantic.ui.vertical.segment).new {
        div(fomantic.ui.header).text("Welcome to S3 Browser 💻")
    }

    val keyData = KVar(emptyList<S3Data>())

    val loader = div(mapOf("class" to "ui active text loader")).addText("Retrieving keys...")
    loader.setAttribute("class", "ui disabled text loader")
    createInputSegment(loader, keyData)
    createKeysTable(keyData)
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，这就是 Kwebs 与 kotlin 的深度集成真正派上用场的地方，因为它允许我们使用 [kotlin 协程](https://kotlinlang.org/docs/reference/coroutines-overview.html)来处理具有大量 i/o 的任务(比如从 AWS 桶中检索数据)。我最近开始在生产代码中频繁使用协程，毫无疑问，它们是编写异步任务而不用担心线程的最佳方式。低接触语法设置和结果是如此简单，几乎感觉像作弊。

下面的代码片段使用前面介绍的`S3Client`来启动一个协程，当用户点击搜索按钮时，显示一个加载图标，直到检索到所有数据或者抛出一个错误:

```
private fun ElementCreator<DivElement>.createInputSegment(
    loader: Element,
    keyData: KVar<List<S3Data>>
) {
    div(fomantic.ui.vertical.segment).new {
        div(fomantic.ui.input).new {
            val endpointInput = input(type = InputType.text, placeholder = "Enter S3 Endpoint Url")
            val bucketInput = input(type = InputType.text, placeholder = "Enter S3 Bucket Name")
            button(mapOf("class" to "ui primary button")).text("Search").on.click {
                GlobalScope.launch {
                    loader.setAttribute("class", "ui active text loader")
                    val s3Client =
                        S3Client(endpointInput.getValue().await(), bucketInput.getValue().await())
                    try {
                        keyData.value = s3Client.listAllKeys()
                    } catch (ex: Exception) {
                        p().execute(ERROR_TOAST)
                        loader.setAttribute("class", "ui disabled text loader")
                    }
                    if (keyData.value.isNotEmpty()) {
                        p().execute(SUCCESS_TOAST)
                        loader.setAttribute("class", "ui disabled text loader")
                    }
                }
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止一切顺利，现在我们已经成功地将数据放入了 Kvar 容器中，我们可以开始呈现一个表格了。现在，我还想显示一个漂亮的小图标，以表明检索到的对象是一个文件——并且还允许用户单击作为链接的键的名称，以便用户可以下载。

现在，据我所知，Kweb 没有能力通过 DSL 对象来指定，但是，它提供了在表元素中指定嵌套 HTML 的能力，以添加我自己的自定义行为。

```
private fun ElementCreator<DivElement>.createKeysTable(
    keyData: KVar<List<S3Data>>
) {
    table(mapOf("class" to "ui celled striped table")).new {
        thead().new {
            tr().new {
                th().text("Key")
                th().text("File Size (in KB)")
                th().text("Last Modified At")
            }
        }
        tbody().new {
            keyData.map {
                it.forEach {
                    tr().new {
                        td(mapOf("data-lable" to "Key")).innerHTML("<i class=\"file outline icon\"></i> <a target=\"_blank\" href=${it.downloadUrl} download=${it.key}>${it.key}</a>")
                        td(mapOf("data-lable" to "File Size")).text("${it.size} KB")
                        td(mapOf("data-lable" to "Last Modified At")).text(it.lastModifiedAt)

                    }
                }
            }
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码创建了一个新表，并在表中为提供的 bucket 中的每个公钥生成了一个新行。如果没有数据，则不会呈现任何内容。

这可能也是时候为 Kweb 的创建者们提供一个与预配置了漂亮 UI 元素的 [Fomantic UI](https://fomantic-ui.com/) 的集成了。尽管如此，集成并没有就此结束，还有一个很好的 APi，任何人都可以用他们最喜欢的 UI 元素库编写一个新的插件。

## 期末笔记

这就是使用 Kweb 编写一个简单的 S3 浏览 web 应用程序所需的全部内容。[这里的](https://secure-scrubland-34237.herokuapp.com/)是部署在 heroku 上的 app，完整的工作代码在 [github](https://github.com/shavz/Bows3r) 上。

Gif 演示:

[![](img/4595e102389dd724246b8650b3f08c60.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TPVvSEcH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://imgur.com/YoJdUxj.gif)