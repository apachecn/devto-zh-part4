# 请帮我建立一个手机云视觉 SLAM 系统

> 原文：<https://dev.to/mjyc/please-help-me-building-a-cloud-visual-slam-system-for-cellphones-ine>

你好，黑客们，修补者们，网络开发者们，系统开发者们，机器人专家们，以及所有的程序员们！我一直对[云机器人](https://en.wikipedia.org/wiki/Cloud_robotics)感到兴奋，这是一个利用云计算力量的机器人领域，我想与你分享这种兴奋，并建议一个我们可能合作的项目。我正在考虑的项目是“手机视觉冲击”。这个想法是在云上运行一个视觉 SLAM 系统，这样像手机这样的移动设备就可以通过简单地上传相机数据到云上来构建 3D 地图。

以下是我正在考虑的步骤:

1.  尝试使用 [ORB_SLAM2](https://github.com/raulmur/ORB_SLAM2) 和桌面相机图像创建 3D 地图。这一步的主要目标是熟悉可视化 SLAM 库，并感受其局限性。
2.  尝试使用运行在台式机上的 ORB*SLAM2 和手机摄像头图像创建 3D 地图。ORB_SLAM2 支持 [ROS](https://www.ros.org/) 。因此，人们可以使用 [HTML5 的`MediaDevices.getUserMedia()`](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia) 轻松捕获设备摄像头图像，将它们转换为 ROS 图像消息，并使用 [roslibjs](https://github.com/RobotWebTools/roslibjs) 发布它们，以便 ORB_SLAM2 可以使用从远程设备收集的图像。_ 更新:*
3.  运行 ORB_SLAM2 到 cloud。我没有尝试过，但似乎很容易将一个 ROS 包打包并部署在云上。

就是这样！你有兴趣尝试这个想法吗？如果你有视觉 SLAM 的经验并有建议？告诉我，我很想听听你的想法。

### 更新

我已经继续前进，因为我没有时间花在修补上，但仍然认为这是一个有趣的项目，有一天可以尝试一下。
*2020/11/23*[fusion](https://fyusion.com/)和 [CANVAS](https://canvas.io/) 似乎为产品提供了相关技术。似乎 se 2 lam github.com/izhengfan/se2lam 可以用来代替 ORB_SLAM2。

## 注释

> 请帮我搭建一个手机云端视觉 SLAM 系统[【https://t.co/PtRVnC0MFT】](https://t.co/PtRVnC0MFT)[【机器人】](https://twitter.com/hashtag/robotics?src=hash&ref_src=twsrc%5Etfw)[【机器人】](https://twitter.com/hashtag/robotics?src=hash&ref_src=twsrc%5Etfw)[【docker】](https://twitter.com/hashtag/docker?src=hash&ref_src=twsrc%5Etfw)[【ar】](https://twitter.com/hashtag/ar?src=hash&ref_src=twsrc%5Etfw)[【SLAM】](https://twitter.com/hashtag/slam?src=hash&ref_src=twsrc%5Etfw)[【cloud】](https://twitter.com/hashtag/cloud?src=hash&ref_src=twsrc%5Etfw)[【C】](https://twitter.com/hashtag/C?src=hash&ref_src=twsrc%5Etfw)++[# JavaScript](https://twitter.com/hashtag/JavaScript?src=hash&ref_src=twsrc%5Etfw)[#开源](https://twitter.com/hashtag/opensource?src=hash&ref_src=twsrc%5Etfw) [#webdev](https://twitter.com/hashtag/webdev?src=hash&ref_src=twsrc%5Etfw)
> 
> — Mike C. (@mjyc_) [June 10, 2019](https://twitter.com/mjyc_/status/1137953012926672896?ref_src=twsrc%5Etfw)