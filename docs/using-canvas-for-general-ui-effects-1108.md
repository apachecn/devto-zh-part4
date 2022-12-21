# 🎩JavaScript 增强的 SCSS 混合！🎩-使用画布实现独特的用户界面特效

> 原文：<https://dev.to/adam_cyclones/using-canvas-for-general-ui-effects-1108>

更新，背景滤镜终于来了，用它代替这种方法或者基于这种想法设计一个 polyfil。

我花了很长时间来完善一个我很少见到的 UI 技术，但是结果可能会令人震惊，我想与你分享这个，所以希望我们可以得到更好的工具来做这种事情。

使用画布来生成漂亮图片的想法对我来说并不那么有吸引力，因为实际上我构建 ui 是为了实用。我看到了 canvas 的另一个潜力，它受到 css element()函数的启发，Firefox 多年来一直支持该函数。

可以从客户端截取整个文档的截图，并将其应用到画布上。好吧那又怎样，有什么大不了的？

请注意，这在移动 chrome 上会很好，因为改变背景位置是非常可怕的绘画。
[https://codepen.io/acronamy/embed/XLoVbR?height=600&default-tab=result&embed-version=2](https://codepen.io/acronamy/embed/XLoVbR?height=600&default-tab=result&embed-version=2)

好吧，怎么回事？通过分层和处理这个截图，可以创建各种各样的效果。我在代码笔的某个地方有麦克基尼扭曲效果。

那么问题出在哪里？尽管在 headless chrome 中已经存在，但没有原生的屏幕截图 API，所以需要使用 html2canvas，这意味着更多的重量，然后实验性的画布过滤器有点不工作，所以出现了 stackblur。

响应也很棘手，在我的例子中，我不想在每次调整大小时都截图，所以我选择了在调整大小后截图。

总之，我在等待 CSS 背景过滤器或画布过滤器和本机屏幕截图 API 或视频屏幕捕捉来拍摄视频，而不必选择输入。复合 UI 可能是一个有趣的挑战，去尝试一下吧。