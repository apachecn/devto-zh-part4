# 如何在 react 应用程序中播放隐藏的视频？

> 原文：<https://dev.to/stevenlavine/how-do-i-play-a-hidden-video-within-a-react-app-2gh1>

我正在通过转换以前的基于 jQuery 的 web 应用程序来学习 React。然而，我遇到了一个障碍。我正在尝试使用 react-simple-idle-monitor 让一个隐藏的视频在 x 秒后播放。我可以得到要播放的视频帧，如果我添加了 controls 属性，视频就可以播放，但是我知道如何使用 react 访问视频。

我尝试过使用 refs 和访问 document.getElementById('video ')。

这是我在 Screensaver.js 中的屏保组件

```
import React, { Component } from 'react';
type Props = {};

export default class Screensaver extends Component<Props> {
  props: Props;

  render() {
    return (
      <div className="screensaver">
        <video muted loop>
          <source
            src="../assets/video/my-video.mp4"
            type="video/mp4"
          />
        </video>
      </div>
    );
  }
} 
```

下面是我在 App.js
中的渲染函数

```
render() {
    return (
      <IdleMonitor
        activeClassName="appActive"
        idleClassName="appIdle"
        timeout={30000}
        onActive={stopScreensaver}
        onIdle={startScreensaver}
      >
        <div className="panels">
          <Home />
          <Screensaver />
        </div>
      </IdleMonitor>
    );
  } 
```

下面是 actions/screensaver.js
中启动和停止屏幕保护程序的函数

```
export function startScreensaver() {
   const videoFile = document.getElementById('screensaver-video');
   videoFile[0].play();
}
export function stopScreensaver() {
  const videoFile = document.getElementById('screensaver-video');
  videoFile[0].pause();
  videoFile[0].currentTime = 0;
} 
```

超时触发后，视频容器通过 CSS 显示，但视频不播放。

目前，我得到的错误是视频文件[0]是未定义的。