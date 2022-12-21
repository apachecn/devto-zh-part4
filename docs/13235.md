# 使用语音识别、Javascript 和 ffmpeg 创建音乐视觉效果:版本 1

> 原文：<https://dev.to/vbaknation/creating-visuals-for-music-using-speech-recognition-javascript-and-ffmpeg-version-1-4mll>

这是我对我正在做的项目第一版的讨论，这个项目叫做 *animatemusic* 。

[点击这里进入它的 GitHub 库](https://github.com/vishalbakshi/animatemusic)。

我关于这个项目 0 版的博文[可以在这里找到](https://dev.to/vbaknation/creating-visuals-for-my-music-using-speech-recognition-javascript-and-ffmpeg-4a6d)。

## 毫秒，不是帧

在这个项目的版本 0 中，为了在画布元素(最终将成为视频帧)上呈现文本，我选择围绕一个基本问题进行设计:

正在渲染哪个视频帧？

这是一个合理的问题，因为我正在渲染有限的已知数量的帧(基于视频的帧速率和持续时间)。

然而，它最终有一个不太合理的解决方案，因为它涉及到要呈现的每组单词从 float(以秒为单位的开始时间)到 integer(帧号)的转换。这导致重复舍入，从而导致文本滞后于有声音频。以下是 codepen 阐明的困境:

[https://codepen.io/vishalbakshi/embed/orrRaE?height=600&default-tab=result&embed-version=2](https://codepen.io/vishalbakshi/embed/orrRaE?height=600&default-tab=result&embed-version=2)

对于版本 1，我选择避开这个问题，围绕一个新的基本问题进行设计:

这个词是什么时候呈现的？

## 使用`requestAnimationFrame`的例子

我找到了两个资源，让我确信我的新的基本问题是值得追求的:

*   [使用 requestAnimationFrame 控制帧速率](http://codetheory.in/controlling-the-frame-rate-with-requestanimationframe/)
*   [如何将 requestAnimationFrame 限制到特定的帧速率](https://stackoverflow.com/a/19772220/10244270)

我最终想使用 anime.js 或 three.js 之类的库，它们的文档和 API 也迎合了基于时间的动画方法。

## 重构和`generateFrameImages`

我借此机会重构了我的原始脚本，此外还添加了当视频的当前时间(过去的时间)在单词的`start`和`end`时间之内时在画布上呈现文本的函数。这里有一个 codesandbox 和[样例脚本 json 供上传](https://github.com/vishalbakshi/animatemusic/blob/master/20190623C_transcribed_test.json):

[https://codesandbox.io/embed/canvas-export-fnvws](https://codesandbox.io/embed/canvas-export-fnvws)

## 结果

当我向你们展示改进后的新视频时，我激动得发抖！！歌词与音频同步比版本 0 好得多！我看不出文本有多少问题(虽然有一些空白和一个`<unk>`，这是 gentle 版的`undefined`)

[https://www.youtube.com/embed/iujaWnphaY4](https://www.youtube.com/embed/iujaWnphaY4)

感谢阅读！如果你有什么可以分享的，请在下面评论，以帮助我改进这个项目。我要试着在肾上腺素激增中睡觉...

## 跟着我

[YouTube](https://www.youtube.com/channel/UCqvA0CVB3QR3TLpGVzkD35g)T2[insta gram](https://www.instagram.com/vbaknation)