# 如何使用 HTML、CSS 和 JavaScript 制作自定义输入范围滑块

> 原文：<https://dev.to/temmietope/how-to-make-a-custom-input-range-slider-using-html-css-and-javascript-4j7g>

HTML 的输入类型 range 非常好用。只需一些 CSS 样式，您就可以开始了。然而，自然的行为可能并不符合你的需要。假设你需要一个音频播放器或者任何东西的自定义输入范围，你可以设计这个范围，控制器，你怎么做呢？我是这样做的:

**HTML**

*   创建一个 div 并给它一个类名

```
<div class="range">
</div> 
```

Enter fullscreen mode Exit fullscreen mode

*   在 div 中创建另一个 div 作为滑块。也给它一个类名

```
<div class="range" id="range">
  <div class="slider" id="slider">
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

**CSS**

*   给出两个 div 的具体高度。在这个例子中，我将使用相同的高度。

```
.range{
  height: 10px;
  border: 1px solid black;
}
.slider{
  height: 10px;
  width: 80px;
  background-color: red;
} 
```

Enter fullscreen mode Exit fullscreen mode

**JavaScript**

*   将 DOM 节点作为目标，并将每个节点附加到变量上

```
const rangeDiv = document.querySelector("#range");
const sliderDiv = document.querySelector("#slider"); 
```

Enter fullscreen mode Exit fullscreen mode

*   我将使用宽度百分比。因此，我们必须创建一个名为 percent 的变量。

```
let percent = 0; 
```

Enter fullscreen mode Exit fullscreen mode

*   为了模拟滑块增量，我将使用 setInterval，我将它赋给了一个名为“timer”的变量。

```
let timer = setInterval(()=> {
  sliderDiv.style.width = `${percent}%`
  percent += 5
}, 1000); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们将 styleDiv 的宽度指定为“percent%”。然后，每秒钟，百分比变量增加 5。

*   现在，当宽度达到 100%时会发生什么？我们必须确保计时器停止。我们通过清除 setInterval 来做到这一点

```
let timer = setInterval(()=> {
  if(percent === 100){
    clearInterval(timer);
  }
  sliderDiv.style.width = `${percent}%`
  percent += 5
}, 1000); 
```

Enter fullscreen mode Exit fullscreen mode

就是这样。自定义输入范围滑块工作正常。您可以查看下面的完整代码库。

[https://codepen.io/temmietope/embed/ewOYLN?height=600&default-tab=result&embed-version=2](https://codepen.io/temmietope/embed/ewOYLN?height=600&default-tab=result&embed-version=2)

我用这种方法为我的音乐播放器定制输入范围。检查我的 github 库。
[https://github.com/temmietope/Music-player](https://github.com/temmietope/Music-player)