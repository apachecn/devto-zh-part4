# Chrome 和 Firefox 中的动画声音

> 原文：<https://dev.to/tmns/animating-sound-in-chrome-firefox-1cb4>

# 上下文

我正在努力为希腊的一家木偶剧院公司开发一个交互式网站，让最终用户只需访问该网站，就能体验到木偶剧院的体验。这导致开发了一个主页，其中有几个木偶一样的角色，当用户将鼠标放在他们身上时，这些角色会执行一个独特的动作并发出独特的声音。网站的演示可以在这里用[播放。你可以在这里查看完整的源代码](https://oups.netlify.com)。

让角色执行不同的动作，这基本上相当于独特的 CSS 过渡，是容易的部分。当我试图给角色配音时，我开始面临挑战。我已经通过 JavaScript 设置了适当的事件监听器。但出于某种原因，当我在 Firefox 中尝试这个网站时，我什么也听不到。此外，在 Chrome 中，我只能在打开 DevTools 时听到合适的声音。会出什么问题呢？？

# 火狐

原来几个月前火狐就已经开始阻止自动播放了。这似乎主要是由于恼人的视频的扩散，当你试图简单地阅读一篇新闻文章或类似的东西时，会自动播放恼人的视频。此外，与 Chrome 不同，Firefox 不会方便地显示允许网站播放音频的对话框。

因此，接下来的自然问题很简单——我们如何让用户同意播放音频？对于我的用例，这不是一个恼人的视频，而是主页木偶的基本动画的一部分，是创建一个音频开/关切换。我通过下面的 HTML 完成了这个任务:

```
<div class="row d-none d-md-flex flex-column justify-content-center align-items-center fadeInSlow">
  <span class="h3 text-secondary js--soundOn d-none"><i class="fas fa-volume-up"></i></span>
  <span class="h3 text-secondary js--soundOff d-none d-md-block"><i class="fas fa-volume-mute"></i></span>
  <input class="tgl tgl-light js--soundToggle" id="cb1" type="checkbox"/>
  <label class="tgl-btn" for="cb1"></label>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

如果仔细观察上面的代码片段，您会注意到，根据切换是打开还是关闭，相关的类、`js--soundOn`、`js--soundOff`和`js--soundToggle`被添加到元素的类列表中。我们也来看看相关的 JavaScript:

```
 $('.js--soundToggle').click(function () {
      if ($('.js--soundOff').hasClass('d-md-block')) {
        $('.js--soundOn').addClass('d-md-block');
        $('.js--soundOff').removeClass('d-md-block');
        for (let character of characters) {
          setAudioOnHover(character);
        } 
      } else {
        $('.js--soundOn').removeClass('d-md-block');
        $('.js--soundOff').addClass('d-md-block');
          for (let character of characters) {
            $(`#${character}`).off('mouseenter', playAudio);
            $(`#${character}`).off('mouseleave', pauseAudio);
          }
      }          
    })

[...]

  function setAudioOnHover(character) {
    $(`#${character}`).mouseenter(playAudio);
    $(`#${character}`).mouseleave(pauseAudio);    
  }

  function playAudio () {
    $(`#${this.id}Sound`)[0].currentTime = 0;
    $(`#${this.id}Sound`)[0].play()
      .catch(function (error) {
        // do nothing
      });
  }

  function pauseAudio () {
    $(`#${this.id}Sound`)[0].pause();
  } 
```

Enter fullscreen mode Exit fullscreen mode

这样，只要用户打开开关，Firefox 中的音频就会播放。但是 Chrome 呢？

# 铬

在 chrome 中，会出现一个对话框，提示用户允许播放当前站点的声音。此外，只要 DevTools 打开，音频就会正常播放。然而，如果你的网站必须在 DevTools 打开的情况下访问，它肯定还没有准备好供公众使用；所以，我开始寻找合适的解决办法。

这里的技巧最终是简单地(但聪明地)利用一个`iframe`来播放一个无声的音轨，以触发首次加载时的自动播放。一旦这样做了，随后的音轨可以正常播放。这一点非常棒，因为它可以单独通过 HTML:
来实现

```
<iframe src="resources/audio/silence.mp3" allow="autoplay" id="audio" style="display:none"></iframe> 
```

Enter fullscreen mode Exit fullscreen mode

就这样了！一旦无声音频触发了自动播放模块，其余的角色音频动画就可以正常播放了。

# 结论

火狐和 Chrome 最近都加强了他们的防骚扰措施，包括防止媒体自动播放。虽然我承认这是一个有用和受欢迎的功能，当处理侵入性的视频或音频广告时，它也可能是一个障碍，当仅仅试图用小动画集使网站变得生动时。

当然，漏洞总是存在的。对于 Firefox，这包括设置一些 HTML 和 JavaScript，利用添加和删除类名来为角色添加音频动画。在 Chrome 中，这甚至更容易，因为它只需要一行 HTML，一个聪明的`iframe`，就可以实现想要的效果。

我希望这能帮助其他陷入类似困境的人。此外，如果有更好的解决方案，我会洗耳恭听！(好了，我现在就不说了> _ <)