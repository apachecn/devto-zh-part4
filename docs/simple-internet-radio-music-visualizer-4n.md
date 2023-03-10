# 简单的互联网电台音乐可视化

> 原文：<https://dev.to/camerenisonfire/simple-internet-radio-music-visualizer-4n>

创建对音乐做出反应的动画是开始学习如何构建生成性艺术和设计的一种有趣方式。看到各种形状随着音乐在屏幕上跳舞是一件非常令人满意的事情。本教程将带您了解如何开始创建基于互联网广播流的反应式音乐可视化工具。本教程将坚持 web 技术，并基于我的 [Radio Visualizer Starter 项目](https://github.com/camerenisonfire/radio-visualizer-starter)。

> 这是我的博客 [Imago Dev](https://imago.dev) 的交叉帖子。

### 保持时间

音乐作为一个易于使用和不断变化的数据集，用于生成动画。声音和动画都有一个共同的变量——时间。虽然文字或图像一旦被创建就被锁定在适当的位置，但声音和动画都必须经过一段时间的体验。正是这个共享的参数允许这两种媒体之间如此容易的链接。

时间的强结合允许更容易地映射声音和动画之间确实不同的参数。对于声音，常用的参数是音量、频率和速度。这些直观地映射到动画中对象的大小、位置、颜色和速度。由于随着时间的推移改变这些参数是创建动画的原因，让音乐控制各种值是驱动动画的理想方式。

虽然音乐和动画之间的这种联系可以在任何音乐作品中实现，但对着广播流这样做实际上提供了无限量的音乐来播放。鉴于即使是最蹩脚的广播电台也不会全天候播放完全相同的歌曲，它的好处是提供更广泛的声音参数来构建您的动画。不仅如此，作为开发人员，您还可以在构建过程中刷新动画，而不必数百次听到同一首歌曲的前 20 秒。

### 带上声音

首先，我们需要接收和分析无线电流。有许多很棒的各种语言的声音库。现代网络浏览器配备了`<audio>`节点和[网络音频 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API) 。这个 API 提供了简单易用的方法来连接和分析我们的音频源。首先，我们需要定义我们的音频源。在这个例子中，我使用了[北卡罗来纳州立大学的广播电台，WKNC Raleigh](https://wknc.org) 。只要流允许跨原点共享，就可以把`src`换成自己喜欢的站。

```
<audio id="radio" preload="auto" crossorigin="anonymous">
  <source src="http://sc8.streammystation.com:7430/stream" />
</audio> 
```

Enter fullscreen mode Exit fullscreen mode

既然定义了音频节点，就需要告诉它播放。Chrome，以及可能更多的浏览器，现在强制要求用户在播放音频之前与页面进行交互。为了处理这个问题，我们必须将对`play()`方法的调用放在事件监听器中。在 starter 项目中，这包含在`radio.js`文件中。

```
//Uses the WebAudio API to analyze streaming audio
let analyser, source;
let audioElement = document.getElementById("radio"); //gets the element in the HTML document with the id="radio"

window.onload = function() {
  // Start playing the radio stream when document is clicked
  document.getElementById("defaultCanvas0").addEventListener("click", function() {
    audioElement.play();

    //TODO: hook up the analyzer
  });
}; 
```

Enter fullscreen mode Exit fullscreen mode

但是等等，这个`defaultCanvas0`元素是什么？现在是我们进入本教程动画部分的时候了。starter 项目使用 p5js 来创建我们的动画。最初的`setup()`函数创建了我们的`<canvas>`，它获得了`defaultCanvas`的 id。

在我们启动音频流并将其连接到将为我们分析音频的 API 之前，我们需要`draw()` p5js 函数告诉我们单击`<canvas>`开始。由于我们还没有创建我们的分析器，我们将只让 p5js 绘制背景和开始文本，然后不运行我们的 draw 函数的任何其余部分。

```
drawBackground();

if (!analyser) {
  //we haven't started the audio yet
  showStartText();
  return;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要连接分析器，这是我们整个动画的关键。这是网络音频 API 真正闪光的地方。所有音频节点的连接都发生在 click 事件监听器内部，紧跟在我们的`audioElement.play();`调用之后。

```
let context = new (window.AudioContext || window.webkitAudioContext)(); //Tells the browser we want to use Audio for out analyser

source = context.createMediaElementSource(audioElement); //Set the context to be our radio stream
analyser = context.createAnalyser();

source.connect(analyser); //Connect up the radio stream to the audio analyser

// Connect the output of the analyser to the destination, so that the audio will play out speakers
analyser.connect(context.destination); 
```

Enter fullscreen mode Exit fullscreen mode

基本上，上面的代码只是告诉 Web Audio API 在哪里可以找到我们的音频源，将该音频源连接到分析器，然后将分析器连接到我们的`destination`，它实际上就是我们计算机的扬声器。

### 获取频率

所有的东西都被连接起来，并准备好为我们的动画进行分析。要使用我们的分析器，我们需要向它请求动画每一帧的数据。对于初学者项目，我们将根据声音频率制作动画。为此，在 p5js 的`draw()`方法中，我们有

```
frequencyData = new Uint8Array(analyser.frequencyBinCount);

//Tell the analyser in radio.js to fill our Array with the frequency data
analyser.getByteFrequencyData(frequencyData); 
```

Enter fullscreen mode Exit fullscreen mode

这将清除上一个动画帧中的频率数据，然后用最新的频率数据填充它。这些数据被放入一个数组中，默认情况下，分析器用 1024 个不同的值填充这个数组。数组中索引越低，像`frequencyData[10]`，音频越低；因此，指数越高，如`frequencyData[900]`，细胞代表的音频频率越高。

### 驱动动画

最后一步是在我们的动画中使用这个频率数据。这个入门项目只是根据低频调整圆的大小。音频的频率越高，圆圈就越大。

```
//Draw an ellipse in the center of our canvas that scales with the frequency data
ellipse(width / 2, height / 2, 1 + frequencyData[10], 1 + frequencyData[10]); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样！每一帧，我们都有一个新的`frequencyData[10]`值，因此我们的圆也有了一个新的大小。最后一个要添加的小效果是让圆圈淡出。当圆改变大小时，这提供了更平滑的动画。

我们重新绘制框架的方法是用一个新的框架覆盖先前的框架。我们通过用新帧的背景覆盖最后一帧来做到这一点。如果我们保持背景 100%不透明，每一帧看起来都与前一帧完全分离；然而，如果我们把不透明度降低到 10%,那么这两帧就会互相渗透，因为最后一帧只是被新的背景模糊了一点点。这就产生了想要的淡出效果。

```
drawBackground() {
  //Sets the fill color of our shape red with low opacity
  //uses low opacity so the circle fades out on edges
  fill(255, 0, 0, 10);

  //Draw a rectangle, which will be red with low opacity
  rect(0, 0, width, height);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您已经掌握了基于音乐构建动画的基础知识，尝试将其他动画参数(如位置或对象数量)链接到频率数据！