# 有损压缩与无损压缩，面向非视频工程师

> 原文：<https://dev.to/missamarakay/lossy-vs-lossless-compression-for-non-video-engineers-59ii>

我让一些人去询问关于有损和无损压缩的更多信息，特别是与视频和视频编解码器相关的信息。

我承认，我以前在处理语音到文本的音频文件时遇到过这个问题(Opus，如果你是从浏览器捕获的，在一个 webm 容器中)，但是我学到了获得结果的最起码的东西。在这一点上，我可能只是一个视频最低限度以上的设置。

这不会是你对视频压缩最全面的观察，因为有其他资源可能做得更好。

让我们从基础开始。

## 压缩 101

您遇到压缩的最常见方式之一是将巨大的 powerpoint 演示文稿打包成 zip 文件。

通过 voodoo 电脑魔术，你的演示文稿从巨大变得几乎可以管理，你可以把它作为电子邮件附件发送。接收端的人可以下载压缩文件，解压缩(或解压)，他们就能得到你的演示文稿。

这是你的大脑。
[![Overhead view of parking lot with gaps between vehicles.](img/df0ee4d7c851c2098130fb4e0921f3f6.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--LfnLIPUe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xdfbyoswfdl18zxi6mjc.jpg)

这是你受压的大脑。
[![Overhead view of parked vehicles with no space in between, triple parked.](img/1504830f2bcd031fce72b8063ae1b5cf.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--frBtDbjy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fehq831xecunyftx7rly.jpg)

开玩笑但不尽然。我们去掉了多余的信息，车辆之间的空间。不管怎样，这是个比喻。使您的文件更小，更容易通过网络发送您真正想要传达的信息。

## 无损压缩

在我继续寻找图像隐喻之前，我提到的巫毒计算机魔法实际上是一套算法，也称为编码方法或*编解码器*，它识别出你可以使用*引用而不是重复*的地方。如果你接受过正规的计算机科学教育，记得回到你的“按值传递”和“按引用传递”的日子。参考更便宜，因为我们总是可以参考相同值的位置，即使你可能在 3 张幻灯片上有相同的图表，因为你试图证明一个观点。

这种方法保留了文件中的所有原始信息，被认为是无损压缩——在压缩过程中我们没有丢失任何信息。

语言和书面文本很容易压缩，因为它们充满了模式。虽然美式英语很乱，但我保证它还是有模式的。

## 有损压缩

问题是，并不是所有的东西都是基于文本的，我们的 powerpoint 例子可能不是最好的，主要是因为它可能充满了图像、gif 和其他非文本项目。

图像、音频文件、视频——它们的模式都少得多。因此，我们的算法必须做出一些选择、假设，去除相似或无关的信息。

这会导致文件被更改，在此过程中可能会丢失信息。你真的把信息扔掉了，为什么你还想把它找回来？

现在，理论上，我们的算法可以很好地压缩像图像这样的特定类型的文件，但你可能会看到颜色的问题，因为算法为你所有的绿草或蓝天选择了一个单一的值。

我们想要一个更小的文件，但我们也想确保我们不会以明显的方式降低体验。

## 但是我想要高质量的流媒体视频

你当然知道，这就是为什么你要用有损压缩。

我们需要一个有损编解码器，它能挑选出正确的内容扔掉，并且不会降低您的体验。这是很难的，当一个东西对计算机来说很难的时候，它会很慢。

编码或解码，字面意思是您的编解码器正在做的工作，是视频过程中最慢的部分，您需要决定在此过程中选择什么来权衡。比特率较低的编解码器可能会有更多的失真，这对您来说可能没问题。

## 更多资源

如果你想了解更多关于视频编解码器、压缩和编码的信息，我在下面提供了一些链接。请记住，编解码器是进行编码或解码的软件或硬件。有两种压缩编解码器-有损和无损。

列表，如果你喜欢的话:

*   [https://en.wikipedia.org/wiki/Comparison_of_video_codecs](https://en.wikipedia.org/wiki/Comparison_of_video_codecs)
*   [https://en.wikipedia.org/wiki/List_of_open-source_codecs](https://en.wikipedia.org/wiki/List_of_open-source_codecs)

概述，甚至可能带有图形:

*   [https://www.wowza.com/blog/video-codecs-encoding](https://www.wowza.com/blog/video-codecs-encoding)
*   [https://video . IBM . com/blog/streaming-video-tips/what-is-video-encoding-codecs-compression-techniques/](https://video.ibm.com/blog/streaming-video-tips/what-is-video-encoding-codecs-compression-techniques/)
*   [https://mux.com/articles/what-is-video-encoding/](https://mux.com/articles/what-is-video-encoding/)