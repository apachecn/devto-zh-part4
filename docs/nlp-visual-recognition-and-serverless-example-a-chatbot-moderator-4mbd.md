# NLP，视觉识别和无服务器示例——一个聊天机器人版主

> 原文：<https://dev.to/lidderupk/nlp-visual-recognition-and-serverless-example-a-chatbot-moderator-4mbd>

### NLP，视觉识别和无服务器示例——一个聊天机器人版主

[![](img/f31f38f903312cf6279e433739ddcac4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--exRKTGpw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/822/1%2ANuawfho5sJsKpxiCoDuoKg.png) 

<figcaption>IBM 团队举办网络研讨会</figcaption>

Lisa Jung 和我提交了一个关于创建聊天机器人版主的网络研讨会，该版主告诉人们在 Slack 上说一些粗鲁的话或发布适当的图片时要有礼貌。对于图像位，我们更改了代码，将任何狗图片视为粗鲁的图片，而不是寻找露骨的图片。

在这个演示中，我们使用了几种不同的 IBM 服务。

1.  我们用 [**IBM 自然语言理解**](https://ibm.biz/BdzmSV) 分析文本为**粗鲁**。你可以在这里找到更多信息[。](https://ibm.biz/BdzmSV)

[![](img/ddbdc43658f260258b914f68b88d88d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qaQ9nJnZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A5imqEiN5v4MgNGcBh4xqyg.png) 

<figcaption>使用 IBM 自然语言理解服务分析美联储银行的演讲</figcaption>

1.  我们使用 [**IBM 视觉识别服务**](https://ibm.biz/BdzmSJ) 来检测明确的图片(本例中是一只狗的图片)。在这里阅读更多。

[![](img/abc0cb09fbf0724f4d12c717841edc5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sy0Pcn9o--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AmvjfD-qKuFOMak_Vu4Hy9w.png) 

<figcaption>用 IBM 沃森视觉识别服务分析图像</figcaption>

1.  最后，我们使用[**IBM Cloud Functions**](https://ibm.biz/BdzmSA)作为无服务器平台，将所有东西粘合在一起。在这里了解更多。

[![](img/292d6f4bc5c46cdd4a1942632474c5be.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7Cv_ZOuR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2At1yS6L1qO-R836jW2DnWIw.png)

这是用例的流程:

1.  用户在松弛通道中输入文本或图像。
2.  Slack 将这些内容推送给 IBM Cloud Function action。
3.  如果输入是文本，该动作调用沃森自然语言理解服务来确定是否粗鲁。
4.  如果输入是一个图像，该动作调用 Watson 视觉识别服务来确定它对于通道是否是不安全的。
5.  如果输入是“坏的”，这个动作会移除图像，并告诉用户要更有礼貌。它通过在松弛通道中直接发布来做到这一点。

[![](img/596e3c949d9d84b8b6833687b0204567.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9J-WpFpt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2A15O6_eBtdHoxyhq81UGq8A.png) 

<figcaption>某聊天机器人版主</figcaption>

可以在 crowd cast . io[【https://www.crowdcast.io/e/a-moderator-using-nlp-vr】T2 上查看[完整录音。你可以在](https://www.crowdcast.io/e/a-moderator-using-nlp-vr) [**这个 github 资源库**](https://ibm.biz/BdzmSu) 中获得完整的代码并亲自试用。希望这很有趣！加入我们未来的网络研讨会、面对面会议和研讨会！

感谢 [Max Katz](https://medium.com/u/bf01a11701fe) 和 [IBM 开发者](https://medium.com/u/262975298e3a)的主持！