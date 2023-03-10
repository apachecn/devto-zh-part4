# 通过 JS 传递 CSS 的危害——对 CSS 转换和不一致性的探讨

> 原文：<https://dev.to/emnudge/the-hazards-of-passing-css-through-js-a-look-into-css-transform-and-inconsistencies-44n0>

我爱 CSS。这是我的孩子。我会永远珍惜 CSS，但是说实话。有点乱。

多年来，向后兼容和浏览器支持使得 CSS(和 JS，但这不是本文要讨论的)有点混乱。虽然不同的浏览器引擎不会总是影响一些 JS 的结果，只是影响速度，但 CSS 就不一样了。

不同的引擎有不同的处理风格。这是相当微妙的，但它最终会导致*真正的*在开发过程的后期恼人的错误。一个特别易变的 CSS 属性是 CSS `transform`。

它可以用来移动和操作元素。Yair Even 或制作了这个简洁的代码笔来看看变形有什么能力:【https://codepen.io/vsync/pen/RayMgz】T2

这有点奇怪，因为类似于[过滤器](https://codepen.io/EmNudge/pen/JxdpYq)属性，它并不真的只做*一个*的事情。它可以用于多种用途。它改变了他们。怎么会？这取决于你。

```
transform: translate(10px, 20px); /* moves to the x,y coords of (10, 20) */
transform: rotate(15deg); /* rotates 15 degrees clockwise */ 
```

Enter fullscreen mode Exit fullscreen mode

您可能经常会发现自己一次需要多个转换。像这样列出它们会覆盖前面的。我们可以将它们结合起来。

```
/* moves to the x,y coords of (10, 20) and rotates 15 degrees clockwise */
transform: translate(10px, 20px) rotate(15deg); 
```

Enter fullscreen mode Exit fullscreen mode

明白了吗？酷毙了。从这里只有上山的路...对吗？

# 好人

转换非常好，因为它们不需要任何基于布局的计算。[保罗·爱尔兰](https://www.paulirish.com/2012/why-moving-elements-with-translate-is-better-than-posabs-topleft/)为使用`left: 10px`和`position: absolute;`使用`transform: translate(10px, 10px)`超过绝对定位提供了一个很好的例子。

变换不仅性能更好，而且允许亚像素定位。您可以将元素移动到(20.3423，4.5294)的位置，而不会出现任何错误。你为什么想要这个？光标使用子像素移动。如果您想要任何拖动交互，使用绝对定位可能会进行所谓的“复制步进”我强烈推荐查看保罗·爱尔兰的文章以获得更多相关信息。

现在，尽管有好处，但`transform`的复杂本质导致了许多烦恼。其中一个烦恼是关于可怕的模糊。

# 模糊

由于变换在幕后的计算方式不同，所以元素在一天结束时的显示方式也会受到影响。在变换动画过程中，元素可能会经历轻微的模糊。由于我们处理的是子像素，这是可以理解的。很烦，但是可以理解。

可惜这种行为是*超*不一致的。不仅在浏览器之间，而且在几乎相同的环境中的同一浏览器内。Chrome 似乎是最大的罪魁祸首。很难再现模糊，但它通常会在变换动画中出现。我第一次注意到这一点是在一段时间前，当时我正在构思一个想法:一个麦克风比较网站。

你可以在这里看到早期的原型，但请注意麦克风盒在悬停时有所增长。Transform 是使用`transform: scale()`实现这种效果的唯一方法，虽然很简单，但是我遇到了一个问题。在一些浏览器和操作系统上，有时在动画过程中甚至动画结束后，框会变得模糊。

我最终想出了一个聪明的解决方案，在这个方案中，框最初是按比例缩小的，只有在悬停时大小才恢复正常。这将确保当鼠标悬停时，盒子不会停留在任何子像素上。如果它更小、更模糊，那也没什么大不了的——只是当它被放大时变模糊了。

虽然，这只是解决了大多数时候的问题。我很快就忘记了这一切，只是在做一个涉及[可拖动窗口](https://codesandbox.io/s/window-react-component-2zl5z)的项目时才想起这件事。

这就是问题所在。我们通过 JS 操作转换。

# 用 JS 操作 CSS

假设我们想将元素的填充比原来多 5px。假设这个填充是在一个类为`box`的元素上，我们可能会这样做:

```
const box = document.querySelector('.box');
const padding = parseInt(box.style.padding, 10);
box.style.padding = padding + 5 + 'px'; 
```

Enter fullscreen mode Exit fullscreen mode

我们得到盒子，得到它的填充 px 值作为一个数字，然后再返回 5，加上“px ”,这样它就知道我们使用的是哪种单元类型。有点多，但是可以忍受。

顺便说一下，我们正在使用一个关于`parseInt()`的技巧。使用`Number()`将返回一个`NaN`，因为它包含字母`px`。`parseInt()`将得到数字*，直到*变成一个字符串。只有当第一个字符不可解析时，它才会返回一个`NaN`。给它输入一个`px`字符串，直到`px`它才会给出数字。

现在让我们尝试在顶部和底部添加 10px，在两侧添加 20px。为什么？只是为了好玩。

```
const box = document.querySelector('.box');
const [paddingY, paddingX] = box.style.padding.split('  ').map(val => parseInt(val, 10));
box.style.padding = `${paddingY + 10}px ${paddingX + 20}px`; 
```

Enter fullscreen mode Exit fullscreen mode

哇哦。我们能够使用相同数量的线，但是现在它有点复杂。我们假设 CSS *已经*填充了 2 个值，用空格隔开，但也可能没有。我们可以在没有基数的情况下传递 parseInt，将映射缩短到仅仅`.map(parseInt)`，但是这是一种糟糕的做法。我们还使用了数组析构来节省更多的空间。

现在让我们继续讨论 CSS 转换。让我们处理一个非常真实的例子，将一个盒子向右移动 10.513 个像素，向上移动 3.422 个像素。对于使用 CSS 转换的可拖动框，我们会遇到这种情况。

```
const box = document.querySelector('.box');
const matchTranslate = new RegExp(/translate\(.+?\)/);
const translateStr = box.style.transform.match(matchTranslate)[0];
const translatePos = translateStr.slice(10, -1).split(',');
const [x, y] = translatePos.map(val => parseFloat(val, 10));
const newTransStr = `translate(${x + 10.513}px, ${y - 3.422}px)`
box.style.transform = translateStr.replace(matchTranslate, newTransStr); 
```

Enter fullscreen mode Exit fullscreen mode

哎哟。Regex？没错。我们可以用一些高阶函数来实现，但是 regex 会更快更清晰(Regex？清楚了吗？什么？).

因为 transform 属性可以包含多个转换，所以我们必须在不接触其他转换的情况下找到并返回我们的转换。我们的值也包含在`transform(${rightHere})`中，所以在我们使用`parseFloat`将它们解析成数字之前，我们必须挖掘一点来获取它们(与 parseInt 相同，但现在是针对小数)。变换处理子像素，记得吗？).

现在又来了一些挫折，这也是我坐下来写这篇文章的原因之一。不同的浏览器对转换做的一些事情在任何文档中都没有很好的描述。

# 可选值和 CSS 解析

你看，translate 的第二个值是可选的。仅添加一个 px 值有效。它只会在 x 方向移动。注意，这与`scale()`不同，它也有第二个选项值，但作用不同。

如果`scale()`的第二个值被忽略，它将在两个方向上成比例缩放。对于前面提到的`padding`这样的属性也是一样。留下一个值，它将在所有 4 个方向填充。然而`translate()`只会在 x 轴上移动。

有点奇怪，但没什么奇怪的，对吧？不完全是。我们之前讨论过奇怪的浏览器行为。这里还有一个要添加到堆栈上。

当在 JS 中操作 CSS 值时，浏览器可能会在使用它们之前对值进行一些清理。如果你把你盒子的`transform`设置为`translate(20px, 1px)`，当检索你的值时，你会发现所有那些空格都被压缩了。它将作为一个更干净的`translate(20px, 1px)`回来。

再次，[有点奇怪](https://bulbapedia.bulbagarden.net/wiki/Oddish_(Pok%C3%A9mon))，但现在还没什么可担心的。这就是有趣的地方。

因为只有一个值传递给`translate()`会在 X 方向移动它，如果我们传递它`translate(20px, 0px)`会发生什么？在这种情况下，它等同于`translate(20px)`。浏览器会清理吗？嗯，看情况。在 Chrome、Edge 和 Safari 上不会，但在 Firefox 上会。当我们的可拖动框位于位置(20，0)时，如果我们运行前面的代码会发生什么？应该没什么问题...对吗？

不幸的是，我们的代码被压缩了。我们的`y`值将是未定义的，我们做的任何数学运算都将产生一个`NaN`。这意味着我们的`y`位置从现在起每当它达到 0 时就会被卡住，但只在 FireFox 中。

由于这些信息并没有准确地列在表层的任何地方，找到这些信息是一件痛苦的事情。我们如何才能防止类似的事情在未来悄悄降临到我们身上？

# 解 01 -本地状态

最明显的一个可能是将所有变化的 css 值存储为 state。如果我们保存所有当前 css 值的本地状态，我们可以在需要时自动生成一个新的 css 字符串。

```
let skew = 0,
    translateX = 0,
    translateY = 0,
    rotate = 0;

function getTransform(skew, translateX, translateY, rotate) {
    return `skew(${skew}deg) translate(${translateX}px, ${translateY}px) rotate(${rotate}deg)`
} 
```

Enter fullscreen mode Exit fullscreen mode

这使得改变转换更加容易。我们不需要关心任何东西的位置或者浏览器返回什么。我们什么也没得到，只有每个场景。

现在碰巧的是，在我的具体例子中，我被禁止保留任何本地状态。细节无关紧要，但如果出现这种情况，我们就不会开心了。

让我们试试另一种方法

# 解 02 - CSS 变量

CSS 变量支持所有主流浏览器(当然不包括 IE)。原生 CSS 变量相对于预编译/转换的变量(比如来自 Sass 或更低版本的变量)的主要优点是 CSS 变量可以通过 JS 访问和修改。

我们可以在我们的`transform`例子中定义 CSS 变量如下。这个想法归功于 T2 的汤米·哈金斯。

```
.box {
  /* ...other styles */
  --pos-x: 0;
  --pos-y: 0;
  transform: translate(
    calc(var(--pos-x) * 1px),
    calc(var(--pos-y) * 1px)
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

使用这个语法，我们设置了两个 CSS 变量，分别名为`--pos-x`和`--pos-y`。这些变量通过`translate()`函数传递，并使用`calc()`转换成像素值。通过将这些值乘以 1px，我们可以有效地将它们从之前的值转换成像素。看，妈妈！没有 JS！

每当我们想要操纵位置时，我们只需编写一些 JS，如

```
const box = document.querySelector('.box');
const pos = { x: 20, y: 75 };
box.style.setProperty("--pos-x", pos.x);
box.style.setProperty("--pos-y", pos.y); 
```

Enter fullscreen mode Exit fullscreen mode

瞧啊。我们的箱子会移动的！我们不需要以任何奇怪的方式提取值，编写大量代码，或者在 JS 中处理任何单位转换。CSS 为我们做了大部分工作！

这可能是大多数情况下的最佳解决方案。当您想要开始操作许多值，并且切换到 CSS 来创建新变量变得有点麻烦时，问题就来了。虽然还没有完全实现，但 Houdini 正在努力全面引入 OM 类型的 CSS。

# 解决方案 03 - CSS 类型化对象模型

埃里克·比德尔曼(Eric Bidelman)写了一篇很好的文章。我强烈推荐阅读一下关于[谷歌开发者](https://developers.google.com/web/updates/2018/03/cssom)的文章。

本质上，它是 CSS 的一个对象 API。它让我们以一种更干净的方式与 CSS 交互。我们得到的不是字符串，而是根据我们得到和设置的内容组织起来的嵌套对象。
这里有一个直接来自 Eric 帖子的例子(经过一些编辑):

```
const box = document.querySelector('.box');
box.attributeStyleMap.set('padding', CSS.px(42));
const padding = el.attributeStyleMap.get('padding');
console.log(padding.value, padding.unit); // 42, 'px' 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们正在设置，然后得到填充。它有点冗长，但是现在我们正在做的事情已经*清晰多了*。我想用`transform`展示一个例子，但是现在对 CSSOM 的支持似乎太弱了。CSSOM 是 [Houdini](https://developers.google.com/web/updates/2016/05/houdini) 计划的一部分，从版本 66 开始只支持 Chrome。MDN 文档相当稀疏，它对包含多种样式选项的属性如`transform`和`filter`的使用相当差。暂时如此。

总的来说，胡迪尼的努力是了不起的，它的介绍是非常必要的。

好吧，虽然我们陷入了僵局，但还有另一个选择