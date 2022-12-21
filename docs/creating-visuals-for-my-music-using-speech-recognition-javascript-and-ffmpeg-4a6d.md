# 使用语音识别、Javascript 和 ffmpeg 为音乐创建视觉效果:版本 0

> 原文：<https://dev.to/vbaknation/creating-visuals-for-my-music-using-speech-recognition-javascript-and-ffmpeg-4a6d>

你好！这是我在 dev.to 上的第一篇博文

我做音乐，我编码。

## 问题

推出音乐并获得关注，需要我身兼数职，完成各种任务:品牌推广、社交媒体营销、节拍制作、写歌、掌握音频、拍摄和编辑视频、设计图形，不胜枚举...

为了给我的音乐创建社交媒体视听内容，我通常遵循以下流程:

*   1)在 Garageband 中制作节拍
*   2)写歌词
*   3)练习歌曲
*   4)设置我的 DSLR 相机
*   5)设置我的麦克风
*   6)录制歌曲的视频
*   7)将视频导入 Adobe Premiere
*   8)将歌曲音频导入 Adobe Premiere
*   9)将音频与视频对齐
*   10)添加歌词(文本图形)并将其与音频对齐
*   11)给视频添加一些效果[我喜欢这个 80 年代的造型](https://adobemasters.net/how-to-create-an-80s-vintage-filter-in-adobe-premiere-pro-cc-2019/)
*   12)渲染视频(45 分钟到 1 小时)
*   13)导出到`.mp4`(再 30-40 分钟)
*   14)上传到 YouTube(另外 30-40 分钟)
*   15)上传到 IGTV(另外 30-40 分钟)

我想增加花在步骤 1 到 3 上的时间，减少花在步骤 4 到 15 上的时间。

## 灵感

上周日(2019 年 7 月 7 日),我在一个从 jQuery 到 Web APIs 的项目中重构了我的一些代码。随着我在 [MDN](https://developer.mozilla.org/en-US/) 呆的时间越来越长，事情就越来越多，我遇到了 WebRTC (Web 实时通信)标准和 YouTube LiveStream API 文档。这让我在谷歌上搜索关于音频和视频编解码器的信息。这终于让我找到了`ffmpeg`，一个用于音频和视频处理的开源软件。太好了-我可以从那里开始。

我过去很少使用这个软件，所以我花了几天时间尝试了一些不同的图像到视频的转换，以便学习基础知识。在这里，我用一整天拍摄的 338 张照片来转换经过附近的 BART(海湾地区快速交通)列车的延时:

[https://www.instagram.com/p/BzzM0xPBpoN/embed/captioned](https://www.instagram.com/p/BzzM0xPBpoN/embed/captioned)