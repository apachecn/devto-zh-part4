# 构建具有可访问性的自定义音频播放器

> 原文：<https://dev.to/snowleo208/build-a-custom-audio-player-with-accessibility-1c8f>

> 这篇文章最初发表在我的[个人博客](https://blog.atrera.com/javascript/accessibility/2019/06/28/create-a-custom-audio-player-with-accessibility.html)上。

这不是我第一次创建自定义音频播放器。

为了迎合我可爱的设计师的需求，我需要创建一个自定义的音频播放器。在学习了 [HTML5 音频属性](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/audio)和查看了更多制作自定义音频播放器的教程后，大部分教程都没有提到可访问性。

这一次我使用 **React** 作为音频播放器，但是您可以随时查看我上次为普通 JavaScript 版本定制的音频播放器(尽管当时并不太关心可访问性)。

我不是无障碍的专家，请随时让我知道你的想法！

* * *

## 玩家的标记

第一，音频播放器的外容器，它应该有`role="region"`和`aria-label="Audio Player"`。角色告诉屏幕阅读器这个`div`代表着什么，从`aria-label`开始，屏幕阅读器知道它是一个音频播放器。

### 外容器

```
<div className="c-audio" aria-label="Audio Player" role="region">
  // ...
</div> 
```

### 播放按钮

下面是里面的播放按钮:

```
<button
  title={!isPlay || isPlay === null ? 'Play' : 'Pause'}
  className={
    !isPlay || isPlay === null
      ? 'c-audio u-btn l-play l-play__play'
      : 'c-audio u-btn l-play l-play__pause'
  }
  aria-controls="audio1"
  onClick={this.controlAudio}
  aria-label={!isPlay || isPlay === null ? 'Play' : 'Pause'}
/> 
```

`aria-controls`链接到底部`audio`标签的 id(如`<audio id="audio1" ... >`)`aria-label`在播放或暂停时会发生变化。

### 滑块

对于音频控制，实际上我想使用 range(例如`<input type="range" ...>`)，然而，很难在所有浏览器中保持相同的风格，所以我决定使用`div`和`svg`，加上 aria 标签。另外，我在这里使用`tabIndex="0"`让键盘聚焦到这个元素。

在此滑块中，用户可以:

*   使用鼠标或键盘来更改音频的当前时间
*   可以聚焦滑块
*   可以用鼠标改变位置
*   可以使用键盘上的左键或右键来改变位置

```
<div
className="c-audio__slider"
onKeyDown={this.onKeyDown}
onClick={this.onClick}
tabIndex="0"
aria-valuetext="seek audio bar"
aria-valuemax="100"
aria-valuemin="0"
aria-valuenow={Math.round(percentage)}
role="slider"
ref={this.audioSeekBar}
> 
```

重新发明滑块需要很多工作，但这是值得的。在这些实现之后，你可以创建不同风格的滑块，并且具有可访问性！在此查看来自 [WAI-ARIA 创作实践](https://www.w3.org/TR/wai-aria-practices/examples/slider/slider-1.html)的示例。

### 操纵滑块

如何改变百分比的滑块时，它检测到点击或按键？我们可以使用`onClick`和`onKeyDown`函数。对于点击函数，它计算点击位置的百分比。(注:`seekBar.getBoundingClientRect().left`用于 IE11，因为它不支持`x/y`值)

```
onClick(e) {
    const seekBar = this.audioSeekBar.current;
    const audio = this.audioFile.current;

    const pos =
    (e.pageX -
        (seekBar.getBoundingClientRect().x ||
        seekBar.getBoundingClientRect().left)) /
        seekBar.getClientRects()[0].width;

        this.setState({
            percentage: pos * 100
        });

        audio.currentTime = audio.duration * pos;
} 
```

对于键盘版本，它根据不同的键增加或减少百分比。

滑块的键绑定:

*   顶部:至 100(最大)
*   底部:至 0(分钟)
*   向左:-1 步
*   右:+1 步
*   顶部:+10 步
*   底部:-10 步

```
onKeyDown(e) {
    // when user focus in audio slider and 
    // clicks keys inside key list, will change current time of audio
    const audio = this.audioFile.current;
    const isLeft = 37;
    const isRight = 39;
    const isTop = 38;
    const isBottom = 40;
    const isHome = 36;
    const isEnd = 35;
    const keyList = [isLeft,isRight,isTop,isBottom,isHome,isEnd];

    if (keyList.indexOf(e.keyCode) >= 0) {
        let percentage;
        switch(e.keyCode) {
            case isLeft:
            percentage = parseFloat(this.state.percentage) - 1
            break;
            case isRight:
            percentage = parseFloat(this.state.percentage) + 1
            break;
            case isTop:
            percentage = parseFloat(this.state.percentage) + 10
            break;
            case isBottom:
            percentage = parseFloat(this.state.percentage) - 10
            break;
            case isHome:
            percentage = 0
            break;
            case isEnd:
            percentage = 99.9 // 100 would trigger onEnd, so only 99.9
            break;
            default:
            break;
        }

        // add boundary for percentage, cannot be bigger than 100 or smaller than zero
        if(percentage > 100) {
            percentage = 100
        } else if(percentage < 0) {
            percentage = 0
        }

        this.setState({
            percentage
        });

        audio.currentTime = audio.duration * (percentage / 100);
    }
} 
```

### 音频标签

这里最主要的是音频标签。从音频标签，我们需要使用`onTimeUpdate`和`onEnded`来控制滑块。当音频运行时，它调用`onTimeUpdate`的函数并更新滑块。

当音频结束时，它会将音频的当前时间变为零，并将滑块的百分比也变为零。对于`<track kind="captions" />`，它是用于有字幕的音频或视频，我们这里没有，所以现在跳过它。

```
<audio
  className="c-audio__sound"
  id="audio1"
  src={path}
  onTimeUpdate={this.getCurrDuration}
  onEnded={() => {
    this.audioFile.current.currentTime = 0;
    this.setState({
      isPlay: false,
      currentTime: 0,
      percentage: 0
    });
  }}
  ref={this.audioFile}
>
  <track kind="captions" />
</audio> 
```

### 焦点样式

此外，不要忘记为播放按钮和滑块创建自定义的焦点样式！

```
.l-play:focus {
  outline: none;
  box-shadow: 1px 1px 1px 0px rgba(25, 25, 25, 0.2);
} 
```

### 结果

在下面查看我的结果，或者点击这里在 Codepen 上查看！

[https://codepen.io/snowleo208/embed/Zdvzym?height=600&default-tab=js,result&embed-version=2](https://codepen.io/snowleo208/embed/Zdvzym?height=600&default-tab=js,result&embed-version=2)

欢迎给我写信或让我知道你的想法！:)

* * *

## 阅读更多

*   [滑动噩梦:理解范围输入](https://css-tricks.com/sliding-nightmare-understanding-range-input/)
*   开发。Opera -一个更易使用的 HTML5 视频播放器
*   [WAI-ARIA 创作实践](https://www.w3.org/TR/wai-aria-practices/examples/slider/slider-1.html)