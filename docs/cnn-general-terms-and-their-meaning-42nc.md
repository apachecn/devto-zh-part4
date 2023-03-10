# CNN:通用术语及其含义

> 原文：<https://dev.to/jai00271/cnn-general-terms-and-their-meaning-42nc>

本博客是以下内容的延续:

[![jai00271 image](img/6034a87a88b344e7ffb52fdcb4ed0b15.png)](/jai00271) [## 卷积神经网络:背景与基础

### 斋浦尔 28 ' 194 分钟读取

#dnn #cnn #ai #machinelearning](/jai00271/background-basics-21bl)
After publishing the above blog, I received feedback on sharing some basic concepts of CNN. So I tried listing general concepts used in CNN.

### 建筑基础

1.  神经网络的层数如何决定？

1.  我们是在不增加模型开销的情况下走向全局极小值吗？​
2.  我们的模型在时间和内存消耗方面是否有效和优化。

3.  解释 MaxPooling？

让我们把问题分成两部分:

1.  什么是 MaxPooling？

当我们必须在不损失图像中重要特征的情况下减少层数时，使用最大池概念。

3.  我们为什么要使用它们？

当我们想要减少网络中的参数时，随着网络的每层增加，我们增加了参数，并且我们的网络在计算上变得昂贵。如果我们在 400x400 的图像上使用 MP，并使用 2x2 矩阵来缩小图像，其大小将缩小到 200x200，因此与卷积相比减少了许多层。

6.  什么是 1x1 卷积？

因此，我们通常按照 32、64、128、512、1024、2048 的顺序增加通道，并在这里重置为 32，然后重新开始。但这种方法的问题是，我们在合并 512 个频道后形成的这 32 个复杂而丰富的频道需要删除一些对我们的网络无用的功能。如果我们使用 3x3 来执行从 512 通道到 32 通道的通道减少，它将重新评估并给出新的通道，但使用 1x1，它将组合 512 通道并给出 32 通道，这不会让背景等噪声继续传播。一个例子是，如果你的输入图像是一张脸，3x3 将分别获取两只眼睛，而 1x1 将获取两只眼睛，说明它们总是一起出现。因此，当我们想要减少通道数量时，我们将使用 1x1 而不是 3x3。还有一点要记住，1x1 的计算开销非常小，因为它一次只能看到 1 个 o/p 内核 x n 个通道，而不是 9 个 o/p 内核 x n 个通道块。查看下图，更好地理解它。
[![alt text](img/6d3c8567b629291220d297226e3c5f6e.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s---TacWC5f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AHO0_VnNxAYE4k4dblpYzQA.png)

8.  什么是 3x3 卷积？

图像分多个步骤进行处理，以提取边缘、纹理、梯度、对象部分和对象本身。现在，从第一步进入下一步，我们需要以这样一种方式处理图像，即它们从上一层提取特征，就像我们随着年龄的增长，在做出任何决定之前如何利用我们的经验一样。(阅读我的第一篇[博客](https://dev.to/jai00271/background-basics-21bl)以获得更好的理解)

10.  什么是感受野？

所以当我们对一幅图像进行卷积时，我们将图像缩小了 2 维。例如，一个 5x5 的图像经过一次 3x3 的卷积后，大小将变为 3x3。现在，如果我们再次对这个 3x3 图像进行卷积，结果将是 1x1。现在使用这张 1x1 图像，我可以看到我的 5x5 图像。让我们看看下面的图片，把它们想象成一扇窗户。如果您站在最后一层(即第 3 层)之后，我们打开 1x1 窗口，您可以看到 9 像素的 3x3，如果我现在打开 3x3 窗口，您可以看到 25 像素的 5x5。因此，这里层 2 的局部感受野是 3(从 1 个像素可见的矩阵的大小)，而层 3 的局部感受野是 5(可见的矩阵的大小)

[![alt text](img/6d38c6d9ba8878d066128336e2e2a98a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QmLI_O3h--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.researchgate.net/publication/316950618/figure/fig4/AS:495826810007552%401495225731123/The-receptive-field-of-each-convolution-layer-with-a-3-3-kernel-The-green-area-marks.png)

13.  什么是 SoftMax？

Softmax 为多类问题中的每一类分配小数概率。所有概率的总和必须是 1。Softmax 就在输出层之前实现。Softmax 图层必须具有与输出图层相同的节点数。

[![alt text](img/5b061b6f179a94013ed05847f460b323.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tPODZH83--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2A670CdxchunD-yAuUWdI7Bw.png)

16.  什么是学习率？

LR 决定了我们向全局/局部最小值移动的速度。较小的值导致向最小值的小步前进，导致过程中的过度延迟，而较大的值意味着大步前进，您可能会过冲，永远达不到最小值。

[![alt text](img/9b21f5a9480d21c8f8076ff85c47fec2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5fkyAYDR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://cdn-images-1.medium.com/max/1600/0%2AD7zG46WrdKx54pbU.gif)

19.  什么是内核，我们如何决定内核的数量？

谈到像 1x1、3x3、11x11 这样的内核，我们有多种选择。但是我们更喜欢 3x3 内核，因为它已经成为整个行业的标准(阅读我的第一个[博客](https://dev.to/jai00271/background-basics-21bl)以获得更好的理解)。

21.  什么是批量正常化？

批量标准化通过标准化层的输出来修复神经网络中的协变量移位(特征的分布在训练/测试数据的不同部分中是不同的)。

[![alt text](img/fe5072c35af8d49807b7f9f14114cb6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WpsTUduX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2ArXY5zJrDdHv6EdKhJvKqcA.png)

神经网络通过在反向传播期间校正其权重和偏差来学习。现在，起始层中的微小变化将在接下来的层中引起连锁反应，这导致训练中的延迟，因为整个网络需要再次训练。
标准化是一个将所有人放在同一尺度上进行更好计算的概念。例如，如果有人问你人们是如何参加今天的会议的，你可以回答说大约 10 个人，而不是说 5 个男人，5 个女人参加了。
批量规格化在各层之间增加一个规格化“层”。必须通过“小批量”对每个输入神经元分别进行标准化，而不是对所有维度都进行标准化。

[![alt text](img/f10944da1600cb02925a16f828b02911.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EJe1lgfu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2000/1%2AWRio7MD4JDeLww-CyrxEbg.png)

26.  什么是图像归一化？

当我们处理图像时，我们总是得不到我们想要训练模型的数据。很多时候，我们不得不即兴发挥，对输入图像进行修改，这就是所谓的标准化。所以根据 wiki 的说法，归一化是一个改变像素亮度值范围的过程。例如，应用包括由于眩光导致对比度差的照片。标准化有时称为对比度或直方图拉伸。在更一般的数据处理领域，如数字信号处理，它被称为动态范围扩展。在各种应用中，动态范围扩展的目的通常是将图像或其他类型的信号带入感官更熟悉或更正常的范围，因此称为归一化。

28.  MaxPooling 的理想定位是什么？

在 2 个卷积层之后，Maxpooling 是首选最小值。MP 有助于减少网络中的参数数量。

30.  过渡层的概念是什么，过渡层的位置是什么？

过渡层是[卷积+池化]的组合，它只是一种在我们从 512x512 移动到 256x256 再到 128x128 时对密集块计算的表示进行向下采样的方式。因此，简而言之，降低/增加模型数学复杂性的决策发生在过渡层。

[![alt text](img/cc26bf53c376fa5ad0db444460cb30f9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B0nxP56V--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.stack.imgur.com/cSwqp.png)

33.  什么是历元数，何时增加？

神经网络将看到整个数据集的次数称为时期。例如，如果神经网络在 60000 个数据集上运行 10 次，你会说我的纪元是 10。当我们训练我们的模型时，我们指定纪元，例如(这里纪元是 10):

```
model.fit(X_train, Y_train, nb_epoch=10) 
```

36.  什么是退学？

我们希望我们的神经网络是有效的，并且是知道你的神经网络是否已经学习了正确的特征并且没有记住数据的最佳方式，然后我们移除一些神经元并再次运行网络。如果结果很好，这意味着我们的网络训练有素，如果你的网络性能下降，这意味着一些神经元学习了错误的特征/记忆的数据，并在模型做出决定时规定了条款。因此，总而言之，dropout 是一种用于减少神经网络中过拟合的正则化技术。

38.  什么时候我们引入了辍学，或者什么时候我们知道我们有一些过度适应？

当我们的模型在训练数据上表现良好，但在测试/真实数据上表现不佳时，我们知道我们的模型过拟合。你的网络中的一些神经元可能已经学习了一些特征，这些特征可能是图像识别中的决定性因素。因此，你的整个网络依赖于一个错误的特定神经元。对于 dropout，我们从网络中丢弃一些随机的神经元，并重新训练网络，以便所有的神经元都得到同等的良好训练。

40.  我们什么时候停止卷积，转而使用更大的内核或其他替代方案(我们还没有介绍过)

当我们处理高质量图像时，提取特征在计算上非常昂贵，我们不能用正常的 3x3 卷积处理图像，或者在这种情况下，我们执行最大池。

42.  相对而言，我们如何很早就知道我们的网络不太好？

当我们在第一个纪元后的结果与目标值相差甚远时，我们知道我们开始错了，需要在重新训练前改变。让我们考虑一下这个例子。如果我们的第一个纪元的结果是大约 92%,我们的目标是 99.5%,那么我们肯定不会在 n 个纪元后达到 99.5%,因为我们从一开始就向前迈出了错误的一步。

44.  什么是批量，批量的影响是什么？

老师说你全年将有 6 次 1 小时的考试，每次考试有 30 个问题。这里 6 是纪元，30 是批量大小。现在考试是一个小时，如果老师给你 120 个问题来写，你能做到吗？这可能需要你用双手书写:)让我们在神经网络中使用相同的概念。批量越大，需要的内存越多，但处理时间越短，而批量越小，需要的内存越少，但处理时间越长。

46.  何时添加验证检查？

有没有想过为什么我们在期末考试前有这么多考试？它帮助你弄清楚你是否在正确的轨道上。当我们在测试中得分较少时，我们知道这里有问题，我们需要在这方面/这一层(神经网络)进行改进。验证检查与此类似，我们需要在每个历元运行后了解我们模型的准确性，而不是在完成训练后最终了解它，并等待完整的训练来了解准确性。

48.  LR 时间表及其背后的概念？

你的学习速度决定了你想多快达到最小值。如果选择较小的值，则该过程可能需要更多的时间来达到最小值，而如果选择较大的值，则可能会过冲，并且永远不会达到最小值。

[![alt text](img/eb21edb4a97db62dcbdeb1b544bcd82c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pxBPQFdg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.jeremyjordan.me/conteimg/2018/02/Screen-Shot-2018-02-24-at-11.47.09-AM.png)

51.  Adam 和 SGD 的区别是什么？

梯度下降又名批量梯度下降，是优化深度学习网络最常用的方法。根据白皮书:

```
Gradient descent is a way to minimize an objective function J(θ) parameterized by a model’s
parameters θ ∈ (R^d) by updating the parameters in the opposite direction of the gradient of the objective function ∇θJ(θ) w.r.t. to the parameters. The learning rate η determines the size of the steps we take to reach a (local) minimum. In other words, we follow the direction of the slope of the surface created by the objective function downhill until we reach a valley

In code, batch gradient descent looks something like this:
for i in range ( nb_epochs ):
    params_grad = evaluate_gradient ( loss_function , data , params )
    params = params - learning_rate * params_grad 
```

SGD 是梯度下降的方差。与 GD 不同，SGD 为每个训练示例*执行参数更新。*

```
for i in range ( nb_epochs ):
np.random.shuffle ( data )
  for example in data :
     params_grad = evaluate_gradient ( loss_function , example , params )
     params = params - learning_rate * params_grad 
```

新币波动:
[![alt text](img/d43c9643c47eae19031d332f521a1101.png)](https://camo.githubusercontent.com/7cafed0adfef0d6261bbd7cf1b66bdc66a707cbb/68747470733a2f2f75706c6f61642e77696b696d656469612e6f72672f77696b6970656469612f636f6d6d6f6e732f662f66332f53746f6772612e706e67)

最近许多新的优化程序变得很有名，其中一个这样的优化程序是亚当。自适应矩估计(Adam)是计算每个参数自适应学习率的另一种方法。它比 SGD 更快地达到最小值，并且在内存消耗方面也是高效的。[阅读此处了解更多信息](https://stats.stackexchange.com/questions/220494/how-does-the-adam-method-of-stochastic-gradient-descent-work/220563#220563)

[SGD 白皮书](https://arxiv.org/abs/1609.04747)

图片来源:
[1x1 卷积](https://cdn-images-1.medium.com/max/800/1*HO0_VnNxAYE4k4dblpYzQA.png)
[感受野](https://www.researchgate.net/publication/316950618/figure/fig4/AS:495826810007552@1495225731123/The-receptive-field-of-each-convolution-layer-with-a-3-3-kernel-The-green-area-marks.png)
[Softmax](https://cdn-images-1.medium.com/max/2000/1*670CdxchunD-yAuUWdI7Bw.png)
[批量归一化](https://cdn-images-1.medium.com/max/2000/1*WRio7MD4JDeLww-CyrxEbg.png)
[过渡层](https://i.stack.imgur.com/cSwqp.png)
[学习率](https://www.jeremyjordan.me/conteimg/2018/02/Screen-Shot-2018-02-24-at-11.47.09-AM.png)
[学习率](https://cdn-images-1.medium.com/max/1600/0*D7zG46WrdKx54pbU.gif)