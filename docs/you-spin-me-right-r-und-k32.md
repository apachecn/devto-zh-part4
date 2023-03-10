# 你让我旋转吧，r⟳und

> 原文：<https://dev.to/rowan_m/you-spin-me-right-r-und-k32>

这是我如何建造 [系列文章的第一篇🥑 *Rotavo PWA*](https://bit.ly/rotavo-pwa) *。试一试，看看你能画出什么！*

没有一个酷孩子再对 skeuomorphism 感兴趣了，但这并不意味着在软件世界里没有物理控制的模拟。让我们看看如何重新创造(希望)调整立体声音量的直观体验📻通过构建我们自己的旋转、触摸感应旋钮网络组件。

## 目录

*   [🎛️滑块、磁带和刻度盘](#sliders-tapes-and-dials)
*   [⚛️一个基本的定制元素](#a-basic-custom-element)
*   [👻进入阴影 DOM](#enter-the-shadow-dom)
*   [🚧浏览器支持](#browser-support)
*   [🤝手柄触摸](#handle-touch)
*   [👆指针事件和默认行为](#pointer-events-and-default-behaviour)
*   [📐触摸三角学](#touching-trigonometry)
*   [🔄感人的演示](#a-touching-demo)
*   [🚧哦等等…浏览器支持](#oh-wait-browser-support)
*   [🖌️绘画和表演](#painting-and-performance)
*   [🏆奖励内容](#bonus-content)

## 🎛️滑块、磁带和表盘

我们将使用`input type="range"`作为我们希望组件呈现的 API 的基础。下面的代码片段将创建一个小滑块，从 0 到 100，起始值为 42。

```
<input type="range" name="volume" min="0" max="100" value="42" /> 
```

[![The default slider view in Chrome](img/9efdf138db66337dd809c7ef17ef48fd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Gkl_9csz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9pngvopkw7hjeliptpdt.png)

坚实的基础，但这是最好的解决方案吗？如果你和我一样，喜欢阅读作为 HCI International 2009 的一部分举行的[2009 人机界面研讨会的会议记录，那么在第二部分第 773 页左右，你会发现这个有价值的信息:](https://books.google.co.uk/books?id=I47_sQnnf_IC&pg=PA773&lpg=PA773&dq=)

> 飞行员发现阅读磁带显示比模拟刻度盘更难

我们将需要一点试点行话，使 UX 知识的意义，是在这里下降。

[![Diagram showing a linear "tape" display compared to a round "dial".](img/4affd2ad0825b0a747bd0faba4f8608d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ala0Wdyj--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/58coq9g7vyip5cgt328h.png)

“磁带”显示器以线性方式显示信息，之所以这样命名，是因为它实际上是一条在玻璃后面上下缠绕的磁带。这可以用来显示高度或空气速度。另一种选择是刻度盘指示器，以圆形布局显示相同的线性信息，通常用于汽车仪表板上显示速度和燃油油位。这里的假设是，与卷尺相比，人可以更快地解读刻度盘上的粗略值，因为整个量程都是可见的。

老实说，这与构建我们的组件没有太大关系，但是我希望您的第一印象是我在工作中采用了基于证据的方法。另外，我想得越多，我不确定在表盘上一次看到整个刻度的好处是否真的适用于我们可以旋转多次的旋钮…事实是，我做这个是为了好玩，很高兴意外地从中获得了一些有价值的东西。我们将暂时搁置工程与艺术的争论，继续创作这个部分。

## ⚛️一种基本的风俗元素

我将快速浏览一下基础知识，所以如果你正在寻找一个合适的介绍，你应该前往“[定制元素 v1:可重用 Web 组件](https://developers.google.com/web/fundamentals/web-components/customelements)”。

我们从简单的开始:一个只有一个`value`属性的元素。定制元素的名称中需要一个“`-`”来区别于内置元素，所以让我们使用`<input-knob>`来提供返回到`<input type="range">`的链接:

```
<input-knob value="1"></input-knob> 
```

为了方便起见，我们也将 HTML 元素的属性映射到 JavaScript 类，这样我们就可以这样做:

```
const knob = document.querySelector('input-knob');
knob.value = 2; 
```

这意味着我们需要扩展`HTMLElement`类，连接属性，然后将它连接到我们的页面:

```
class InputKnob extends HTMLElement {
  constructor() {
    // Nothing to do in here at the moment, but we do need to call the parent
    // constructor
    super();
  }

  // Watch for changes on the 'value' attribute
  static get observedAttributes() {
    return ['value'];
  }

  // Map the JavaScript property to the HTML attribute
  get value() {
    return this.hasAttribute('value') ? this.getAttribute('value') : 0;
  }

  set value(value) {
    this.setAttribute('value', value);
  }

  // Respond to any changes to the observed attributes
  attributeChangedCallback(attrName, oldVal, newVal) {
    // nothing yet
  }
}

// Map the class to an element name and make it available to the page!
window.customElements.define('input-knob', InputKnob); 
```

现在，如果你在浏览器中加载，你会看到…什么也没有。页面上没有，控制台上也没有。没有显示任何内容，但也没有任何内容被显式破坏。我们已经成功地在页面中附加了一个新元素，但是我们创建的只是一个空元素。因此，它的工作方式与向页面添加一个空的`<span>`是一样的。不过方便的是，我们的新`<input-knob>`元素可以像页面上的其他任何东西一样进行样式化——让事情变得更明显一些。

```
input-knob {
  display: block;
  background: #cadbbc;
  border: 1rem dashed #356211;
  box-shadow: 0.3rem 0.3rem 0.3rem rgba(0, 0, 0, 0.5);
  width: 8rem;
  height: 8rem;
} 
```

[![A light-green square with a dark-green dashed border on a light-yellow background](img/2cce752d0602c642fba09fa3f3850297.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CvaKYZU3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/oflojqapbalhth5i2v7d.png)

好了，在显眼的广场！

## 👻进入影子王国

对于元素显示，我们应该对`value`属性做些什么。具体来说，我们希望根据属性值旋转元素。它的核心将是我们在`transform`中使用的 [CSS 变量](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)，就像这样:

```
{
  --angle: 0rad;
  transform: rotate(var(--angle));
} 
```

然而，我们不想直接在`<input-knob>`元素上设置这个样式，因为它向组件的用户公开了实现细节。如果一个开发者想在他们的页面上添加`<input-knob>`并应用旋转，这将会发生冲突。为了解决这个问题，我们将利用[影子 DOM](https://developers.google.com/web/fundamentals/web-components/shadowdom) 来创建一些标记，这些标记本质上是我们组件的私有标记，我们可以在其中添加我们自己的结构和样式来支持组件的行为。

我们将从这个 HTML 开始:

```
<style>
  #container {
    --angle: 0rad;
    transform: rotate(var(--angle));
  }
</style>
<div id="container" part="container">
  <slot></slot>
</div> 
```

让我们来分析一下这里发生了什么:

*   `--angle` CSS 变量将被更新以控制元素的旋转
*   `<div>`中的`id="container"`定义了我们将要旋转的包装元素
*   `<div>`中的`part="container"`指示我们想要使元素可用于外部样式
*   元素定义了标签`</input-knob>`中的`<input-knob>`内容应该呈现在哪里

我们想要通过`part="container"`暴露`<div>`进行样式化的原因是因为我们的`<input-knob>`元素不会旋转。所以，它仍然可以被样式化——但是当值改变时，它将保持不变。相反，我们需要在样式表中使用 [`::part()`选择器](https://meowni.ca/posts/part-theme-explainer/)。在这种情况下，它是一个简单的加法。

```
input-knob::part(container) {
  display: block;
  background: #cadbbc;
  border: 1rem dashed #356211;
  box-shadow: 0.3rem 0.3rem 0.3rem rgba(0, 0, 0, 0.5);
  width: 8rem;
  height: 8rem;
} 
```

回到我们的元素，现在我们知道了我们想要的结构，是时候把它放到我们的元素可以使用的模板中了。我们将在`InputKnob`类之外定义它:

```
const template = document.createElement('template');
template.innerHTML = `
  <style>
    #container {
      --angle: 0rad;
      transform: rotate(var(--angle));
    }
  </style>
  <div id="container" part="container">
    <slot></slot>
  </div>`; 
```

接下来，我们想为元素添加一个影子根，并将模板克隆到其中:

```
class InputKnob extends HTMLElement {
  constructor() {
    super();
    this.attachShadow({ mode: 'open' });
    this.shadowRoot.appendChild(template.content.cloneNode(true));
    this._container = this.shadowRoot.getElementById('container');
  } // ✂️ rest of the class omitted
} 
```

我们正在获取对我们的`#container`的引用，以便我们可以更新那个`--angle`变量。

```
// class InputKnob
_drawState() {
  this._container.style.setProperty('--angle', `${this.value}rad`);
} 
```

我们希望在组件第一次被添加到页面时以及每当`value`被更改时更新该值。

```
// class InputKnob
connectedCallback() {
  this._drawState();
}

attributeChangedCallback(attrName, oldVal, newVal) {
  this._drawState();
} 
```

这样，我们现在有了一个基于`value`属性旋转的组件。我们可以通过打开浏览器控制台尝试设置这些值并观察方块旋转来验证这一点。

[![Screenshot of Chrome with Developer tools open](img/a0ca23ab096fbc3f7c41c29fd11b5ba3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ttfv-FnX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/07jcf5s47vsdznp9xo68.png)

好吧，好吧——你不感兴趣，我应该给你一些互动的东西。让我们把它整圆，并添加一个滑块来控制旋转。这很简单，因为:

```
<input-knob value="2.5"></input-knob>
<input type="range" name="angle" min="0" max="6.28" step="0.1" value="1" />
<script>
  const knob = document.querySelector('input-knob');
  const slider = document.querySelector('input[type=range]');
  // Just take the value from the slider and set it straight on the knob
  slider.addEventListener('input', () => { knob.value = slider.value; });
</script> 
```

请在下面亲自尝试一下。滑块应该将平方从 0 到 2π (6.28)弧度或一整圈。

[https://glitch.com/embed/#!/embed/building-rotavo-01?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/building-rotavo-01?previewSize=100&path=index.html)

## 🚧浏览器支持

不过，有点太干净了，是吧？几乎不是现代 web 开发的全部。我可能没有提到，在上面的例子中，为了让它在不同的浏览器中显示，还需要做更多的事情。事实上，如果你查看了 [MDN 关于浏览器对`::part()`](https://developer.mozilla.org/en-US/docs/Web/CSS/::part#Browser_compatibility) 支持的部分——嗯，我可能会为此提交一个拉取请求。基本上，这是 [Chrome 和其他浏览器发出了积极的声音](https://chromestatus.com/features/5763933658939392)，但我们还没有到那一步。

第一个问题是解决那些不支持自定义元素或阴影 DOM 的浏览器。幸运的是，我们可以使用 [`webcomponents.js` polyfill](https://github.com/webcomponents/polyfills/tree/master/packages/webcomponentsjs) 来检测需要什么支持。在导入/定义元素之前，将脚本放入。我是从`unpkg.com`下载的，但是你也可以下载这个包或者按照说明使用`npm`。

```
<script src="https://unpkg.com/@webcomponents/webcomponentsjs"></script> 
```

不要被一种虚假的安全感所迷惑——它现在变脏了。我找不到检测对`::part()`支持的好方法，所以请多包涵。还记得我们给元素添加了`part="container"`属性吗？我们将测试该属性是否可以通过 JavaScript 访问。如果不是，我们需要创建一些替代结构来进行样式化。在本例中，一个`<span>`包装元素中的任何内容，一个`.fallback`类。

```
// connectedCallback()
if (!this._container.part) {
  // create a <span>
  const wrapper = document.createElement('span');
  // move this element's child nodes into it
  wrapper.append(...this.childNodes);
  // add the classes
  wrapper.classList.add('fallback');
  this.classList.add('fallback');
  // and add the wrapper into this element
  this.append(wrapper);
} 
```

现在我们复制回退元素的样式:

```
input-knob.fallback {
  display: block;
}

input-knob.fallback>span.fallback {
  display: block;
  background: #cadbbc;
  border: 1rem dashed #356211;
  box-shadow: 0.3rem 0.3rem 0.3rem rgba(0, 0, 0, 0.5);
  width: 8rem;
  height: 8rem;
} 
```

理想情况下，我们可以将选择器添加到现有的规则中，而不是复制整个块——然而，这会导致至少一个其他浏览器忽略整个块。所以，复制/粘贴它。恶心。无论如何，现在我们有了一个几乎在任何地方都有效的元素，我们将永远不会再提到它。让我们来点更有趣的。

## 🤝手柄触摸

使用滑块证明我们可以控制旋转，但是如果我们希望能够点击并拖动元素，事情会变得有点复杂。我们需要做三件事:

*   使用[指针事件](https://developers.google.com/web/updates/2016/10/pointer-events)
*   抑制一些默认的浏览器行为
*   拼命努力记住学校里的一些三角学知识

## 👆指针事件和默认行为

让我们从指针事件(坦白地说，这是一个可爱的 API)开始，并加入一点处理默认事件的内容，因为这两者都不会给我们带来太多痛苦——不像列表中的最后一项。

```
class InputKnob extends HTMLElement {
  // ✂️ existing code has been removed
  constructor() {
    // Capture the pointer so that the user can still rotate the element even
    // if they leave the bounding area of it.
    this.setPointerCapture(e.pointerId);
    // Re-bind this for listeners so that this == instance of this class
    this._onPointerdown = this._onPointerdown.bind(this);
    this._onPointermove = this._onPointermove.bind(this);
    this._onPointerup = this._onPointerup.bind(this);
  }

  connectedCallback() {
    // Listen for pointerdown events when the element is connected to the page
    this.addEventListener('pointerdown', this._onPointerdown);
  }

  disconnectedCallback() {
    // And stop listening if the element is removed
    this.removeEventListener('pointerdown', this._onPointerdown);
  }

  _onPointerdown(e) {
    e.preventDefault();
    // A long press can bring up the context menu, we want to disable that when
    // the user is controlling the element
    window.oncontextmenu = () => { return false; };
    // Only add listeners for the other events once interaction has started
    this.addEventListener('pointermove', this._onPointermove);
    this.addEventListener('pointerup', this._onPointerup);
    this.addEventListener('pointercancel', this._onPointerup);
  }

  _onPointermove(e) {
    // On all of these we're preventing the default behaviour as we assume our
    // web developer won't do something like put a <button> inside the element.
    e.preventDefault();
  }

  _onPointerup(e) {
    e.preventDefault();
    // Re-enable the context menu
    window.oncontextmenu = null;
    // Release the pointer we captured earlier
    this.releasePointerCapture(e.pointerId);
    // Remove all our listeners
    this.removeEventListener('pointermove', this._onPointermove);
    this.removeEventListener('pointerup', this._onPointerup);
    this.removeEventListener('pointercancel', this._onPointerup);
  }
} 
```

我们还想更新我们的`template`中的 CSS，以确保一些合理的交互。我们将利用`:host`伪类来选择外部的`<input-knob>`标签。

```
host {
  display: inline-block;
  user-select: none;
  -webkit-user-select: none;
  touch-action: none;
}
#container {
  --angle: 0rad;
  transform: rotate(var(--angle));
} 
```

在上述风格中发生了三件事:

*   首先，如果您在 DevTools 中检查前面例子中的`<input-knob>`元素，您将看到它有一个`auto`宽度和高度，并且没有占用文档中的空间。如果我们的用户要触摸任何东西，我们需要元素有一个大小！
*   其次，`user-select`行阻止用户选择控件中的任何文本。就像防止上下文菜单弹出一样，这可以防止用户按住手指时出现奇怪的文本选择效果。
*   第三，`touch-action`告诉浏览器不要在这个元素上处理任何常见的手势——比如平移、缩放等。因为我们要在这里实现我们自己的行为。

## 📐触摸三角学

现在监听器已经连接好了，我们的元素将接收指针事件，我们可以响应这些事件。让我们深入数学！

作为一个温和的介绍，我们在类外创建了一个方便的常量供以后使用:

```
const TWO_PI = 2 * Math.PI; 
```

接下来，我们需要在用户第一次触摸元素时初始化一些值。容易得到的是指针事件的坐标、元素的中心和起始角度。

[![A square element with a point indicated at the centre and one in the upper right of the square](img/0c8bddb819e006b9a1bbce663bd1c98d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--y8NjgkzF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2110ud35kriggj0ie5kd.png)

```
// _onPointerdown(e)
this._touchX = e.clientX;
this._touchY = e.clientY;
this._centerX =
  this.offsetLeft - this.scrollLeft + this.clientLeft + this.offsetWidth / 2;
this._centerY =
  this.offsetTop - this.scrollTop + this.clientTop + this.offsetHeight / 2;
this._initialAngle = this._angle; 
```

接下来，我们需要触摸的起始角度远离零旋转。我们可以把这想象成画一个直角三角形，其中一个角在对象的中心，斜边向外延伸到用户的指针。我们可以从 2 个自变量的反正切函数得到这个角度，又名 [`Math.atan2()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/atan2) 。通过获取指针事件坐标并减去元素的中心坐标，我们基本上是将它们归一化，就好像元素的中心是 0，0。

[![A square element with a point indicated at the centre and one in the upper right of the square with a right angle triangle drawn through those points](img/cccea4cd0fb8403e0a2be3fd92a567b1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7mF_hSTu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/b70nzebozzzallam1mru.png)

```
// _onPointerdown(e)
this._initialTouchAngle = Math.atan2(
  this._touchY - this._centerY,
  this._touchX - this._centerX
); 
```

现在，当用户移动指针时，我们希望跟踪该移动，这样我们就可以相应地旋转元素。

[![The same square showing the point in the upper right moving down and further to the right](img/4772a0297389b9828d1dd747222c1afe.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--th6NdMvo--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zndqa2xonoxq1m37zzm3.png)

我们怎么知道这里发生了什么？答案当然是更多的三角形。首先，我们可以进行与之前完全相同的`atan2()`计算，但是使用新的指针位置。如果我们从当前角度减去起始角度，就得到我们在触摸中旋转的量。对于元素的最终角度，我们取它当前的旋转角度，即`value`，然后加上我们已经旋转的角度。

[![The same square but rotated by the change in angle](img/94a3e698c8470136ff754ad2d7898b47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sQ1JRRgi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8nw32osdu2f3y2tj0s34.png)

在代码中，它看起来像这样:

```
// _onPointermove(e)
this._touchX = e.clientX;
this._touchY = e.clientY;
this._angle =
  // initial rotation of the element
  this._initialAngle
  // subtract the starting touch angle
  - this._initialTouchAngle
  // add the current touch angle
  + Math.atan2(this._touchY - this._centerY, this._touchX - this._centerX);
// Normalise value back into a 2π range
this._angle = (this._angle + TWO_PI) % TWO_PI;
// Done, update the value!
this.value = this._angle; 
```

## 🔄感人的演示

天啊，喘口气吧。如果你还和我在一起，你…这是你应得的。你可以伸出你的鼠标或手指，旋转你喜欢的那个方块。

[https://glitch.com/embed/#!/embed/building-rotavo-02?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/building-rotavo-02?previewSize=100&path=index.html)

## 🚧哦，等等…浏览器支持

抱歉，我知道，我知道。[指针事件在支持方面有所进展](https://caniuse.com/#feat=pointer)，但并非无处不在。当指针不被支持时，我们需要换入[鼠标](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent)和[触摸](https://developer.mozilla.org/en-US/docs/Web/API/Touch_events)事件。

第一步是将特定于指针的逻辑与实际响应事件的业务分开。老实说，这无论如何都是很好的软件工程实践，并且可能是它最初应该被编写的方式。我没有展示`_rotation*()`方法，因为它们实际上就是之前的代码。

```
_onPointerdown(e) {
  e.preventDefault();
  this._touchX = e.clientX;
  this._touchY = e.clientY;
  // ✂️ Chop out existing code, extract into new method
  this._rotationStart();
  this.setPointerCapture(e.pointerId);
  this.addEventListener('pointermove', this._onPointermove);
  this.addEventListener('pointerup', this._onPointerup);
  this.addEventListener('pointercancel', this._onPointerup);
}

_onPointermove(e) {
  e.preventDefault();
  this._touchX = e.clientX;
  this._touchY = e.clientY;
  // ✂️ Cut and paste again
  this._rotationChange();
}

_onPointerup(e) {
  e.preventDefault();
  // ✂️ One last time!
  this._rotationEnd();
  this.releasePointerCapture(e.pointerId);
  this.removeEventListener('pointermove', this._onPointermove);
  this.removeEventListener('pointerup', this._onPointerup);
  this.removeEventListener('pointercancel', this._onPointerup);
} 
```

如果我们不能使用指针事件，我们还需要连接新的监听器:

```
// connectedCallback()
if ('PointerEvent' in window) {
  this.addEventListener('pointerdown', this._onPointerdown);
} else {
  this.addEventListener('touchstart', this._onTouchstart);
  this.addEventListener('mousedown', this._onMousedown);
} 
```

完事之后收拾干净。

```
// disconnectedCallback
if ('PointerEvent' in window) {
  this.removeEventListener('pointerdown', this._onPointerdown);
} else {
  this.removeEventListener('touchstart', this._onTouchstart);
  this.removeEventListener('mousedown', this._onMousedown);
} 
```

鼠标事件很容易添加进来，因为我们使用的表面与指针等同:

```
_onMousedown(e) {
  this._touchX = e.clientX;
  this._touchY = e.clientY;
  this._rotationStart();
  document.addEventListener('mousemove', this._onMousemove);
  document.addEventListener('mouseup', this._onMouseup);
}

_onMousemove(e) {
  e.preventDefault();
  this._touchX = e.clientX;
  this._touchY = e.clientY;
  this._rotationChange();
}

_onMouseup(e) {
  e.preventDefault();
  document.removeEventListener('mousemove', this._onMousemove);
  document.removeEventListener('mouseup', this._onMouseup);
  this._rotationEnd();
} 
```

触摸事件稍微复杂一些，因为存在多点触摸的范围。然而，我们只是假设第一次触摸是我们所关心的，而且…嗯，它还没有造成问题。

```
_onTouchstart(e) {
  e.preventDefault();
  this._touchX = e.changedTouches[0].clientX;
  this._touchY = e.changedTouches[0].clientY;
  this._rotationStart();
  this.addEventListener('touchmove', this._onTouchmove);
  this.addEventListener('touchend', this._onTouchend);
  this.addEventListener('touchcancel', this._onTouchend);
}

_onTouchmove(e) {
  e.preventDefault();
  this._touchX = e.targetTouches[0].clientX;
  this._touchY = e.targetTouches[0].clientY;
  this._rotationChange();
}

_onTouchend(e) {
  e.preventDefault();
  this.removeEventListener('touchmove', this._onTouchmove);
  this.removeEventListener('touchend', this._onTouchend);
  this.removeEventListener('touchcancel', this._onTouchend);
  this._rotationEnd();
} 
```

喔！这还不算太糟——现在我们的代码有了更好的关注点分离和跨浏览器支持！

## 🖌️的绘画和表演

不过，我们还没完。即使该元素正在运行，我们也应该看一看该元素运行得有多好。为此，你需要在一个新窗口中打开[之前的小故障演示](https://building-rotavo-02.glitch.me)，这样你就可以启动 Chrome 的 DevTools 了。我们想启用“油漆闪烁”——你可以在 3 点菜单下找到它→更多工具→渲染→油漆闪烁，或者如果你不喜欢三级菜单，然后用`Ctrl / Cmd + Shift + P`调出命令调色板，键入“油漆闪烁”。当你尝试旋转元素时，你会看到一个绿色的矩形一直画在上面。

[![Paint flashing rectangle displaying when the element moves](img/a0e8ff4305d3da8e19c0b95fca6abf06.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SUPOiE9F--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/g9l606dermuf6ohmqbkw.png)

这很糟糕。这意味着浏览器不得不在每一个交互上绘画，这并不总是一个便宜的操作。如果你在笔记本电脑或高端手机上，你可能不会注意到什么，但这会影响你在低端或繁忙设备上的帧率。幸运的是，这个补丁添加起来非常简单。我们希望向浏览器提供一个指示，表明我们打算以某种方式改变元素。很方便的是，`will-change`属性允许我们这样做。我们在内部`#container`的样式上指定了这一点。

```
#container {
  --angle: 0rad;
  transform: rotate(var(--angle));
  will-change: transform;
} 
```

现在浏览器知道我们打算改变`transform`属性，它可以优化结果。比较这个[更新的小故障演示](https://building-rotavo-03.glitch.me)和增加的这一行。你应该会看到一个短暂的初始闪光，但之后-没有任何绘画事件。成功！

## 🏆额外内容

到目前为止，我们还没有在元素中放入任何内容——我们只是对元素本身进行了样式化。如果你看一下 [Rotavo 应用程序](https://bit.ly/rotavo-pwa)你会发现🥑控件只是标签中的 SVG。原来你可以把任何你想要的内容放在那个标签里并呈现它。所以，作为这篇文章的结尾 *( **非常感谢永远有耐心的[保罗·金兰](https://twitter.com/Paul_Kinlan)借给我他的脸** )* 请随意去认识……保罗·斯平兰。

[https://glitch.com/embed/#!/embed/paul-spinlan?previewSize=100&path=index.html](https://glitch.com/embed/#!/embed/paul-spinlan?previewSize=100&path=index.html)

下一次，我们将探索添加自定义事件、最小/最大值和计数旋转到元素，这样我们就可以围绕它构建一个应用程序！