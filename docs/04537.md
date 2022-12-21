# 制作动态滑块

> 原文：<https://dev.to/mikkelmbk/making-a-dynamic-slider-2bka>

# 简介

### 在本教程中，我们将制作一个动态滑块，根据视窗的大小，在给定时间显示 1 到 3 个滑块项目。

### 滑块会向左滑动，无限循环。

### 要实现这种“向左滑动”,有三个关键因素在起作用:

CSS 属性 Margin，更确切地说是 Margin-Left:

```
 margin-left: 
```

Enter fullscreen mode Exit fullscreen mode

CSS 属性转换:

```
 transition: 
```

Enter fullscreen mode Exit fullscreen mode

以及 Javascript 函数 setInterval

```
 setInterval(()=>{
    },TimeInMilisecondsHere) 
```

Enter fullscreen mode Exit fullscreen mode

# 准备就绪

好的，首先你需要启动你最喜欢的代码编辑器，为这个项目创建一个目录，一旦完成，我们需要创建三个不同的文件，如下所示:

*   index.html
*   style.css
*   script.js

太棒了。现在在你的代码编辑器中打开你的 index.html 文件，在文件中输入一个感叹号，应该会弹出一个类似于[https://imgur.com/a/4JBIsrN](https://imgur.com/a/4JBIsrN)的下拉菜单，选择第一个。您现在应该有一个 HTML 文件，其中包含一些基本的 HTML。

既然 Html 文件已经准备好了，就可以开始链接样式表和 javascript 了

将你的 style.css 链接到你的 index.html 如下:[https://imgur.com/a/QBhVPHF](https://imgur.com/a/QBhVPHF)

将你的 script.js 链接到你的 index.html 如下:
[https://imgur.com/a/6LnxA2F](https://imgur.com/a/6LnxA2F)

# 构建基本的 HTML 结构

好了，现在你应该有一个 HTML 文件，其中绑定了一个样式表和一个脚本，所以现在我们可以开始构建动态滑块所需的基本 HTML 结构。

在 body 元素中，创建三个 div 元素，类如下:

```
<body>

    <div class="slider-content-wrapper">

        <div class="slider-container">

            <div class="slider-size-regulator">

            </div>
        </div>
    </div>

</body> 
```

Enter fullscreen mode Exit fullscreen mode

为滑动容器设置包装器的原因将在后面解释清楚，但是现在，我们只需要一个这样的结构。

我们的 slider-container 将作为网站上的可视滑块，它将显示我们稍后将在 javascript 中选择的滑块项目的数量。

我们的 slider-size-regulator 将充当无限宽的 slider-item 容器，它将根据我们得到的 slider-item(图像)的数量以及每个图像的宽度来调整宽度，并根据我们选择在可视 slider 上显示的 slider-item 计数来计时。

# 应用所需的样式来可视化地创建滑块

幻灯片的样式相当简单，我们只需要 CSS 文件中的一些规则来创建我们所追求的静态视觉外观。

首先，我们要为我们的可视 slider-container 元素定义一个高度，我们需要提供一个静态高度作为参考点，以便我们以后可以根据视窗大小以及显示的 slider-items 的数量来调整它:

Overflow:hidden 将让我们超出所提供的元素宽度，而不会在我们的网站上出现水平滚动条。由于这是我们的可视滑块元素，稍后将由一些 javascript 代码计算出它的宽度。

```
.slider-container{
    margin: 0 10px 15px 10px;
    height:265px;
    overflow:hidden;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，对于我们的滑块大小调节器元素，这是一个可能具有无限宽度的元素，取决于滑块项目的数量。

我们希望这个元素和它的父元素 slider-container 具有相同的高度，所以我们将赋予它一个值为 100%的 height 属性，它也将具有 overflow:hidden，原因和上面提到的一样。

我们的滑块大小调节器也应该有显示:flex，现在这可能看起来不明显，因为它真的不是！

Display:flex 将删除 slider-size-regulator 元素中每个 slider-item 之间的所有小间隙，这是因为当没有内容填充空白空间时，display flex 会折叠，这与 display:block 不同，它只存在于提供或不提供内容的情况下。这件事的重要性以后会变得清楚。

```
.slider-size-regulator{
    height:100%;
    overflow:hidden;
    display:flex;
} 
```

Enter fullscreen mode Exit fullscreen mode

slider-items 本身将是 slider-size-regulator 中的独立 div。我们的目标是在每个硬编码的 div 上使用内联样式，为它提供我们想要显示的图像，这远非完美，仍在进行中，但现在已经足够了。

```
<div class="slider-item" style="background-image:url(asseimg/featuredimg1.jpg)"></div> 
```

Enter fullscreen mode Exit fullscreen mode

将我们的图像作为硬编码的背景图像允许我们使用现有的大量背景 css 属性。

我们将为我们的 slider-items 赋予四个不同的 css 属性:高度、背景位置、背景重复和背景大小。

height 属性只是强制 slider-item 填充与其父级一样多的高度。

Background-position:center 找到图像的中心，并将其与 HTML 元素的中心对齐。

如果图像没有填满整个元素的高度或宽度，背景重复:不重复可防止图像重复。

Background-size:cover 强制图像覆盖整个 HTML 元素提供的空间。

```
.slider-item{
    height:100%;
    background-position: center;
    background-repeat: no-repeat;
    background-size:cover;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 用 Javascript 制作滑块滑动

首先，我们希望在 DOM 加载完成时监听，为此我们创建了一个事件监听器，就像这样

```
document.addEventListener('DOMContentLoaded',()=>{
 // Once the DOM has finished loading, all code in here will be run
}); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有了 eventListener，重要的是我们从现在开始做的所有代码只存在于 eventListener 中

现在，我们可以创建对 HTML 元素和其他变量的引用。

### 杂项变量

这 3 个变量实现了 3 个不同的目的，sliderInterval 变量被设置为包含一个空字符串，然而，它包含什么在这个时候并不重要！这里重要的是，它的作用域是这样的，你几乎可以在任何地方找到它，这在以后会有意义。

`numberOfSlidesBeingDisplayed`变量很简单，所以你不必在很多不同的地方改变数字，而是只在一个地方改变它。

从`contentWrapperElement`给`variableSliderWidth`一个数值，该数值等于`contentWrapperElement`的像素宽度，通过 offsetWidth 属性
计算得到

```
var sliderInterval = "";
let numberOfSlidesBeingDisplayed = 2;
let variableSliderWidth = contentWrapperElement.offsetWidth; 
```

Enter fullscreen mode Exit fullscreen mode

### 元素引用

这些变量只是对我们的 Html 元素的引用，这样我们就可以交互和监听发生的事件。

```
let contentWrapperElement = document.querySelector('.slider-content-wrapper');
    let sliderContainerElement = document.querySelector('.slider-container');
    let sliderItemElements = document.querySelectorAll('.slider-item');
    let sliderSizeRegulatorElement = document.querySelector('.slider-size-regulator'); 
```

Enter fullscreen mode Exit fullscreen mode

# 计算元素属性

现在我们已经得到了我们需要的 HTML 元素的引用，我们可以开始计算所需的属性了。

首先，我们将为我们的可视滑块`sliderContainerElement`提供一个动态宽度，我们通过使用我们的`contentWrapperElement`的默认宽度，也就是视窗宽度来实现。

使用属性“offsetWidth ”,我们可以为我们的`sliderContainerElement`提供与`contentWrapperElement`完全相同的宽度，然后我们连接一个包含“px”的字符串，使其成为一个像素值。

```
sliderContainerElement.style.width = (contentWrapperElement.offsetWidth) + `px`; 
```

Enter fullscreen mode Exit fullscreen mode

接下来，我们将运行 Html 元素的节点列表，特别是我们的 slider-item div，您的 HTML 现在应该看起来像这样。

```
<body>
    <div class="slider-content-wrapper">
        <div class="slider-container">
            <div class="slider-size-regulator">
                <div class="slider-item" style="background-image:url(asseimg/featuredimg1.jpg)"></div>

                <div class="slider-item" style="background-image:url(asseimg/featuredimg2.jpg)"></div>

                <div class="slider-item" style="background-image:url(asseimg/featuredimg3.jpg)"></div>

                <div class="slider-item" style="background-image:url(asseimg/featuredimg4.jpg)"></div>
            </div>
        </div>
    </div>
</body> 
```

Enter fullscreen mode Exit fullscreen mode

现在我们可以对我们的`sliderItemElements`变量进行 forEach 循环，并遍历我们的 HTML 元素的节点列表。

这就是我们的杂项变量发挥作用的地方，我们将使用存储在我们的`variableSliderWidth`中的数值，并将其除以存储在`numberOfSlidesBeingDisplayed`中的数字，然后我们希望将其与包含“px”的字符串连接起来，使其成为一个像素值，一旦计算出来，它将是单个滑块项目的宽度。

因为我们的`variableSliderWidth`变量从与`sliderContainerElement`相同的计算中得到它的宽度，所以可以安全地假设它们是一样宽的，因此我们可以简单地通过改变`numberOfSlidesBeingDisplayed`变量来控制我们想要显示的滑块项目的数量。

```
sliderItemElements.forEach((sliderItemElement) => {
        sliderItemElement.style.width = variableSliderWidth / numberOfSlidesBeingDisplayed + `px`; 
    }) 
```

Enter fullscreen mode Exit fullscreen mode

现在我们要计算我们的`sliderSizeRegulatorElement`的宽度，这在理论上可以是无限长的，并且因为我们之前谈到的 overflow:hidden，它不会创建一个水平滚动条。

为了使`sliderSizeRegulatorElement`足够宽以包含所有的滑动项而不换行，我们需要向它提供滑动项的数量以及它们的实际宽度

为了找到 slider-item 的实际宽度，我们将使用与 forEach 循环中使用的相同的计算方法，但是我们将把它放在括号中，只是为了强制首先进行计算，然后我们用它乘以`sliderItemElements` nodelist 的长度，这将给出所有 slider-item 的总宽度，这又变成了我们的`sliderSizeRegulatorElement`的宽度。

```
sliderSizeRegulatorElement.style.width = ((variableSliderWidth / numberOfSlidesBeingDisplayed) * sliderItemElements.length) + `px`; 
```

Enter fullscreen mode Exit fullscreen mode

# 函数、事件监听器和条件

好了，现在我们已经有了我们需要的 HTML 结构，CSS 的样式，以及通过 javascript 适应任何大小设备的计算，我们可以开始制作 slider slide 了！

我们将在我们的 DOMContentLoaded EventListener 的最底部定义一个名为`slideImagesByInterval`的函数，在这个函数中，我们将创建一个名为`sliderMovedDistance`的新的杂项变量，这个新的变量将计算单个 slider-item 的宽度，计算方式与我们之前的 forEach 完全相同，有了这个信息，我们现在知道我们要将 slider 向左移动多少，以使它移动的像素长度与单个 slider-item 的宽度完全相同，从而使它在任何类型的设备上完全动态

```
function slideImagesByInterval() {
    let sliderMovedDistance = variableSliderWidth / numberOfSlidesBeingDisplayed;
    }; 
```

Enter fullscreen mode Exit fullscreen mode

现在在同一个函数中，但是在我们刚刚创建的变量下面，我们想要开始一个区间。我们之前在一个所有代码都可以到达的范围内创建了一个变量，名为`sliderInterval`，我们现在要用一个实际的区间来覆盖其中的空字符串，这个区间将使滑块滑动

```
function slideImagesByInterval() {
        let sliderMovedDistance = variableSliderWidth / numberOfSlidesBeingDisplayed;

        sliderInterval = setInterval(() => {
        }, 2500)
    } 
```

Enter fullscreen mode Exit fullscreen mode

在 interval 函数中，我们将向我们的`sliderSizeRegulatorElement`添加一些 CSS 属性，我们希望滑块平滑地过渡，而不是被拉到一边，所以我们将使用 transition 属性，但我们也需要告诉它必须走多远和哪个方向，为此我们将使用 margin-left，具体来说是减去 margin-left。

通过使用我们在这个函数的顶部创建的变量，我们知道了一个滑动项有多宽，因此我们知道了我们想要负边距-向左多远到 ***拖动*** 容器，在这一点上我们需要做的就是连接“px”字符串，让它知道这是一个像素值

```
sliderInterval = setInterval(() => {
    sliderSizeRegulatorElement.style.transition = "all 0.3s";
    sliderSizeRegulatorElement.style.marginLeft = -sliderMovedDistance + 'px';
}, 2500); 
```

Enter fullscreen mode Exit fullscreen mode

此时，我们需要做的就是调用 DOMContentLoaded eventListener 中的函数来滑动滑块，但是为了在当前状态下工作，函数调用需要以这样的方式定位，即它所依赖的变量已经声明。我们需要调用计算`sliderSizeRegulatorElement`宽度的那条线下面的函数。

滑块现在滑动，但它不循环！它最终会用完所有的图片，并简单地在空的滑动条中滑动。让我们使滑块适当地循环。

为了实现滑块循环，我们将取出节点列表中的第一个滑块项，并在每次出现间隔时将其附加到节点列表的末尾。这样，我们将有一个无限的滑块，只是一直在循环。

为了让这一切发生，我们首先需要监听滑块转换何时结束，也就是说每次滑块移动滑块项目的宽度

```
sliderSizeRegulatorElement.addEventListener('transitionend', () => {
    sliderSizeRegulatorElement.style.transition = "unset";
}); 
```

Enter fullscreen mode Exit fullscreen mode

为了让下面的步骤生效，我们需要一些条件来处理 HTML 结构中可能出现的空白。

我们正在检查节点列表中的第一个元素的节点名是否为“#text”，如果是这样，那么我们希望将其从节点列表中删除，这是因为它阻止了幻灯片重新排列幻灯片以形成无限循环。

```
sliderSizeRegulatorElement.addEventListener('transitionend', () => {
    sliderSizeRegulatorElement.style.transition = "unset";
    if(sliderSizeRegulatorElement.childNodes[0].nodeName == "#text"){
        sliderSizeRegulatorElement.removeChild(sliderSizeRegulatorElement.childNodes[0]);
    };
}); 
```

Enter fullscreen mode Exit fullscreen mode

一旦我们从节点列表中删除了空白，节点列表的第一个索引现在将是一个滑块项，我们将声明一个新的变量，我们称之为`firstChild`，它将从节点列表中删除滑块项，并存储它。

```
sliderSizeRegulatorElement.addEventListener('transitionend', () => {
    sliderSizeRegulatorElement.style.transition = "unset";
    if(sliderSizeRegulatorElement.childNodes[0].nodeName == "#text"){
        sliderSizeRegulatorElement.removeChild(sliderSizeRegulatorElement.childNodes[0]);
        let firstChild = sliderSizeRegulatorElement.removeChild(sliderSizeRegulatorElement.childNodes[0]);
    }; 
```

Enter fullscreen mode Exit fullscreen mode

既然我们已经从节点列表中移除了第一个 slider-item 并将其存储在我们的`firstChild`变量中，我们将使用***insertAdjacentElement***方法在节点列表结束之前插入我们存储的 slider-item，这意味着在节点列表的最后，这将使 slider 无限循环。

同时，我们将把 margin-left 属性设置为 0。作为 If 语句的最后一项，我们将在底部输入`return;`

```
sliderSizeRegulatorElement.addEventListener('transitionend', () => {
    sliderSizeRegulatorElement.style.transition = "unset";
    if(sliderSizeRegulatorElement.childNodes[0].nodeName == "#text"){
        sliderSizeRegulatorElement.removeChild(sliderSizeRegulatorElement.childNodes[0]);
        let firstChild = sliderSizeRegulatorElement.removeChild(sliderSizeRegulatorElement.childNodes[0]);
        sliderSizeRegulatorElement.insertAdjacentElement('beforeend', firstChild);
        sliderSizeRegulatorElement.style.marginLeft = 0;
        return;
    }
}; 
```

Enter fullscreen mode Exit fullscreen mode

如果节点列表中的第一个节点名不是“#text”，这也需要工作，因此在 if 语句之后的 addEventListener 中，我们将复制 If 语句中执行存储和附加以及重置边距的代码。

```
let firstChild = sliderSizeRegulatorElement.removeChild(sliderSizeRegulatorElement.childNodes[0]);
sliderSizeRegulatorElement.insertAdjacentElement('beforeend', firstChild);
sliderSizeRegulatorElement.style.marginLeft = 0; 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经解决了空白区域的问题，我们已经让滑块无限循环，并且给了滑块一个间隔，我们可以继续最后的任务了！我们需要声明一个函数来根据视窗的大小改变变量`numberOfSlidesBeingDisplayed`。

我们将声明一个名为 **viewportWidth** 的函数，在该函数中声明一个名为`viewportWidth750`的变量，该变量将包含我们的 Javascript 媒体查询，它将帮助我们根据视窗宽度计算出要显示多少张幻灯片

在函数内部，基于我们刚刚创建的变量，我们将创建一个条件，规定如果视口小于 750 像素，则`numberOfSlidesBeingDisplayed`应设置为 1，使整个幻灯片适应并计算 slider-item 的适当大小以及幻灯片需要滑动到节点列表中下一个 slider-item 的适当距离。

如果视口高于 750 像素，进行相同的操作，但将`numberOfSlidesBeingDisplayed`设置为 3。

```
function viewportWidth(){
    let viewportWidth750 = window.matchMedia("(max-width: 750px)");
    if(viewportWidth750.matches){
        numberOfSlidesBeingDisplayed = 1;
        sliderContainerElement.style.height = 210+'px';
    }   
    else{
        numberOfSlidesBeingDisplayed = 3; 
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在剩下的是在正确的地方调用这个 **viewportWidth** 函数，所以我们将在我们制作的 Html 引用变量下直接调用它，看起来像这样

```
var sliderInterval = "";
let numberOfSlidesBeingDisplayed = 2;

let contentWrapperElement = document.querySelector('.slider-content-wrapper');
let sliderContainerElement = document.querySelector('.slider-container');
let sliderItemElements = document.querySelectorAll('.slider-item');
let sliderSizeRegulatorElement = document.querySelector('.slider-size-regulator');
let variableSliderWidth = contentWrapperElement.offsetWidth - 20;
viewportWidthDetection(); 
```

Enter fullscreen mode Exit fullscreen mode

## 这种方法的问题和不便

用硬编码的 HTML 制作这样的滑块有一些问题

*   它不太漂亮
*   它对用户不太友好
*   添加和删除图像非常耗时
*   因为它是一个节点列表，HTML 中的任何空白都将在节点列表中生成并索引一个[文本],所以我们仍然必须创建条件来识别它并处理它，以便滑块工作。