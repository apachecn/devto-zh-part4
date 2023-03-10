# 云端无签名上传和人脸检测

> 原文：<https://dev.to/codingcatdev/cloudinary-unsigned-upload-and-face-detection-ige>

# 面部粉碎演示项目

我正在开发一个叫做 Face Smash 的 Ionic 工作室和 Cloudinary 演示，它允许你上传一张照片，然后被一个水球击中。

使用 [Cloudinary 上传 API](https://cloudinary.com/documentation/image_upload_api_reference) 用户可以上传一张照片，然后使用 [Cloudinary 的人脸检测](https://cloudinary.com/blog/face_detection_based_cropping)它会显示所有上传文件的列表和他们的脸(你的应该在顶部)，当你点击这个它会用水球打碎你的脸。

## 什么是离子工作室

一个强大的 IDE，为使用 Ionic 构建的团队提供流畅的开发体验。从单一工具开始，以最快、最简单的方式创建屡获殊荣的跨平台应用程序。

Ionic Studio 的原因由 Ionic 的首席执行官 Max Lynch 在博客文章[中完美地陈述:Ionic Studio:一种强大的构建应用的新方式](https://ionicframework.com/blog/announcing-ionic-studio-a-powerful-new-way-to-build-apps/)

今天就来看看 [Ionic Studio](https://ionicframework.com/studio) 吧！

## 什么是 Cloudinary

在 [JAMStack](https://jamstack.org/) 领域， [Cloudinary](https://cloudinary.com) 是提供全面的基于云的图像和视频管理平台的市场领导者。从小型创业公司到大型企业，全球有成千上万的用户正在使用 Cloudinary。在我看来，这是当今在网络上托管媒体的最佳解决方案！

## Cloudinary API 上传

### 图片上传 API

使用[图像上传 API](https://cloudinary.com/documentation/image_upload_api_reference) ，有几种不同的 SDK 可用于将图像上传到 Cloudinary。为了简单起见，我们将只对未签名的上传使用端点类型。我刚好有一张自己戴着梦寐以求的爱奥尼亚帽子的照片

[![Ionic hat](img/2e170da63dce02d431a75a8361de1c59.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uZxBVJxN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_400/v1565636611/AlexPics/alex_ionic_hat_inside.jpg)

在这个博客示例中，我们将使用`file`参数
来引用这张图片

```
file=https://res.cloudinary.com/ajonp/image/upload/v1565636611/AlexPics/alex_ionic_hat_inside.jpg 
```

Enter fullscreen mode Exit fullscreen mode

Cloudinary 将自动复制该文件，然后将其添加到 Cloudinaries 服务器上的新位置(请注意，虽然我使用的是 cloudinary 图像 url，但您可以使用任何 URL)。

在 AJonP 的应用程序中，我们将让用户拍摄一张照片，也将使用`file`参数引用该照片，但是它将向端点发送 Base64 编码的字符串。

请查看[日期上传选项](https://cloudinary.com/documentation/upload_images#data_upload_options)了解所有可用信息。

### 未签名上传

在我们的例子中，我们还必须使用`upload_preset`参数。这是因为我们使用的是未签名的上传。Unsigned upload 是一个直接从浏览器或移动应用程序执行上传的选项，不需要认证签名，也不需要通过您的服务器。但是，出于安全原因，在执行未签名的上载调用时，并非所有上载参数都可以直接指定。

正是因为这个原因，我不得不在我的[控制台](https://cloudinary.com/console/settings/upload)中创建一个新的上传预置。【T2![Upload Preset](img/2be17b5e4dfb9e28bd5b16296000aa6b.png)

该预设将所有新上传的照片放入文件夹 ajonp-ionic-cloud inary-face smash 文件夹中。[![preset folder](img/89c030943472c40f9ac0cfb30e5c86d9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YU-mB_pX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/v1565638259/ajonp-ajonp-com/blog/Screen_Shot_2019-08-12_at_3.30.49_PM.png)

现在当我们需要上传和触发这个预置操作时，我们可以将我们的参数设置为`upload_preset`

```
upload_preset=kuqm4xkg 
```

Enter fullscreen mode Exit fullscreen mode

### 端点上传

现在我们知道了所有这些工作的流程，让我们使用端点来完成实际的上传。

<video controls="controls"><source src="https://res.cloudinary.com/ajonp/video/upload/v1565651945/ajonp-ajonp-com/blog/cloudinary_api_endpoing_upload.webm" type="video/webm"> <source src="https://res.cloudinary.com/ajonp/video/upload/v1565651945/ajonp-ajonp-com/blog/cloudinary_api_endpoing_upload.mp4" type="video/mp4"></video> 

```
https://api.cloudinary.com/v1_1/ajonp/image/upload?file=https://res.cloudinary.com/ajonp/image/upload/v1565636611/AlexPics/alex_ionic_hat_inside.jpg&upload_preset=kuqm4xkg 
```

Enter fullscreen mode Exit fullscreen mode

一旦上传完毕，你应该会看到一些返回的 json 负载。

```
{
  "public_id": "ajonp-ionic-cloudinary-facesmash/raumizdelqrlows7pvzy",
  "version": 1565650174,
  "signature": "59905774f17ea06629ff90b73dfc9bed6c7fbdfd",
  "width": 2448,
  "height": 3264,
  "format": "jpg",
  "resource_type": "image",
  "created_at": "2019-08-12T22:49:34Z",
  "tags": [
    "facesmash",
    "face",
    "head",
    "chin",
    "selfie",
    "forehead",
    "photography",
    "fun"
  ],
  "pages": 1,
  "bytes": 1882858,
  "type": "upload",
  "etag": "4ae8ba0edfb90689101fdfbb8b97548d",
  "placeholder": false,
  "url": "http://res.cloudinary.com/ajonp/image/upload/v1565650174/ajonp-ionic-cloudinary-facesmash/raumizdelqrlows7pvzy.jpg",
  "secure_url": "https://res.cloudinary.com/ajonp/image/upload/v1565650174/ajonp-ionic-cloudinary-facesmash/raumizdelqrlows7pvzy.jpg",
  "access_mode": "public",
  "moderation": [
    {
      "response": { "moderation_labels": [] },
      "status": "approved",
      "kind": "aws_rek",
      "updated_at": "2019-08-12T22:49:36Z"
    }
  ],
  "info": {
    "categorization": {
      "google_tagging": {
        "status": "complete",
        "data": [
          { "tag": "Face", "confidence": 0.9635 },
          { "tag": "Head", "confidence": 0.9097 },
          { "tag": "Chin", "confidence": 0.8504 },
          { "tag": "Selfie", "confidence": 0.8183 },
          { "tag": "Forehead", "confidence": 0.7823 },
          { "tag": "Photography", "confidence": 0.738 },
          { "tag": "Fun", "confidence": 0.7039 },
          { "tag": "Headgear", "confidence": 0.6748 },
          { "tag": "Cap", "confidence": 0.6577 },
          { "tag": "T-shirt", "confidence": 0.5763 },
          { "tag": "Smile", "confidence": 0.5404 }
        ]
      }
    }
  },
  "faces": [[132, 906, 808, 1077]],
  "coordinates": { "faces": [[132, 906, 808, 1077]] },
  "original_filename": "alex_ionic_hat_inside"
} 
```

Enter fullscreen mode Exit fullscreen mode

这里的关键一行是，它实际上在坐标`"coordinates": { "faces": [[132, 906, 808, 1077]] },`中拾取了一张脸。仍然没有💯%还很完美。这里有一个行不通的例子。

[![Alex Ionic Hat Outside](img/5f91ac39949bfa327f48d71a7d540bd4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--nIn9SWQH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/ajonp/image/upload/w_200/v1565636611/AlexPics/alex_ionic_hat.jpg)T3】

```
"coordinates":{"faces":[]} 
```

Enter fullscreen mode Exit fullscreen mode

我希望与 [Cloudinary 团队](https://cloudinary.com/team)合作，看看我是否能以任何方式帮助提高检测率。

## 如何使用云状人脸检测

文档中有大量的信息涵盖了云人脸检测。

很简单，输入`g_face`参数将会给图像中最大的脸部位置增加“重力”,然后你可以开始做你认为合适的图像操作。在我们的示例程序中，我打算将所有的图片添加为缩略图。

这是我们的样本图片缩略图

```
http://res.cloudinary.com/ajonp/image/upload/w_1000,h_1000,c_crop,g_face,r_max/w_200/v1565650174/ajonp-ionic-cloudinary-facesmash/raumizdelqrlows7pvzy.jpg 
```

Enter fullscreen mode Exit fullscreen mode

[![Cropped Sample Thumbnail](img/35fc3bc411be629d59e98bb7c7395af9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--RsxnMnKs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/http://res.cloudinary.com/ajonp/image/upload/w_1000%2Ch_1000%2Cc_crop%2Cg_face%2Cr_max/w_200/v1565650174/ajonp-ionic-cloudinary-facesmash/raumizdelqrlows7pvzy.jpg)

## 可以理解的旁注

我不禁想起了社交网络的 [Facemash，我不想让他们混淆，这是一个非常有趣的项目，我们不会给任何人评分🤢！！我希望亚马逊的人工智能审核能抓住大部分不好的东西。如果局面失控，我会把它推倒。](https://youtu.be/VSKoVsHs_Ko)

所以我只是希望每个人都保持冷静，这个应用程序不会贬低任何人！我从来不想贬低任何人，也不想像扎克那样写一个 [facemash 道歉](https://www.thecrimson.com/article/2003/11/19/facemash-creator-survives-ad-board-the/)，所以我们将使用你可以自己上传的照片。