# 在画中画窗口中玩霸王龙赛跑者游戏

> 原文：<https://dev.to/arnellebalane/playing-the-t-rex-runner-game-inside-a-picture-in-picture-window-kpc>

画中画 API 是一种新的网络平台 API，允许网站在一个小的浮动窗口中播放视频，即使浏览器不可见，该窗口也位于其他窗口的顶部，允许我们在与其他网站或应用程序交互时继续观看这些视频。

API 目前仅限于视频元素。幸运的是，我们还可以从画布元素创建视频流。这意味着我们可以在画布上画任何东西，并让它出现在画中画窗口中。这种尝试最终让我尝试让 Chrome 的离线 T-Rex runner 游戏可以在画中画窗口中玩([点击此处](https://pip-trex.arnelle.me/)立即玩)。

> ![unknown tweet media content](img/4700620eac8dfa5f9892ca6867efb9a4.png)![Play butt](img/980e9fb12d58fa9423fc94c33003fc4f.png)<video loop="" controls=""><source src="https://video.twimg.com/ext_tw_video/1134837766619926537/pu/vid/1306x720/7zO-DFeXU9BUPZ0_.mp4?tag=9" type="video/mp4"></video>![Arnelle Balane profile image](img/ec221d691766db967d555cd101d62314.png)Arnelle Balane[@ Arnelle Balane](https://dev.to/arnellebalane)![twitter logo](img/65e26e35707d96169ec8af6b3cbf2003.png)另一个画中画实验:在画中画窗口内运行 Chrome 的 T-Rex runner 游戏🦖
> 
> (使用键盘、耳机等上的播放/暂停媒体按钮。要跳！🔥)
> 
> [【web dev】](https://twitter.com/hashtag/webdev)[【JavaScript】](https://twitter.com/hashtag/javascript)[【web API】](https://twitter.com/hashtag/webapis)
> 
> [pip-trex . arnelle . me](https://t.co/7m1q58CCWT)
> [github.com/arnellebalane/…](https://t.co/TXQGQj26BH)2019 年 6 月 15:04 日[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1134838128127012864)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1134838128127012864)6[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1134838128127012864)

## 获取游戏的源代码

我们可以从[铬库](https://chromium.googlesource.com/chromium/src/+/master/components/neterror/resources/)获得游戏的源代码。我们复制该位置的内容，重命名一些文件，并清理 HTML，使其只包含`<body>` :
中必要的标记

```
<!-- This will contain the canvas element -->
<div class="interstitial-wrapper"></div>

<!-- Game assets: sprits + audio -->
<div id="offline-resources">
  <img id="offline-resources-1x" src="images/100-percent/100-offline-sprite.png">
  <img id="offline-resources-2x" src="images/200-percent/200-offline-sprite.png">

  <div id="audio-resources">
    <audio id="offline-sound-press" src="sounds/button-press.mp3"></audio>
    <audio id="offline-sound-hit" src="sounds/hit.mp3"></audio>
    <audio id="offline-sound-reached" src="sounds/score-reached.mp3"></audio>
  </div>
</div>

<!-- The main game script -->
<script src="offline.js"></script>

<!-- Initialize the canvas and the game. -->
<!-- Originally performed inside neterror.js -->
<script>
  const runner = new Runner('.interstitial-wrapper');
</script> 
```

这给了我们一个已经可以玩的离线霸王龙跑垒员游戏的精确副本:

[![offline T-Rex Runner replica](img/30144f57ec46aef61de1be1feb5bdc00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SSvNrgyA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9yz3fswcc6hpvwzdbbp6.png)

## 实现程序化霸王龙跳跃

每次我们按空格键，霸王龙就会跳起来。让我们添加一种方法，通过编程让我们的 T-Rex 跳跃，而不需要我们真的按空格键。

仔细研究游戏代码，我们会发现处理空格键的`runner`对象有两种方法:

1.  [`onKeyDown`](https://chromium.googlesource.com/chromium/src/+/master/components/neterror/resources/offline.js#676) ，让霸王龙在游戏运行时跳跃。
2.  [`onKeyUp`](https://chromium.googlesource.com/chromium/src/+/master/components/neterror/resources/offline.js#727) ，当霸王龙撞上障碍物时重启游戏。

让我们编写一个方法，根据游戏的状态调用其中任何一个，传入一个*虚拟*键盘事件。

```
function simulateSpacebar() {
  const keyboardEventOptions = {
    code: 'Space',
    keyCode: 32,
  };

  if (runner.crashed) {
    const event = new KeyboardEvent('keyup', keyboardEventOptions);
    runner.onKeyUp(event);
  } else {
    const event = new KeyboardEvent('keydown', keyboardEventOptions);
    runner.onKeyDown(event);
  }
} 
```

## 捕获画布内容的视频流

调用`new Runner('...')`创建一个画布元素并将其插入页面。我们需要获取对 canvas 元素的引用，然后将它的内容捕获为视频流:

```
const canvas = document.querySelector('canvas');
const videoStream = canvas.captureStream(); 
```

然后我们用视频流作为源创建一个`video`元素:

```
const video = new Video();
video.srcObject = videoStream;

video.muted = true;
video.play(); 
```

这里我们也将视频静音，这样我们就可以自动播放了(见 [Chrome 的自动播放政策](https://developers.google.com/web/updates/2017/09/autoplay-policy-changes))。

## 显示画中画窗口

当使用画中画这样的新的 Web APIs 时，在尝试使用它们之前，一定要检测它们是否可用。这确保了当 API 不可用时，我们的应用程序不会中断，只有当它可用时，才会逐渐增强体验。对于画中画，可以通过检查`document.pictureInPictureEnabled`属性:
来实现

```
const button = document.querySelector('button');

if (document.pictureInPictureEnabled) {

  // Picture-in-Picture is available!
  // Subsequent code snippets will be place inside this block.

} else {
  // Picture-in-Picture is not available. User can still play the game normally in the page.

  button.textContent = 'Picture-in-Picture is not available';
  button.disabled = true;
} 
```

我们还在页面中添加了一个`<button>`元素，用户可以点击它进入
画中画。我们希望将这种控制权交给我们的用户，通常是通过 UI 中的画中画图标，这样他们就可以决定何时在画中画窗口中查看我们的内容。

现在到了有趣的部分，当点击按钮时，让我们在画中画窗口中显示我们的视频流！

```
button.addEventListener('click', async () => {
  simulateSpacebar();
  await video.requestPictureInPicture();
}); 
```

结果看起来像这样:

[https://www.youtube.com/embed/JxmrlG_ROAY](https://www.youtube.com/embed/JxmrlG_ROAY)

## 实现游戏控制

画中画窗口可以停留在其他应用程序窗口的顶部，在这种情况下，我们将无法按下页面上的空格键来让 T-Rex 跳跃，所以我们需要另一种方法来做到这一点。

**媒体会议 API 来拯救！**

[媒体会话 API](https://developer.mozilla.org/en-US/docs/Web/API/Media_Session_API) 允许网站定制媒体通知，以及定义回放控制的事件处理程序(例如，播放、暂停等)。).通过定义`play`和`pause`事件处理程序，每当我们按下键盘上的播放/暂停按钮(或其他可以控制媒体播放的设备)时，我们可以让我们的 T-Rex 跳转。

```
navigator.mediaSession.setActionHandler('play', simulateSpacebar);
navigator.mediaSession.setActionHandler('pause', simulateSpacebar); 
```

画中画 API 与媒体会话 API 集成得很好。当我们定义回放事件处理程序时，画中画窗口也会显示它们对应的动作按钮。

[![](img/b58143905f38c71d5b31fb6c371b5641.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6-hRhiuc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/537g9fpfh4zxoao6bkw1.png)

## 来玩吧！

有了所有这些变化，我们现在可以在画中画窗口中玩霸王龙赛跑者游戏
，使用我们的播放/暂停媒体按钮让霸王龙跳跃！

您可以在以下链接中找到该项目的现场演示以及完整的源代码:

*   [https://pip-trex.arnelle.me/](https://pip-trex.arnelle.me/)
*   [https://github.com/arnellebalane/pip-trex](https://github.com/arnellebalane/pip-trex)

## 结论

在本文中，我们能够使用画中画 API 和媒体会话 API 来构建一些愚蠢的东西。这些 API 有更严肃和有用的用途——Youtube 在他们的播放器控件中有[一个隐藏的画中画按钮](https://twitter.com/arnellebalane/status/1118537633548759041),在我进行这个实验之前，我还使用本文中相同的技术在[上构建了一个演示，在画中画窗口](https://twitter.com/arnellebalane/status/1134768178318303232)中显示音频可视化。

如果你已经开发/正在开发一些使用这些 API 的东西，或者看到它们在野外的一些令人惊叹的用途，请在评论中与我们分享，我很乐意听到它们！

## 资源

*   [画中画 API](https://blog.arnellebalane.com/the-picture-in-picture-api-30415372009f)
*   [使用弗朗索瓦·博福特的画中画](https://developers.google.com/web/updates/2018/10/watch-video-using-picture-in-picture)观看视频
*   定制媒体通知并处理播放列表(媒体会话 API)