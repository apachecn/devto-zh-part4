# 用 tone.js 造钢琴！

> 原文：<https://dev.to/shimphillip/building-a-piano-with-tone-js-5c2f>

# 钢琴

这是我们将要建造的最终产品！你可以点击它或者在键盘上输入匹配的字母。

[https://codepen.io/shimphillip/embed/mZBQvg?height=600&default-tab=result&embed-version=2](https://codepen.io/shimphillip/embed/mZBQvg?height=600&default-tab=result&embed-version=2)

## 什么是 Tone.js？

Tone.js 是一个非常受欢迎的 Web 音频 API 库，在其 [GitHub 库](https://github.com/Tonejs/Tone.js/)上有超过 8k 颗星。在 Web Audio API 出现之前，浏览器必须利用音频文件来播放声音和音乐。它让你的应用程序变得更大，修改声音意味着每次都要引入不同的音频文件。那么，网络音频 API 能为我们做什么呢？

> Web Audio API 为控制 Web 上的音频提供了一个强大而通用的系统，允许开发人员选择音频源、为音频添加效果、创建音频可视化、应用空间效果(如平移)等等。

*来自:[https://developer . Mozilla . org/en-US/docs/Web/API/Web _ Audio _ API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)T3】*

它的一个非常强大的功能是**你现在可以只用 JavaScript 生成数字声音，而不需要使用音频文件！**。凭借振荡器和频率等音乐概念的一点点知识，我们能够创造出适合我们需求的各种音频。由于底层概念，使用纯 Web 音频 API 有点令人生畏，所以我们将在项目中使用 Tone.js，它抽象出底层方法。

## HTML

```
<ul id="piano">
  <li data-note="C4" class="key">
    <div data-note="C#4" class="black-key">R</div>
    D
  </li>
  <li data-note="D4" class="key">
    <div data-note="D#4" class="black-key">T</div>
    F
  </li>
  <li data-note="E4" class="key">
    G
  </li>
  <li data-note="F4" class="key">
    <div data-note="F#4" class="black-key">U</div>
    H
  </li>
  <li data-note="G4" class="key">
    <div data-note="G#4" class="black-key">I</div>
    J
  </li>
  <li data-note="A4" class="key">
    <div data-note="A#4" class="black-key">O</div>
    K
  </li>
  <li data-note="B4" class="key">
    L
  </li>
</ul> 
```

我们的标记相当简单。白键用“key”类表示，黑键有“black-key”类。请注意，在 HTML 层次结构中，黑键被包装在白键内，以便相对定位。每个键还具有带有相应注释的数据注释属性。

## CSS

```
@import url('https://fonts.googleapis.com/css?family=Roboto:400,700&display=swap');
 body {
     font-family: 'Roboto', sans-serif;
     display: flex;
     justify-content: center;
     align-items: center;
     min-height: 100vh;
}
 ul {
     list-style: none;
     display: flex;
}
 ul .key {
     position: relative;
     width: 60px;
     height: 180px;
     border: 1px solid black;
     border-right: none;
     background: #fffff0;
     border-radius: 5px;
     box-shadow: 0px 3px 5px #666;
     cursor: pointer;
     display: flex;
     justify-content: center;
     align-items: flex-end;
     padding-bottom: 10px;
     font-weight: bold;
}
 ul .key:last-child {
     border-right: 1px solid black;
}
 ul .black-key {
     position: absolute;
     top: -1px;
     left: 37.5px;
     width: 45px;
     height: 120px;
     background: black;
     border-radius: 5px;
     box-shadow: 0px 3px 5px #666;
     z-index: 999;
     display: flex;
     justify-content: center;
     align-items: flex-end;
     padding-bottom: 10px;
     color: white;
} 
```

。按键样式我们的白键。它使用`align-items: flex-end`将字母放在键的底部。

## JavaScript

```
// Tone.Synth is a basic synthesizer with a single oscillator
const synth = new Tone.Synth();
// Set the tone to sine
synth.oscillator.type = "sine";
// connect it to the master output (your speakers)
synth.toMaster();

const piano = document.getElementById("piano");

piano.addEventListener("mousedown", e => {
  // fires off a note continously until trigger is released
  synth.triggerAttack(e.target.dataset.note);
});

piano.addEventListener("mouseup", e => {
  // stops the trigger
  synth.triggerRelease();
});

// handles keyboard events
document.addEventListener("keydown", e => {
  // e object has the key property to tell which key was pressed
  switch (e.key) {
    case "d":
      return synth.triggerAttack("C4");
    case "r":
      return synth.triggerAttack("C#4");
    case "f":
      return synth.triggerAttack("D4");
    case "t":
      return synth.triggerAttack("D#4");
    case "g":
      return synth.triggerAttack("E4");
    case "h":
      return synth.triggerAttack("F4");
    case "u":
      return synth.triggerAttack("F#4");
    case "j":
      return synth.triggerAttack("G4");
    case "i":
      return synth.triggerAttack("G#4");
    case "k":
      return synth.triggerAttack("A4");
    case "o":
      return synth.triggerAttack("A#4");
    case "l":
      return synth.triggerAttack("B4");
    default:
      return;
  }
});
// when the key is released, audio is released as well
document.addEventListener("keyup", e => {
  switch (e.key) {
    case "d":
    case "r":
    case "f":
    case "t":
    case "g":
    case "h":
    case "u":
    case "j":
    case "i":
    case "k":
    case "o":
    case "l":
       synth.triggerRelease(); 
  }
}); 
```

我们分手吧。前三行使用 Tone.js 提供的方法并设置我们的声音。然后，我们将事件监听器附加到 piano div，它使用事件冒泡来识别在监听点击事件后哪个元素是目标。我们获取元素的数据注释属性来播放它的声音。

我们不能对键盘事件做同样的事情，因为按键产生的事件对象不同于鼠标点击产生的事件对象。这就是为什么我们必须手动将键盘字母映射到相应的音符。

## 大结局

我希望这个演示很容易理解，并且克服了你对探索网络音频 API 的恐惧！欢迎在你有任何问题或想法的评论中给我留下任何问题或想法！感谢您阅读本博客！！！😆😊😃