# 使用 nodejs 进行服务器端、数据驱动的动画和视频渲染。

> 原文：<https://dev.to/refine/server-side-data-driven-animation-video-rendering-with-nodejs-5e1g>

在这个演示中，我们创建了一个 nodejs 应用程序来实时生成天气预报视频。

动态天气数据以 JSON 的形式加载，并以动画的形式呈现在 web 画布上。使用 node-canvas 库和 ffmpeg，在服务器上渲染相同的画布动画，并导出为 mp4 视频文件。

2020 年 8 月 31 日编辑:

这个演示现在是一个完全工作的 nodejs 库。你可以从:[https://github.com/pankod/canvas2video](https://github.com/pankod/canvas2video)查看

[https://www.youtube.com/embed/xv8M3ScElv0](https://www.youtube.com/embed/xv8M3ScElv0)