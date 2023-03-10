# 我在开始播客时学到的所有经验

> 原文：<https://dev.to/lkopacz/a11y-lessons-i-learned-when-starting-a-podcast-3l82>

*最初发布于[a11ywithlindsey.com](https://www.a11ywithlindsey.com/blog/a11y-lessons-learned-starting-podcast)。*

如果你没注意到，我没在你身边！我发帖已经一个月了，有一个很好的理由。凯莉·沃恩、阿里·斯皮特尔、艾玛·韦德金德和我开始了瓢虫播客。这个播客在付出了很多努力后才取得成果。因此，我一直在想如何平衡这个博客和那个兼职项目！不过，别担心，这种情况不会消失。我的博客是我的激情项目！

现在你知道我去了哪里，我学到了很多关于播客的知识，以及它是如何与可访问性交叉的。我对其中的一些东西有很深的了解，但是在这篇文章中，我们将会更深入一点。

## 成绩单是必须的

当我们开始计划这个播客的时候，毫无疑问我们希望有文字记录。成绩单既费时又费钱，许多人视之为“好东西”但是，我在这里告诉你，它们是必备的。

成绩单有很多好处:

1.  聋人和重听人可以访问您的优秀内容。
2.  有些人喜欢阅读而不喜欢听。我无法告诉你有多少有能力的人告诉我，他们喜欢有成绩单。
3.  它帮助那些母语不是英语的人。
4.  如果人们正在阅读你的文字记录，这意味着他们在你的网站上花费了大量的时间。会话持续时间提高搜索引擎优化。

> 做一个内容创作者意味着你要让尽可能多的人可以看到你的内容。你不想因为决定忽视潜在观众的需求而疏远他们。

我们尝试了几次想要拿到抄本。我想分享我们尝试过的选项以及每个选项的利弊。

### 让某人为你做这件事

我们首先尝试的是雇人，因为这是我们知道的唯一选择。我们没有时间浏览整个音频并把它写下来。所以我们决定雇一个人来帮我们做这件事。我们用的是 Fiverr，我以前从来没试过。然而，雇佣某人有利也有弊。

优点:

*   付钱给一个真实的人并支持其他人。
*   实时捕捉错误

缺点:

*   昂贵的
*   因为他们是人，需要几天时间才能拿回来，这对于快速转身来说并不理想。
*   语言差异。

### 使用人工智能

另一种选择是使用人工智能软件解析你的音频，并为你提供一份抄本。我们尝试了 [Trint](https://app.trint.com) 和 [Otter AI](https://otter.ai) ，它们都是很棒的工具。AI 也有一些利弊。

优点:

*   在几分钟或几小时内取回您的成绩单。奥特艾用了 10 分钟左右。
*   比较便宜的
*   当他们了解你的演讲者时会变得更好

缺点:

*   尤其是刚开始的时候，你要编辑。我们不希望在那里有随机的单词，所以我们花了一些时间来听这个工具，因为它给我们读了抄本。
*   当我用机器学习代替一个人可以做的工作时，我感到有点内疚。

### 我们最终做了什么

因为我们需要快速周转，我们最终选择了人工智能。我们和奥特莱达成协议，最终使用了那台。做最适合你的事情，所有的选择都是好的！

## 定制播客播放器的所有注意事项

你可能知道，也可能不知道，我们用 [GatsbyJS](http://gatsbyjs.org) 建立了瓢虫播客网站。我负责制作一个无障碍的播客播放器。我学到了很多关于如何使用 React 创建所有 audioElement 事件的知识。大声喊出来 [Syntax.fm](https://syntax.fm/) 有一个我可以探索的玩家。我做的第一件事是给一个`<audio>`元素添加一个`ref`。

```
class Player extends React.Component {
  render() {
    const { show } = this.props;

    return (
      <audio
        ref={audio => (this.audio = audio)}
        src={show.audio}
      />
    )
  }
} 
```

如果我在代码中添加一个`console.log`

```
class Player extends React.Component {
  render() {
    const { show } = this.props;
    console.log(this.audio)

    return (
      <audio
        ref={audio => (this.audio = audio)}
        src={show.audio}
      />
    )
  }
} 
```

这使我能够访问音频的所有方法。我可以在我的自定义播放器上以按钮、单选按钮和滑块的形式使用它们。

在播客播放器中，我使用了以下方法:

*   htmlmediaelement . current time-这告诉你音频中的当前时间，以秒为单位。
*   htmlmediaelement . playbackrate-这告诉你你正在玩的速度。当我们按下一个按钮来提高速度时，我们可以设置这个属性！
*   HTMLMediaElement.volume -告诉你当前的音量。
*   [htmlmediaelement . play()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/play)-播放音频。
*   [htmlmediaelement . pause()](https://developer.mozilla.org/en-US/docs/Web/API/HTMLMediaElement/pause)-暂停音频。
*   HTMLMediaElement.duration -这告诉你音频的总长度，以秒为单位。

一旦将`ref`添加到音频中，您就可以使用`this.audio`来控制音频中的任何内容。您可以将方法或属性名添加到`this.audio`中。很整洁！您也可以使用它来控制反应状态🤯！在这篇博文中，我不会过多地介绍所有的州。如果你想了解更多，我建议你查看一下我们的源代码和 [Syntax.fm 源代码](https://github.com/wesbos/Syntax/blob/master/components/Player.js)。

### 按钮标签

按钮标签是播放器可访问的最重要的部分。记住，我们是在“黑”音频播放器。我们必须确保屏幕阅读器、键盘和鼠标用户可以访问播放器。后者是有能力的人所关注的，所以如果你这样做，我想确保你考虑了所有的经历。

我使用了`react-icons/fa`包来为我们的玩家获取图标。我们使用了`FaPlay`、`FaPause`、`FaUndo`和`FaRedo`作为播放、暂停、倒回 15 秒和快进 15 秒的按钮。这些图标是 SVG 图标，但我想确保标记中有文本。为此，我们确保了以下几点:

1.  我们使用语义`<button>`而不是`<div>`来支持键盘事件。阅读我的 [3 个提高键盘可访问性的简单技巧](https://www.a11ywithlindsey.com/blog/3-simple-tips-improve-keyboard-accessibility)来获得更多信息。
2.  我们确保不使用图标字体，这已经通过`react-icons`库解决了。
3.  我们确保按钮内有实际的文本，SVG 不为我们做这些。我们可以使用 CSS 在视觉上隐藏文本，但它仍然必须在那里。

```
<button class="player__icon">
  
    <!--paths and things-->
  
  <span class="sr-only">play</span>
</button> 
```

### 创建滑块

我在寻找一个可以显示滑块的播放器。我找到了有能力的玩家，并开始观察他们是如何做事的。让它变得可访问是我做过的最难的事情之一，但它仍然有很多问题。代码最终变成了许多带有 aria 标签和大量事件处理的 div。边项目的难题是完成比完美更好。回想起来，我希望我用[的范围输入](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/range)把它做成一个滑块。经验教训——当我更新那个滑块时，请继续关注未来的博客帖子！

现在，我将仔细考虑一下我想用滑块做什么。下面是源代码........

```
<div
  className="player__progress"
  onClick={this.scrub}
  ref={x => (this.progress = x)}
>
  <div className="player__progress-loaded" />
  <div
    className="player__progress-played"
    style={{ width: `${(currentTime / duration + 0.015) * 100}%` }}
  />
  <div
    orientation="horizontal"
    onKeyDown={this.moveSlider}
    tabIndex="0"
    className="player__slider"
    role="slider"
    aria-label="audio timeline"
    aria-valuemin="0"
    aria-valuemax={duration}
    aria-valuetext={renderValueText(currentTime)}
    aria-valuenow={renderValueNow(currentTime)}
    style={{ left: `${(currentTime / duration - 0.01) * 100}%` }}
  />
</div> 
```

我有 3 个玩家的子 div:`.player__progress-loaded`、`.player__progress-played`和`.player__slider`。让我们看看这些对应于什么:

[![Custom audio player: played portion is the color red and slider is a white circle.](img/be7017bd347f6dce234cbc2666670e06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--H3dB6_fa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.a11ywithlindsey.com/static/annotated-player-screenshot-3eb0883f8dcab597e51001e7657e8597-846dd.png)

*   `.player__progress-loaded`对应整个滑块的宽度。这个 div 对于屏幕阅读器用户没有语义值。
*   `.player__progress-played`是游戏总金额的直观表示。
*   是可访问性的最重要元素。它告诉屏幕阅读器用户当前的时间，并允许我们用键盘移动它。

现在让我们来看看这里的一些事件和风格。

对于`onClick`事件，我们希望确保将音频中的`currentTime`更改为我们单击的位置。如果可以的话，自己看看[代码](https://github.com/ladybug-podcast/ladybugpodcast/blob/master/src/components/player.js#L125)，分叉它，控制台记录所有的事情，看看发生了什么。如果你点击`.player__progress` div 上的任何地方，你将改变`currentTime`。

对于`.player__progress-played` div，我们有了样式。组件的宽度是一集的百分比。

现在是滑块本身，然后是几个[辅助函数](https://github.com/ladybug-podcast/ladybugpodcast/blob/master/src/utils/util.js)，其中一个是我从 Syntax 的网站改编的。

神圣的蝙蝠侠有很多咏叹调属性！让我们仔细分析一下:

1.  这告诉屏幕阅读器用户非语义 div 有一些含义！
2.  `aria-valuetext` -这是滑块值的人类可读版本。例如，在上面的截图中——是“5 分 51 秒”
3.  `aria-valuenow` -定义范围小部件(滑块)的当前值
4.  `aria-valuemin` -定义范围小部件最小值的必需属性
5.  `aria-valuemax` -定义范围小部件最大值的必需属性

下面是 macOS 上的 VoiceOver 读取滑块的方式:

5 分 51 秒，音频时间轴，滑块。于是有了`aria-valuetext`、`aria-label`和`role`。

使用`this.moveSlider`，如果您按下左箭头或右箭头，分别倒回或快进 5 秒。看看[的源代码](https://github.com/ladybug-podcast/ladybugpodcast/blob/e0cfce0159d1f516812465fbeafaaf5ecd47ef5a/src/components/player.js#L137)，看看我们是如何做到的。

### 我多么想重做滑块

如前所述，这是一个小错误。一些容易出错的事情:

1.  浏览器支持(我收到了一个听众的评论，但还没有查看)
2.  我不知道用你的鼠标拖动滑块。

所以，不管出于什么原因，我认为(不要这样想，这很危险)你不能自定义一个范围输入。

我错了。你完全可以。

## 结论

开始播客一直是一种非常积极的体验。我敢肯定，我们会谈论我们在瓢虫播客上开始播客时学到的更普遍的教训！

这篇文章的两个要点是:

1.  总要有成绩单。把其他人包括进来的时间和财务成本是值得的。这不是一个边缘案例。
2.  小心侵入音频播放器，确保你过度测试。

如果你对瓢虫播客感兴趣，一定要[订阅](https://link.chtbl.com/ladybugpodcast)下载它们！

保持联系！如果你喜欢这篇文章:

*   在 [Twitter](https://twitter.com/LittleKope) 上让我知道，并与你的朋友分享这篇文章！此外，如果有任何后续问题或想法，请随时发推特给我。
*   在 [patreon](https://www.patreon.com/a11ywithlindsey) 上支持我！如果你喜欢我的工作，可以考虑每月认捐 1 美元。如果你认捐了 5 美元或更多，你将能够对未来的博客文章进行投票！我还为所有顾客做了一个每月一次的“问我任何问题”的会议！
*   [成为第一个了解我的帖子的人](https://pages.convertkit.com/4218bd5fb5/68dc4e412a)以获得更多的可访问性乐趣！

干杯！祝你一周愉快！