# 使用 Javascript ES6 生成器可视化流程

> 原文：<https://dev.to/elliot/visualizing-process-with-javascript-es6-generators-31fo>

无数的时间被投入到设计在几分之一秒内运行的功能上。当函数执行如此之快时，它们巧妙的实现不容易被欣赏。让我们让他们慢下来，花必要的时间看他们工作。

在本文中，我将介绍生成器函数的基础知识，以及如何使用它们来可视化函数的过程。

# 什么是发电机功能？

生成器函数对 JavaScript 来说是新的，许多人一直在努力寻找它们在现实世界中的实际用途。我很高兴向你展示一个很酷的使用方法，但是让我们先来看一下基本的。这里有一个简单的生成器函数:

```
function * myGeneratorFunction(arg) {
        yield 1;
        yield arg;
} 
```

Enter fullscreen mode Exit fullscreen mode

它看起来很像一个普通的函数，除了两个不同之处:在`function`后面有一个星号(*)，以及使用了`yield`语句。

下面是 myGeneratorFunction 的使用方法:

```
const generator = myGeneratorFunction('hello world');

console.log(generator.next().value)
// logs out 1

console.log(generator.next().value)
// logs out 'hello world' 
```

Enter fullscreen mode Exit fullscreen mode

调用生成器函数不会立即执行它，而是返回一个生成器对象。在 Generator 对象上调用`.next()`会导致 myGeneratorFunction 执行到第一个 yield 语句，返回 yield 关键字之后出现的值。生成器允许我们停止和开始一个函数的执行。查看生成器上的 [MDN 页面了解更多信息。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*)

# 为什么要可视化一个函数的过程？

可视化一个函数的过程有助于理解它的实现，并且可以产生引人入胜的动画和令人印象深刻的效果。以这个视频可视化各种排序算法为例:

[https://www.youtube.com/embed/kPRA0W1kECg](https://www.youtube.com/embed/kPRA0W1kECg)

该视频展示了流程可视化为何如此出色:

*   观看分类的过程非常迷人。
*   每种排序算法工作方式的差异是显而易见的。
*   除了让工作方式看起来很酷之外，还有什么更好的方式让人们对某样东西的工作方式感兴趣呢？

# 我们来观想一下！

现在的计算机运行得荒唐可笑，比乌塞恩·博尔特还快，快得令人难以置信。这意味着函数运行速度一样快。有了发生器，我们可以减慢函数过程，使它以每秒 60 步的速度运行。在这种速度下，我们可以实时观察一个函数做它最擅长的事情。这就像以慢动作观看世界上最快的短跑运动员，看到单个肌肉在一步中收缩和放松。

对于我们的例子，让我们无耻地复制上面的 youtube 视频，并用条形图可视化*插入排序*算法。下面是我们需要的两段代码。一个用于基本算法，一个用于绘制条形图。完成这些代码后，我们将看到如何轻松地将它们组合在一起。

这是基本的*插入排序*算法实现:

```
function insertionSort(inputArray) {
    for (let i = 0; i < inputArray.length; i++) {
        const value = inputArray[i];

        let j = i - 1;
        while (j >= 0 && value < inputArray[j]) {
            inputArray[j+1] = inputArray[j];
            j -= 1;
        }
        inputArray[j+1] = value
    }
    return inputArray;
} 
```

Enter fullscreen mode Exit fullscreen mode

下面我们有一个函数，在画布上以条形图的形式绘制一个数组。我使用 2d 画布 API:

<figure>

```
const c = document.getElementById('canvasEl');
const ctx = c.getContext('2d');

function drawGraphFromArray(array) {
    ctx.clearRect(0,0,c.width,c.height);
    const barWidth = c.width / array.length;
    const barHeightScale = c.height / Math.max(...array);

    array.forEach((value, i) => ctx.fillRect(
        i * barWidth,
        0,
        barWidth,
        barHeightScale * value
    ));
} 
```

Enter fullscreen mode Exit fullscreen mode

<figcaption>draws a bar graph where x axis: index in the array, y axis: value at index</figcaption>

</figure>

现在回到我们的常规节目。为了降低插入排序函数的速度，我们将把它重写为一个生成器函数。听起来很棘手，对吧？其实是 tricky 的反义词，超级简单。这是重写后的插入排序:

```
function * insertionSort(inputArray) {
    for (let i = 0; i < inputArray.length; i++) {
        const value = inputArray[i];

        let j = i - 1;
        while (j >= 0 && value < inputArray[j]) {
            inputArray[j+1] = inputArray[j];
            j -= 1;
            yield inputArray;
        }
        inputArray[j+1] = value
    }
    return inputArray;
} 
```

Enter fullscreen mode Exit fullscreen mode

只有两个变化。我们在 function 关键字后添加了一个`*`,并且每当我们想要在动画中绘制一帧时添加一个`yield`语句，从而产生正在排序的数组。通过这些简单的更改，我们已经将一个函数转换成了一个生成器函数，它一次执行一个步骤，并生成可视化其过程所需的数据。这种重写很棒，因为它是无干扰的——转换几乎不会影响函数的逻辑。

现在让我们把`drawGraphFromArray`和我们新的`insertionSort`生成器函数放在一个`requestAnimationFrame`渲染循环中。

```
// code from above ...

const randomArr = Array(50).fill(0).map(Math.random);
const sortGenerator = insertionSort(randomArr);

function renderLoop() {
    const yieldedArray = sortGenerator.next().value;

    drawGraphFromArray(yieldedArray);

    requestAnimationFrame(renderLoop);
}
renderLoop(); 
```

Enter fullscreen mode Exit fullscreen mode

产生了我们的动画成品:

[https://codepen.io/vez/embed/MdMxKN?height=600&default-tab=result&embed-version=2](https://codepen.io/vez/embed/MdMxKN?height=600&default-tab=result&embed-version=2)

在上面的最后一个动画中，我们看到条形图从参差不齐的混乱变成了美丽的楼梯。为了实现这一点，我们要求我们的插入排序使用`.next()`在每个渲染循环中一步完成。确保我们的渲染循环以每秒 60 帧的速度运行，这是流畅动画的完美速度。

插入排序是我们能做的一个简单的例子...

### 本文原载于 http://elliot.website/a/?[我的博客使用% 20ES6 %生成器可视化% 20 进程% 20](http://elliot.website/a/?Visualizing%20Process%20with%20ES6%20Generators)。请查看一些关于使用生成器进行创造性编码的额外内容。

感谢阅读。你创造性地使用过发生器函数吗？