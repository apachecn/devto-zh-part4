# 我的前两个深度学习项目

> 原文：<https://dev.to/schlende/my-first-two-deep-learning-projects-454p>

上周，我开始参加面向程序员的 fast.ai 实用机器学习课程。

我学会了如何在大约一个小时内建立一个最先进的图像分类器，这是惊人的！然后我开始尝试把我学到的东西应用到我自己的项目中。事情是这样的。

## 项目#1 - YouTube 缩略图分类器

我的第一个项目尝试是建立一个可以判断 YouTube 视频缩略图质量的分类器。

像这样的东西会很酷！想象一下，你可以为你新创建的 YouTube 视频插入一个缩略图，然后得到如何改进它的建议。

作为第一步，我试图建立一个分类器，可以识别良好的缩略图。

我收集了一堆 YouTube 缩略图，估算了这些视频每天的浏览量。我将这些视频标记为“每天不到 100 次浏览”*和“每天超过 100 次浏览”*，并试图看看最终的模型有多准确。**

 **[![image-20190613130523747](img/c5f5ad88f32d80281d83eba2d92092bb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FkmmUoZI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://walt.fyi/asseimg/2019-06-13-learning-about-deep-learning.assets/image-20190613130523747.png)

我有一种感觉，这个测试不会做得很好…除了缩略图之外，还有很多因素会影响浏览量(关注者数量 YouTuber 的知名度)，我没有错。演出令人沮丧。

该模型认为，这个缩略图将获得不到 100 次浏览。

[![image-20190613131252628](img/8e7b4e5922f60b772c78e03c35c2bacb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--06ySnXxK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://walt.fyi/asseimg/2019-06-13-learning-about-deep-learning.assets/image-20190613131252628.png)

这是有道理的……这可能是因为缩略图(虽然很重要)不会对浏览次数产生很大的影响。

老实说，我敢打赌，我可以做一个很好的模型来预测浏览量。只要回归一下订阅者的浏览量就可以了。这就是为什么 YouTube 专家总是说“喜欢并订阅”！

[![image-20190613131544511](img/8ba28059d675c3f75fbe442cef291a0b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pyeMM5iE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://walt.fyi/asseimg/2019-06-13-learning-about-deep-learning.assets/image-20190613131544511.png)

所以项目失败了？

绝对不行！这项工作(在这个阶段)的整个要点是弄清楚机器学习有什么能力+经历训练一个模型所需的步骤。我绝对做到了。

这个项目教会了我深度学习的机制，我开始更直观地感受到什么样的问题模型会成功或挣扎…在这种情况下，模型挣扎是因为它没有做出正确判断所需的信息。

## 项目#2 -中文语言检测器

我的第二个尝试是建立一个模型，可以听人们说话，并将声音分为“英语”或“汉语”。

为了做到这一点，我采用了音频文件(将音频数据存储为“时间序列”随时间变化的振幅)…并将它们随时间变化的振幅数据转换为频谱图(声音随时间变化的频率图片)。

这—>

[![image-20190613132931100](img/6d00d0e632f763d37d9b2189de4dc29f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0DWszvXb--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://walt.fyi/asseimg/2019-06-13-learning-about-deep-learning.assets/image-20190613132931100.png)

转换成这个-->

[![image-20190613133243349](img/68dcb68b50d7105e674f93fbeb58df69.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--UqETB0OF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://walt.fyi/asseimg/2019-06-13-learning-about-deep-learning.assets/image-20190613133243349.png)

假设是，机器学习模型将能够学习英文和中文音频文件之间的差异，并能够挑出哪个是哪个…

[![image-20190613133558444](img/06d200a9ca4acbbeec339bcb228a52fe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YwE9f9jL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://walt.fyi/asseimg/2019-06-13-learning-about-deep-learning.assets/image-20190613133558444.png)

模特在这个项目中的表现很有趣。它在对属于初始训练集的文件进行分类方面做得相当好。

[![image-20190613133824056](img/de5c20cb8721fa8f05038d306122634b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NW0sPU8p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://walt.fyi/asseimg/2019-06-13-learning-about-deep-learning.assets/image-20190613133824056.png)

但是当它实际上涉及到预测我说普通话的样本时，它是非常可怕的。

[![image-20190613134029227](img/97af7879e478d7251ca9223878a0c408.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--uwxmsAGs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://walt.fyi/asseimg/2019-06-13-learning-about-deep-learning.assets/image-20190613134029227.png)

基本上这个模型的训练和测试是…令人沮丧的。

有太多的变量会给这样的模型带来问题。

*   原始音频文件编码
*   音频文件是否干净
*   将音频文件处理成频谱图
*   模型选择了什么样的特征—(我是否有特定的声音特征，这意味着模型总是为我选择英语？？？)

这样的例子不胜枚举。

在做了一些研究后，我了解到 CNN(卷积神经网络)可能不是解决这类问题的最佳网络，因为图片中的数据是重复性的，而不是固定的。

我[发现一些研究人员成功地使用 RNNs](http://yerevann.github.io/2016/06/26/combining-cnn-and-rnn-for-spoken-language-identification/) (一种神经网络，网络基于来自先前数据的数据进行预测)而不是 CNN 解决了这个问题。这些网络很适合这类任务，因为网络会说…'鉴于我刚刚看到这个形状，下一个形状应该是…如果这是英国的话，等等'。

所以我觉得解决这个问题有点超出我现在的技能水平，但是我不会放弃的！我先把这个放一放，等我了解多一点的时候再拿起它。

那么我从这个项目中学到了什么呢？

*   很多关于信号处理和快速傅立叶变换的知识
*   当 CNN 被要求分类的模式不清晰、不明显时，他们会很纠结
*   如果我处理数据集和问题，在那里我可以看到其他人的答案，我可能会学得更快。)

## 退一步讲，为什么要学这个东西？

老实说，在使用这项技术一周后，我被震撼了！

我还不能确切地看到*这东西会如何改变世界，但我毫不怀疑它会改变……一切。*

 *我敢打赌，再多一点经验，我就能构建一些有用的工具。我们走着瞧。到目前为止，这确实很有趣，当我了解到更多的时候，我会让你知道的。***