# 如何获取数据集并创建 ML 模型来对瑜伽姿势进行分类

> 原文：<https://dev.to/cassiebreviu/how-to-get-a-dataset-and-create-a-ml-model-to-classify-yoga-poses-1p>

本教程将介绍如何使用 Azure Cognitive Services 来自动获取图像数据集并训练自定义视觉机器学习模型。github repo 有一个简单的 nodejs 控制台应用程序，它使用 Bing 图像搜索根据搜索词将图像抓取到本地目录。然后使用抓取的图像通过 Azure 认知服务创建一个定制的视觉模型。

#### 先决条件

*   [节点 j](https://nodejs.org/en/download/)
*   [vs 代码](https://code.visualstudio.com/download)

### 首先创建一个 Azure 帐户(如果你还没有的话)

[点击此处创建您的帐户](https://azure.microsoft.com/free/?WT.mc_id=blog-devto-casiljan)

### 接下来，我们将创建获取数据集所需的阿炳搜索 Api 资源

*   前往[portal.azure.com](https://portal.azure.com/)
*   选择“创建资源”
*   选择“AI +机器学习”
*   选择“必应搜索 v7”
*   完成必填字段
*   选择“创建”
*   当部署成功时，您会在右上角得到一个通知。
*   选择“转到资源”
*   从左侧导航中为资源选择“Keys”。
*   复制订阅密钥并将其粘贴到脚本中。

[认知服务必应搜索文档](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-web-search?WT.mc_id=blog-devto-casiljan)

### 图片刮刀使用 Bing 搜索认知服务

我们将使用此服务的图像搜索功能，但是您可以使用相同的端点来搜索网页、新闻、图像、视频和实体。要使用我们在 Azure 中创建的 Bing 搜索认知服务资源运行 Image Scraper 脚本，请遵循以下步骤。

克隆 repo，安装软件包，并在 VS 代码中打开

```
git clone https://github.com/cassieview/Use-ML-To-Classify-Yoga-Poses.git
cd Use-ML-To-Classify-Yoga-Poses
npm install code . 
```

Enter fullscreen mode Exit fullscreen mode

将密钥添加到 app.js 脚本中`javascript const subscriptionKey = 'addyourkeyhere'`
运行脚本`node app.js`

当脚本完成时，你将有一个名为`poses`的新文件夹，其中包含搜索词集合中指定的每个瑜伽姿势的子文件夹。在每个姿势文件夹中会有一个`test`和`train`文件夹。数据分成大约 70%用于训练，30%用于测试。

### 现在我们有了一个数据集，让我们创建自定义视觉 API 资源来训练一个能够识别瑜伽姿势的模型

*   前往[portal.azure.com](https://portal.azure.com/)
*   选择“创建资源”
*   搜索“自定义视觉”
*   选择“创建”
*   完成必填字段
*   选择“创建”
*   当部署成功时，您会在右上角得到一个通知。
*   选择“转到资源”

#### 创建自定义视觉项目和训练模型

*   选择“快速启动”
*   选择“自定义视觉门户”
*   选择“登录”
*   选择“新项目”
    *   输入名称和资源组
    *   项目类型:分类
    *   分类类型:多类常规
    *   域:常规
*   单击创建项目
*   现在我们需要添加和标记图像
    *   选择“添加图像”并导航至抓取图像的训练文件夹
    *   Ctrl + A 选择文件夹中的所有图像
    *   选择“打开”
    *   为照片添加标签，以表明瑜伽姿势
    *   选择“上传照片”
    *   对每个瑜伽体式重复这些步骤，因为每个瑜伽体式在我们的分类模型中都是一个类别。
    *   回顾每堂课，看看图片是否正确。有一些返回的图像不是不正确的就是不好的例子。如果你没有好的数据，你就不会得到好的结果。检查并更新数据，以便每个姿势的分类数据是一个良好、清晰的数据集。
*   所有数据上传并标记后，选择“Train”
*   他们的 API 将提供一些关于你的模型执行情况的统计数据。使用这是一个指示，但不是绝对的真理。使用 Test 文件夹中 scarped 图像的数据测试您的模型。在看不见的数据上测试你的模型是很重要的。
*   上传测试图像和测试模型的步骤。

    *   从右上角选择“快速测试”
    *   浏览到本地文件，并从测试文件夹中选择一个图像
    *   预测将出现在窗口的右侧
*   一旦你测试了你的模型，觉得你有一个好的数据集。是时候实施选定的模式了。有了 Azure 端点，这非常简单。不需要部署任何东西。在接下来的步骤中，我将向您展示如何获取 URL 来将您的模型集成到您的项目中。

### 获取预测 Url

*   从自定义视觉门户的顶部导航中选择“性能”
*   选择“预测 Url”
*   复制 url 以使用图像 Url 或图像文件
*   此外，通过选择右上角的设置图标，您将看到调用端点的按键和 URL。

### 消费某 App 中的 API

一旦模型经过训练，达到令人满意的精度，就可以在你的应用程序中使用了

*   转到包含自定义 Vision 资源的资源组
*   应该有一个名为“YourCustomVisionResourceName _ Prediction-Quick start”的资源
*   从有用链接列表中选择“Api 参考”
*   这将打开一个页面，上面有关于如何调用 api 从模型中获得预测的文档
*   如果你一直读到最下面，有多种语言的代码示例可以帮助你入门

### 我喜欢用 Postman 测试 API。这里是步骤，所以使用邮递员

*   如上所述获取预测 url
*   从下拉列表中选择“Post ”,然后粘贴到 url 中
*   转到设置并获得预测键
*   在标题中添加`Prediction-key`作为键，添加实际的键作为值
*   Auth 应设置为`no auth`
*   选择“正文> Raw > Json”
*   将下面的 Json 粘贴到请求体中

`{
"Url": "UrlForImageGoesHere"
}`

*   发送请求

同样的逻辑可以应用于各种图像分类问题。如果您有任何问题或意见，请在下面留下。谢谢！

## 有用的文档链接

[快速入门:使用 Bing Image Search SDK for node . js](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-image-search/image-search-sdk-node-quickstart?WT.mc_id=devto-blog-casiljan)
[快速入门:使用自定义视觉 Node.js SDK](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/node-tutorial?WT.mc_id=devto-blog-casiljan)
[认知服务 Bing Search Docs](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-web-search?WT.mc_id=hackdemo-talk-casiljan)
[认知服务自定义视觉 Docs](https://docs.microsoft.com/en-us/azure/cognitive-services/custom-vision-service/home?WT.mc_id=devto-blog-casiljan)
[在 Linux 上的 Azure App Service 中创建 node . js App](https://docs.microsoft.com/en-us/azure/app-service/containers/quickstart-nodejs?WT.mc_id=hackdemo-talk-casiljan)