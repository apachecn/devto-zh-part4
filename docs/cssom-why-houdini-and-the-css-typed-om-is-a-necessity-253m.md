# 为什么胡迪尼和 CSS 类型的 OM 是必要的

> 原文：<https://dev.to/emnudge/cssom-why-houdini-and-the-css-typed-om-is-a-necessity-253m>

让我们从基础开始。CSSOM 是什么？
CSSOM 中的 *OM* 代表对象 **M** 模型的**。这就是 JS 如何在 DOM 中与 CSS 接口。**

你可能熟悉 CSSOM，但可能不熟悉它的名字。就是这个:

```
const div = document.querySelector('div');
div.style.marginLeft = '20px';
div.style["padding-right"] = '5px';
div.style.transform = 'translate(20px, 10px)'; 
```

Enter fullscreen mode Exit fullscreen mode

看到了吗？CSS 被表示在一个对象模型中，这个对象模型让我们可以编辑值并实时看到它们的反映。我们可以使用好的 ole JS camel case ( `marginLeft`)，或者 CSS kebab case ( `margin-left`)来设置样式。
T3】

# 凌乱的 CSSOM

CSSOM 在大多数情况下都很好，但是任何长期使用它的人都知道，在有些情况下，最坏的一面会露出丑陋的一面。让我们创造一些这样的情景。

```
const div = document.querySelector('div');
div.style.position = 'relative';
div.style.left = '10' + (Math.random() > .5 ? 'px' : 'rem'); 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们已经将 div 向右移动了 10px 或 10rem。假设我们想将它向右移动 10 个额外的单位，而不考虑单位类型。

```
const num = parseFloat(div.style.left) + 10;
const unit = div.style.left.slice(String(num).length);
div.style.left = num + unit; 
```

Enter fullscreen mode Exit fullscreen mode

起初我使用一个`parseFloat`技巧来获取数字，但是当我们想要追加单元时，就变得有点棘手了。因为`rem`不像大多数其他单位值，它的长度是三，所以我们使用一种不同的方法来处理任何单位长度。

这是可行的，但是它肯定不干净或者不可预测。再来看一个案例。

```
const div = document.querySelector('div');
const randLightness = Math.floor(Math.random() * 50) + 30; // between 30 and 79
div.style.background = `hsl(100, 100%, ${randLightness}%)`; 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们检索颜色并获得它的亮度值。

```
const lightness = div.style.background.split(',')[2].slice(1, -1);
console.log('lightness: ' + lightness); // > "lightness: 0" 
```

Enter fullscreen mode Exit fullscreen mode

太奇怪了。我们放入 30% - 79%的明度，得到的是“0”。也没有百分比。我知道我的 JS 是对的。至少我是这样认为的。让我们控制台记录颜色，看看我们得到了什么。

```
console.log(div.style.background) // > "rgb(68, 204, 0)" 
```

Enter fullscreen mode Exit fullscreen mode

什么？我们放入一个`hsl()`格式。我们似乎正在找回一个代表`hsl(100, 100%, 40%)`的`rgb()`。如果我们想增加 10 倍的亮度，我们将不得不尝试一种与上次完全不同的方法。

# CSSOM 的危险一致性

我在我的的一篇[以前的文章中提到过这个问题，围绕着一个关于`transform: translate()`的火狐问题。](https://dev.to/emnudge/the-hazards-of-passing-css-through-js-a-look-into-css-transform-and-inconsistencies-44n0)

CSSOM 有一个规范，描述了它如何处理传递给它的值。如果可以，它会将任何颜色格式转换成`rgb()`。具体规定如下:

> 如果是解析值或计算值的组成部分，则使用 rgb()或 rgba()函数表示法返回颜色，如下所示

“如下”部分不是很有趣，除了它如何指定如果你传递给`rgb()`一个 alpha 参数，它将把它转换成`rgba()`。重要的是它如何总是以`rgb()`或`rgba()`格式返回颜色。

如果我们想用`hsl()`格式编辑一个属性，这是很可怕的。在编辑并发回之前，我们必须使用复杂的数学方法将其转换为`hsl()`。

避免很多麻烦的一个方法是使用一个 [CSS 变量](https://developer.mozilla.org/en-US/docs/Web/CSS/Using_CSS_custom_properties)来代替其中一个参数。这阻止了引擎解析它，因为那将导致非常不同的结果。

```
div.style.background = 'hsl(100, 100%, calc(var(--lightness) * 1%))';
const randLightness = Math.floor(Math.random() * 50) + 30; // between 30 and 79
div.style.setProperty('--lightness', randLightness); 
```

Enter fullscreen mode Exit fullscreen mode

**注意**:你必须使用`.setProperty`来编辑 CSS 变量。使用`style['--lightness']`在样式属性本身上设置它将不起作用。您可以通过记录`div.style.cssText`来验证这一点。

但是使用 CSS 变量似乎更像是一个补丁，而不是一个解决方案。CSS 本身看起来有点乱，如果第三方想要编辑我们的 CSS，他们必须识别 CSS 变量的存在，以便进行任何更改。

CSSOM 做了很多这样的改变。你可以去汤姆·哈金斯的网站看看有什么变化。这是一个神奇的小工具，让你看到引擎如何实时解析你的 CSS。它主要用于查看您的浏览器是否支持特定的功能，但这也是一个很好的用例。
**2019 . 11 . 21 编辑:**大约 5 分钟前，我们意识到他就是 CSSTOM Hodgins。请在以后的会面中这样称呼他。

当更改接受多个参数的 css 属性值时，这种 CSSOM 行为变得更加混乱。

# CSSOM 的危险清理

根据第 [6.7.2 节中的# 2.2-序列化规范](https://drafts.csswg.org/cssom/#serializing-css-values)中的 CSS 值:

> 如果组件值可以省略或替换为更短的表示，而不改变值的含义，则省略/替换它们。

如果可能的话，通过替换和省略部分来使 CSS 值变得更短。这在理论上似乎是个好主意，但是当传递可以缩短的值时，它们几乎总是会这样做。

使用上一节提到的[相同工具](https://tomhodgins.com/demo/cssom/)，我们可以验证`margin: 1px 1px;`在每个浏览器中都会被缩短为`margin: 1px;`，而`transform: translate(20px, 0px);`在 firefox 中只会被缩短为`transform: translate(20px);`。

这些缩写在所有浏览器中都有完全相同的功能，但有时只有一个浏览器会选择缩写。

由于规范的原因，FireFox 的这个问题被宣布为不是一个 bug，以回应关于 [Bugzilla](https://bugzilla.mozilla.org) 的报告。

> 如[规范]所述，我们可能会为了与其他引擎兼容而改变这一点，但我认为缺少破损的站点，壁虎行为更可取/更一致/更正确。

这意味着当我们从 CSSOM 获取值时，我们必须小心，它可能不包含我们最初传递给它的所有参数。这每次都会导致更多的逻辑和混乱的代码。

幸运的是，胡迪尼倡议旨在解决所有这些问题。它目前还不能解决我们所有的问题，但希望很快就能解决。
T3】

# 介绍 CSS 类型化 OM

就像 JS 有“字符串”、“数字”、“布尔”等类型一样，CSSOM 也有了自己的迷你类型系统。它们可通过全局`CSS`变量访问，用法如下:

```
const div = document.querySelector('div');
div.attributeStyleMap.set('margin-left', CSS.px(23));
const margin = div.attributeStyleMap.get('margin-left')
console.log('margin-left: ' + margin); // > "margin-left: 23px" 
```

Enter fullscreen mode Exit fullscreen mode

现在，这当然更加冗长，CSS 类型化 OM *也将*更加冗长，但是类型化系统要安全得多，类型通常如此。

访问样式的新方法是通过一个类似 [`Map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 的叫做`attributeStyleMap`的对象，而不仅仅是`style`。像 map 一样，它有所有常用的方法:get、set、has 等。它也是一个迭代器，所以在一个`for ...of`中是可循环的。

检索样式时，会进行一些格式化。您得到一个包含`value`和`unit`属性的对象。当调用`.toString()`方法时，您得到的是一个串联的字符串，而不是一个对象。连接 unit 对象将调用`.toString()`方法，这就是为什么上面的例子没有包含它。

这如何解决我们的问题？先说一个基本原则:
你投入的就是你得到的。它为我们做了大量的格式化工作，使我们的 CSS 有效，但我们仍然可以检索我们放入的内容。
我们来举个例子:

```
const div = document.querySelector('div');
div.attributeStyleMap.set('z-index', CSS.number(4.45143)); 
```

Enter fullscreen mode Exit fullscreen mode

我们这里有一个 z-index，它有一个非常特殊的 z-index。假设我们正在以一种非常特殊的方式制作 z-index 动画，那么这些小数位对我们来说非常重要。
CSSTOM 将为我们格式化并截断小数，但当我们稍后请求时，我们的小数将被保留

```
div.attributeStyleMap.get('z-index').values[0].value;  // -> 4.45143
div.computedStyleMap().get('z-index').value;           // -> 4 
```

Enter fullscreen mode Exit fullscreen mode

*注 1* :我们可以用`.computedStyleMap()`代替`.attributeStyleMap`得到解析后的值。

*注 2* :我用了`.values[0].value`而不是`.value`，因为在写这篇文章的时候，似乎有一个错误，CSSTOM z-index 中的十进制值会生成一个 CSSMathSum 对象。

这种行为在将来(我从 [Tab Atkins Jr.](https://github.com/tabatkins) 那里得到了证实)也会延伸到颜色上。该值仍将被解析为`rgb()`格式，但是我们将能够检索我们的`hsl()`表示。

转换函数也更有组织性。此外，我们将获得一个单独的`translate`和`scale` css 属性，以便将来更好地访问。

# CSSTOM 现在

在撰写本文时，CSS 类型的 OM 仅在 Chrome (Chromium、Opera too 和 soon Edge)上独家提供。规范本身仍在编写中，所以我们可能还需要一段时间才能看到完整的实现。

在我看来，CSSTOM 是唯一真正解决问题的胡迪尼倡议。其他特性也非常棒，但是更多的是添加而不是修复。CSSTOM 不能再早了。