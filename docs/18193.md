# 介绍 MAX—模型资产交换

> 原文：<https://dev.to/lidderupk/introducing-max-model-asset-exchange-4m7k>

[![](img/ccdbbc39c973b10695a40a14a8d6295a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--TM7TkaIV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AE99n0jgH4eL9rpfyFELN5Q.png)

***数据科学讲的是学习*** 。我不是指监督学习，非监督学习，强化学习或者迁移学习！我是说 ***学习*** ！从书上。来自代码。从你的同事和朋友那里。来自 stackoverflow！你在不断学习和提高你的技能。新的模式和突破正以比以往更快的速度被宣布。作为数据科学家，你需要了解这个领域正在发生的事情，并掌握最新的发展动态。IBM 有几个我想写的开源项目，可以帮助您跟上时代的步伐。在本帖中，我们将关注 MAX 或模型资产交换。

[![](img/f28bdd66afded6384edc5a3fc0755e17.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i4Iv6hyk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/490/1%2AytBdCoJZcGBZ-igTzjvdsw.gif) 

<figcaption>学习</figcaption>

MAX 是我在 IBM 的同事和朋友开发的开源机器学习和深度学习模型的仓库。它们包括从物体识别、姿势检测器、音频分析和年龄估计器的主题。这个团队不时推出一种新型号。这有什么大不了的？

> 您可以克隆、派生、改进、提供反馈并从这些模型中学习！你学着点！你要学会如何自己制作一个并使之可用！你知道为什么它工作得很好。你会学到为什么有些不能很好地工作，更重要的是为什么它不能与某些输入一起工作。

我亲爱的朋友和同事 Patrick Titzler 最近在旧金山做了一个关于 MAX 的演讲，向我们展示了如何使用 Node-RED 快速测试其中一些模型。我在同一个聚会上遇到了[阿尤什](https://www.instagram.com/throughshutter/)。Ayush 用 MAX 的两个模特来促进他的日常爱好——摄影。我将把它留到以后的另一篇文章中。来看看我最喜欢的 MAX 机型…

#### [物体探测器](https://developer.ibm.com/exchanges/models/all/max-object-detector?cm_mmc=OSocial_Blog-_-Developer_IBM+Developer-_-WW_WW-_-ibmdev-OInfluencer-Medium-USL-ibm-max-object-detector&cm_mmca1=000037FD&cm_mmca2=10010797)

该模型从 COCO 数据集中的 80 个不同的高级对象类别中识别图像中存在的对象。这个问题已经解决了大部分，如果您使用的框架/平台不能很好地处理默认结果，请尝试使用 Watson 视觉识别创建一个自定义分类器(插入 url)。作为一个例子，我给了领先的视觉识别 API 一个 Raspberry Pi 的图像，它们大多返回“计算机芯片”、“主板”或“电子”。这些都是惊人的结果，但我想要更多。我能够使用 Watson 训练一个自定义分类器，非常自信地告诉我照片是“A 型树莓 Pi”还是“B 型树莓 Pi”😳。很酷的东西！你可以在这篇[媒体文章](https://dev.to/lidderupk/watson-visual-recognition--custom-models-15go)中了解客户分类器。也就是说，这个开源模型仍然是一个很好的学习资源。你也可以在 Node-RED 中尝试这些模式，在下一篇文章中会有更多关于这个选项的内容！很酷的是，它得到的自行车只是图片中可见的东西！自行车倒挂在我的房间里🆒！

[![](img/7506d0fa57f6f3e7f2539b0a698f03ed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZvI9lTcz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVIdcctKY4glS0IEWGMQJ4g.png) 

<figcaption>节点-红色为 MAX</figcaption>

#### [面部年龄估计器](https://developer.ibm.com/exchanges/models/all/max-facial-age-estimator?cm_mmc=OSocial_Blog-_-Developer_IBM+Developer-_-WW_WW-_-ibmdev-OInfluencer-Medium-USL-ibm-max-age-estimator&cm_mmca1=000037FD&cm_mmca2=10010797)

该模型检测图像中的人脸，提取每个检测到的人脸的面部特征，并最终预测每个人脸的年龄。

这是我迄今为止最喜欢的，因为它每次都让我变得更年轻:)。起初，我很恼火 IBM 会推出一个不像我预期的那样好的模型。有时我刮胡子，有时我留胡茬。它给了我一个不同的结果，在日光下和昏暗的会议厅里。但后来我意识到，这些对我来说都是学习的机会。我们需要问每个模型的问题。我开始查看训练数据，很快意识到，除非我在好莱坞，化了五磅的妆，否则它无法猜出我的年龄。训练数据是 IMDB imageset，您可以在这里找到[。我们将在下一篇博文中仔细研究这个数据集。](https://developer.ibm.com/exchanges/models/all/max-facial-age-estimator?cm_mmc=OSocial_Blog-_-Developer_IBM+Developer-_-WW_WW-_-ibmdev-OInfluencer-Medium-USL-ibm-max-age-estimator&cm_mmca1=000037FD&cm_mmca2=10010797)

[IMDB-WIKI - 500k+带有年龄和性别标签的人脸图像](https://data.vision.ee.ethz.ch/cvl/rrothe/imdb-wiki/)

我们现在有了一个 [IBM 代码模式](https://developer.ibm.com/patterns/estimate-ages-for-detected-human-faces?cm_mmc=OSocial_Blog-_-Developer_IBM+Developer-_-WW_WW-_-ibmdev-OInfluencer-Medium-USL-max-estimate-age&cm_mmca1=000037FD&cm_mmca2=10010797)，它将帮助您创建一个使用该模型的 web 应用程序！这些世界领导人太年轻了！

[![](img/4ed20ab59449d5bcc50c67bcc3f4a7e4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vSoHqZDq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Anos_L2nOT37mr1snpxLh7Q.png)

<figcaption>G7 领导人</figcaption>

它与我的形象相当好，只有两个…我会让你猜在哪个方向。

<figcaption>上党领袖</figcaption>

#### [图像字幕生成器](https://developer.ibm.com/exchanges/models/all/max-image-caption-generator?cm_mmc=OSocial_Blog-_-Developer_IBM+Developer-_-WW_WW-_-ibmdev-OInfluencer-Medium-USL-ibm-max-image-caption&cm_mmca1=000037FD&cm_mmca2=10010797)

这是 Ayush 用来为他的 Instagram 帐户生成标题的模型。对了，这里可以跟着他:[https://www.instagram.com/throughshutter](https://www.instagram.com/throughshutter/)

该模型从描述 COCO 数据集中图像内容的固定词汇表中生成标题。即使训练数据集是有限的(80 个对象类别)，它做得相当好。这里有一个例子

[![](img/fdf8a63215fa2f01686d38f6bb7279e7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xyqgSzJk--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/950/1%2ASqfWTFfphEaOn2SjU4Y4AA.png) 

<figcaption>***一个西装革履的男人在打手机***</figcaption>

> ***一个西装革履的男人在打手机***

字幕上写着 ***一个穿西装的男人在打手机。*** 那一点也不差！MAX 身上还有很多其他型号可以试穿。但是让我们看看如何使用 Node-RED 来快速测试其中的一些。MAX 团队创造了一些资产来帮助我们。如果你以前从未使用过 Node-RED，那就做好恋爱的准备吧💕。

#### [音频分类器](https://ibm.biz/BdzPh9)

这个太有趣了。*该模型识别签名的 16 位 PCM wav 文件作为输入，生成嵌入，应用* [*PCA 变换/量化*](https://github.com/tensorflow/models/tree/master/research/audioset#output-embeddings) *，使用嵌入作为多注意力分类器的输入，并输出前 5 个类别预测和概率作为输出。什么🤯！！基本上，它识别声音和噪音。数据来自[音频组](https://research.google.com/audioset/ontology/index.html)。我正在构建一些很酷的演示和游戏来使用这个模型。敬请期待！*

[![](img/6ce197738aba82d5f053fabf8861133e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7Xiw-oNQ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Am7St2oLWP1Gm2HuZoOPtQg.png)

#### [人体姿态估计器](https://ibm.biz/BdzPhC)

在最近的一次数据科学研讨会上，我从中获得了很多乐趣！该模型检测给定图像中的人类及其姿态。该模型首先检测输入图像中的人，然后识别身体部位，包括鼻子、脖子、眼睛、肩膀、肘部、手腕、臀部、膝盖和脚踝。接下来，每对相关联的身体部位由姿势线连接。

[![](img/bea640f1d67e806b85f080f794965c64.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VZ1jxN7F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Ax69Jto9UfnwN9MFk_fnCzw.png) 

<figcaption>人体姿态估计器</figcaption>

这些是我最喜欢的一些模型。在模型资产交换中还有更多。[试试看，让我知道你喜欢哪一款](https://ibm.biz/BdzPhV)！我还有一些关于如何使用/部署[其中一些型号](https://ibm.biz/BdzPhV)的帖子。

[Max Katz](https://medium.com/u/bf01a11701fe) ，提前抱歉混淆了:)。我会单独写个帖子给你介绍！ [Patrick Titzler](https://medium.com/u/df9e907db0b1) ，谢谢你的精彩见面会！ [Gabriela de Queiroz](https://medium.com/u/a66cd0d81fb9) 和 Simon，感谢你们在旧金山的 IBM AI-ML 峰会[上演示 MAX。](https://aiml-developer-summit.splashthat.com/)