# 使用 CSS Paint (Houdini) API 生成形状和图像

> 原文：<https://dev.to/jamessouth/generating-shapes-and-images-with-the-css-paint-houdini-api-29c>

有了新的 CSS 画图 API(又名胡迪尼，大概是以梅尔文的专辑命名的🤘😁)，我们可以使用大多数的 [HTML 画布绘制方法](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial)来绘制图像或形状，并在任何获取图像的 CSS 属性中使用它。今天我想展示我是如何在我新重建的[作品集网站](https://jamessouth.github.io/portfolio/) *中使用 Houdini 来生成边框图像和气泡状 div 的。我还将介绍如何使用[polyfill](https://github.com/GoogleChromeLabs/css-paint-polyfill)，如何将 Houdini 与 webpack 和 Babel 结合使用，以及在制作下面的演示时遇到的困难。
* <small>当前未多填充-除非您使用 Chrome</small> 查看，否则您将看到一个回退

使用 Houdini 的基础如下:对于任何接受图像的 CSS 属性，比如 background-image，输入`paint(workletName)`作为值。在 JS 文件中，为您的 worklet 创建一个 ES6 类。在同一个文件中，用`workletName`和类名作为参数调用`registerPaint`方法。然后，在您的主 JS 文件或 webpack 入口点中，对`CSS.paintWorklet`进行特性检测。如果有，现在只有 Chrome，打电话给`CSS.paintWorklet.addModule('./myWorkletClassFile.js')`；否则，在`npm i -S css-paint-polyfill`之后，我们可以动态导入 polyfill，这样它将是一个单独的 webpack 块，然后调用`addModule`。现在我们准备开发我们的类，并生成一些艺术！本文回购:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [詹姆斯·索恩](https://github.com/jamessouth) / [绘画演示](https://github.com/jamessouth/paint-demo)

### 关于 css paint api 的第一篇文章的报告

<article class="markdown-body entry-content container-lg" itemprop="text">

# 绘画演示

我的文章中关于新的 [CSS Paint (Houdini) API](https://dev.to/jamessouth/generating-shapes-and-images-with-the-css-paint-houdini-api-29c) 的演示。

[演示 1](https://jamessouth.github.io/paint-demo/demo1.html)

[演示 2](https://jamessouth.github.io/paint-demo/demo2.html)

[演示 3](https://jamessouth.github.io/paint-demo/demo3.html) (仅限 Chrome)

[演示 4](https://jamessouth.github.io/paint-demo/demo4.html)

</article>

[View on GitHub](https://github.com/jamessouth/paint-demo)

<figure>

```
//(simplified) index.js, webpack entry point
import '../css/demo.scss';

if (CSS.paintWorklet) {
  //not imported so won't be picked up by webpack, which is ok
  CSS.paintWorklet.addModule('./demo.min.js');
} else {
  import(/* webpackChunkName: "css-paint-polyfill" */ 'css-paint-polyfill').then(() => {
    CSS.paintWorklet.addModule('./demo.min.js');
  });
} 
```

<figcaption>[index.js](https://github.com/jamessouth/paint-demo/blob/master/src/js/index.js)</figcaption>

</figure>

## 生成页面的背景

<figure>[![Demo 1](img/c0f86e870074c91747acfa6c53865753.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iLpvZJKJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jamessouth/paint-demo/mastimg/demo1.jpg) 

<figcaption>演示 1</figcaption>

</figure>

所以让我们从生成页面的背景开始。Demo 1 是[住在这里](https://jamessouth.github.io/paint-demo/demo1.html)。

<figure>

```
 <!--index1.html -->
  <body>
    <div class="bg">
      <div>a</div>
      <div>b</div>
      ...
      <div>n</div>
    </div>
  </body>
<!--a fallback for no support or polyfill, then calling paint with demo1 worklet-->
  <style>
    .bg{
      background-image: linear-gradient(black, black);
      background-image: paint(demo1);
    }
  </style> 
```

<figcaption>[index1.html](https://github.com/jamessouth/paint-demo/blob/master/src/html/index1.html)</figcaption>

</figure>

通常我不会在 HTML 中有一个`<style>`标签，但是当在 SCSS 进行`paint`声明时，至少对于运行在页面加载上的 worklets 来说，会有一些缓存问题。要明白我的意思，请查看 Chrome 中的[演示 4](https://jamessouth.github.io/paint-demo/demo4.html)——每次点击重新加载时，页面都无法加载😢。

我认为同样的缓存问题也给我在 Firefox 上带来了问题——如果没有打开开发工具和禁用缓存检查，polyfill 几乎不会运行😭。这可能是由于我设置演示回购的方式，或 GitHub 上的托管。在我的测试中，polyfill 在 Edge 和 Safari 上相当可靠。

当聚合填充不运行时，您将在几何体上看到背景色，而不是渐变回退。聚合填充通过创建一个图像来工作，因此如果您调整大小或重新定向，您将得到页面加载时形成的原始图像的重复或截断。通过将`background-repeat`设置为`no-repeat`可以防止重复；你也会看到身体的背景颜色。由于 Chrome 有一些对 Houdini 的原生支持，当你调整大小或重定向时，worklet 会再次运行并重新绘制以适应新的尺寸，所以如果你编写一个复杂的 paint 函数，请注意这一点。

演示 1 中的假占位符内容在一个 div 中，它将保存绘制的背景并覆盖页面。这是 Chrome 中的[错误](https://bugs.chromium.org/p/chromium/issues/detail?id=808908)的变通方法，它破坏了`body`(显然还有`html`和`:root`)上设置的 CSS 自定义属性，至少在画图小工具中访问它们时是这样。剩下的 CSS 是:

<figure>

```
//demo1.scss
.bg{
  --stars: 522;
  min-height: 100vh;
  width: 100%;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
}
.bg div{
  width: 200px;
  height: 200px;
  background-color: coral;
  margin: 1em auto;
  text-align: center;
  font-size: 3em;
} 
```

<figcaption>[demo1.scss](https://github.com/jamessouth/paint-demo/blob/master/src/css/demo1.scss)</figcaption>

</figure>

Chrome bug 的第二个解决方法是在主体上使用伪内容，但是 polyfill 不起作用。第三种解决方法是在 body 上设置自定义属性，然后在 worklet 中测试道具的存在。如果没有(Chrome 就是这种情况)，设置一个默认值。不管怎样，让我们开始工作吧！

在我们的 worklet 类中，我们可以创建静态的帮助器方法用于`paint`方法，在那里我们进行绘图。`paint`方法有 1 到 4 个参数:

*   调用绘图方法的画布上下文(`ctx`)
*   你正在绘制的元素的尺寸，我们可以将其析构为`{ width, height }`
*   `props`，允许您访问 CSS 自定义属性
*   一个`args`数组，保存从 CSS 中调用画图小工具时传入的参数，就像`paint(workletName, arg1, arg2, ...)`

到目前为止，任何地方都不支持 args😭。胡迪尼准备好了吗？进行更新。

<figure>

```
//demo1.js
class Demo1 {
  static get inputProperties() { return ['--stars']; }//access CSS custom property

  static getWidth() {
    return Math.floor(Math.random() * 10) + 1;
  }

  //other static methods omitted but they all just return random numbers

  paint(ctx, { width, height }, props) {
    const stars = props.get('--stars');//use CSS custom property

    ctx.fillStyle = 'rgb(0, 0, 0)';
    ctx.fillRect(0, 0, width, height);//cover all of element

    for (let i = 0; i < stars; i += 1) {//populate stars on black background
      ctx.fillStyle = `rgba(255, 255, 255, ${Demo.getTransparency()})`;//hsla not working in Edge
      ctx.fillRect(Demo.getDistAlongSide(width), Demo.getDistAlongSide(height), 1, 1);
    }

    const ctr = [//all lines start halfway across, just below bottom
      width / 2,
      height + 2,
    ];

    for (let i = 0; i < 700; i += 1) {
      const startSide = i % 4;
      const endSide = (startSide + Demo.getEndSide()) % 4;
      ctx.beginPath();
      ctx.moveTo(...ctr);//spread coordinates as two args
      ctx.lineTo(...Demo.getPoint(endSide, width, height));
      ctx.lineWidth = Demo.getWidth();
      ctx.lineCap = 'square';
      ctx.strokeStyle = `rgba(${Demo.getColor(240, 15)}, ${Demo.getColor(240, 15)}, ${Demo.getColor(50, 20)},
        .05)`;//use high opacity for a Roy Lichtenstein/pop-art look
      ctx.stroke();
    }
  }
}
registerPaint('demo1', Demo1);//called with worklet name and class name 
```

<figcaption>[demo1.js](https://github.com/jamessouth/paint-demo/blob/master/src/js/demo1.js)</figcaption>

</figure>

现在我们可以开始构建了！据我所知， [Worklet 接口](https://developer.mozilla.org/en-US/docs/Web/API/Worklet)只接受 ES6 类，所以 trans piled-to-ES5-function Worklet 不起作用，webpack 包装在函数中的类也不起作用(如果有办法在 webpack 中缩小，请回答[我关于堆栈溢出的问题](https://stackoverflow.com/questions/57402682/how-to-just-minify-and-nothing-else-a-js-worklet-file-in-webpack))。所以，我一直在 webpack 之外处理它们。这工作得很好，但是使得开发中的迭代有点慢。安装 [`babel-minify`](https://www.npmjs.com/package/babel-minify) 包作为开发依赖项，然后在`package.json`中缩小你的 worklet 文件并把它们放在你的`/dist`文件夹:

<figure>

```
 //package.json
  "scripts": {
    "lint": "eslint ./src/js",
    "dev": "webpack-dev-server",
    "build": "webpack",
    "prebuild": "rm -f dist/demo1.min.js && npx minify src/js/demo1.js --out-file dist/demo1.min.js..."
// && minify the other worklets etc...
  } 
```

<figcaption>[package.json](https://github.com/jamessouth/paint-demo/blob/master/package.json)</figcaption>

</figure>

在我的 webpack 配置中，我使用`CleanWebpackPlugin`并删除所有内容，除了缩小的 worklet 文件:

<figure>

```
 //webpack.config.js
  plugins: [
    new CleanWebpackPlugin({ cleanOnceBeforeBuildPatterns: [
      '**/*',
      '!demo1.min.js',
      //other worklet files...
    ] }), 
```

<figcaption>[webpack.config.js](https://github.com/jamessouth/paint-demo/blob/master/webpack.config.js)</figcaption>

</figure>

为了开发 worklet，我将它移动到`/dist`文件夹中，并将其命名为`demo1.min.js`,因为这是我在其他地方使用的名称。现在，当我启动`webpack-dev-server`时，`/dist`被清除，除了工作流，开发工作流是正常的，除了必须手动刷新浏览器以反映工作流的变化。当我完成时，我将工作流移回源代码(重命名为`demo1.js`)并构建用于生产。预构建脚本将缩小工作小程序，webpack 将处理剩下的工作！

## 生成边框图像

<figure>[![Demo 2](img/fc3297114fb355eb3aa1b98d9b103d68.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--e88XKSz_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jamessouth/paint-demo/mastimg/demo2.jpg) 

<figcaption>试玩 2</figcaption>

</figure>

演示 2 [(现场)](https://jamessouth.github.io/paint-demo/demo2.html)与演示 1 的结构相似，只是一些虚拟内容:

<figure>

```
 <!--index2.html-->
  <body class="border">
    <div>a</div>
    <div>b</div>
    <div>c</div>
    <div>d</div>
  </body>
  <style>
    .border div{
      border-image-source: linear-gradient(blue, blue);
      border-image-source: paint(demo2);
    }
  </style> 
```

<figcaption>[index2.html](https://github.com/jamessouth/paint-demo/blob/master/src/html/index2.html)</figcaption>

</figure>

它的风格类似于演示 1，除了我们使用胡迪尼来生成[边界图像](https://developer.mozilla.org/en-US/docs/Web/CSS/border-image) :

<figure>

```
//demo2.scss
.border{
  min-height: 100vh;
  width: 100%;
  background-color: #efefef;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(284px, 1fr));
}
.border div{
  width: 200px;
  height: 200px;
  background-color: coral;
  margin: 1em auto;
  text-align: center;
  font-size: 3em;
  --lines: 343;
  border-image: 82 / 42px / 14;//short for border-image-slice, -width, -outset
  border-style: solid;
} 
```

<figcaption>[demo2.scss](https://github.com/jamessouth/paint-demo/blob/master/src/css/demo2.scss)</figcaption>

</figure>

只是画一堆线，形成我们设置为边界图像源的图像:

<figure>

```
//demo2.js
class Demo2 {
  static get inputProperties() { return ['--lines']; }//CSS custom prop

  //static methods omitted but they all just return random numbers

  paint(ctx, { width, height }, props) {
    const lines = props.get('--lines');

    for (let i = 0; i < lines; i += 1) {
      const start = Demo2.getRandomPoint(width, height);
      const end = Demo2.getRandomPoint(width, height);
      ctx.beginPath();
      ctx.moveTo(...start);
      ctx.lineTo(...end);
      ctx.lineWidth = Demo2.getWidth();
      ctx.lineCap = 'square';
      ctx.strokeStyle = `rgba(${Demo2.getColor(36, 150)}, ${Demo2.getColor(108, 150)}, ${Demo2.getColor(12, 200)}, ${Demo2.getTransparency()})`;
      ctx.stroke();
    }
  }
}
registerPaint('demo2', Demo2); 
```

<figcaption>[demo2.js](https://github.com/jamessouth/paint-demo/blob/master/src/js/demo2.js)</figcaption>

</figure>

在我的[文件夹网站](https://jamessouth.github.io/portfolio/)中，我只对焦点样式和底部边框使用了这种技术；这里是 [SCSS](https://github.com/jamessouth/portfolio/blob/master/src/css/main.scss#L170) 和[的小工作台](https://github.com/jamessouth/portfolio/blob/master/src/js/BorderPaint.js)。

## 生成任意形状的元素

<figure>[![Demo 3](img/e8bf49c3c59aaf37763da6b05e457d1e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_L1bYzTT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/jamessouth/paint-demo/mastimg/demo3.jpg) 

<figcaption>演示 3。蓝色的云，它的轮廓，和感叹号被遮罩，红色/黄色/白色爆炸被绘制，背景是一个 CSS 圆锥形渐变。</figcaption>

</figure>

我们可以使用 Houdini 用 [`mask-image`](https://developer.mozilla.org/en-US/docs/Web/CSS/mask-image) 属性将 div 雕刻成任何形状(这里是我作品集的[语音泡泡工具包](https://github.com/jamessouth/portfolio/blob/master/src/js/ButtonPaint.js))。当然，我们这样做的任何元素在 CSS 盒子模型中仍然会占据一个矩形，但是在它的盒子中，我们可以实现我们想要的任何外观。对于第三个演示，我有点疯狂:我重新创建了 [POP！美国艺术家](https://www.imageduplicator.com/sat/sat_big_image.php?image_name=images/works/3794_01.jpg)[罗伊·利希滕斯坦](https://lichtensteinfoundation.org/biography/)为 1966 年 4 月 25 日出版的*新闻周刊*封面制作的爆炸石版画。这个只能在 Chrome 中使用，因为 polyfill 似乎不喜欢在一个样式表中有多个`paint`值，但是你可以使用多个样式表，每个样式表调用一个`paint`来解决这个问题。Demo 3 是[住在这里](https://jamessouth.github.io/paint-demo/demo3.html)。

<figure>

```
 <!--index3.html -->
  <body class="shape">
    <div id="pop">
      <p><span>P</span><span>O</span>P</p>
    </div>
    <div id="expt"></div>
    <div id="blue"></div>
    <div id="outline"></div>
  </body>
  <style>
    #expt{
      -webkit-mask-image: paint(demo3b);
      mask-image: paint(demo3b);
    }
    #pop{
      background-image: paint(demo3a);
    }
    #blue{
      -webkit-mask-image: paint(demo3c);
      mask-image: paint(demo3c);
    }
    #outline{
      -webkit-mask-image: paint(demo3d);
      mask-image: paint(demo3d);
    }
  </style> 
```

<figcaption>[index3.html](https://github.com/jamessouth/paint-demo/blob/master/src/html/index3.html)</figcaption>

</figure>

云与轮廓的剩余 CSS:

<figure>

```
//(partial) demo3.scss
#blue{
  background-color: #f5f9fc;
  background-image: radial-gradient(#1b4282 30%, #122182 32%, #1a237e12 33%),
                    radial-gradient(#1b4282 30%, #14248e 32%, #1a237e08 33%);
  background-size: 8px 8px;
  background-position: 0 0, 4px 4px;
}
#outline{
  background-color: #282a2c;
} 
```

<figcaption>[demo3.scss](https://github.com/jamessouth/paint-demo/blob/master/src/css/demo3.scss)</figcaption>

</figure>

就 worklet 代码而言，遮罩和绘制之间几乎没有区别。对于[感叹号](https://github.com/jamessouth/paint-demo/blob/master/src/js/demo3b.js)和[云轮廓](https://github.com/jamessouth/paint-demo/blob/master/src/js/demo3d.js)，两种方式都有效，因为它们都是纯色。[红/黄/白爆炸](https://github.com/jamessouth/paint-demo/blob/master/src/js/demo3a.js)也是实心的，但是我画它是为了应用黑色轮廓；似乎不可能既遮蔽一个形状又在它周围有一个轮廓，这就是为什么云的轮廓是一个单独的工作件。

我试着在工作图中绘制蓝色的云，但是看起来不太好。嵌套循环可以用来绘制主题元素的宽度和高度，但是我没有找到一种方法将绘制的内容限制在云的边界内。我还尝试了 [ctx.createPattern()](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D/createPattern) 方法，但是我找不到一种方法将一个图像放入 worklet(没有 DOM 访问)作为模式源。因此，我在 CSS 中制作了云的图案，然后在一个小工具中遮盖了形状，因此需要另一个小工具来制作轮廓。

为了帮助绘制这些形状，我使用了[这个工具](http://www.victoriakirst.com/beziertool/)，它生成绘制指令并添加 x 和 y 偏移，然后我用它来定位 div 中的形状。

## 多上多下和结论

我想用 polyfill 展示的最后一件事是，在其他声明之前调用 CSS 中的`paint`似乎比把它放在任何地方都更好。我制作了[演示 4](https://jamessouth.github.io/paint-demo/demo4.html) 来展示前面提到的当`paint`在 SCSS 被调用时的缓存问题，同时也是为了说明如果其他声明出现在它之前，polyfill 似乎不会经常运行，所以你只能得到主体的背景颜色。这篇文章的封面图片是演示 4 的一个镜头。

<figure>

```
//demo4.scss
.cache{
  //put paint declaration first for best results
  background-image: linear-gradient(black, black);
  background-image: paint(demo4);
  --rays: 582;
  min-height: 100vh;
  width: 100%;
} 
```

<figcaption>[demo4.scss](https://github.com/jamessouth/paint-demo/blob/master/src/css/demo4.scss)</figcaption>

</figure>

在我发现这种放置有所不同之前，需要像`display: block`这样看似随机的声明(尽管这已经是 div 的默认设置)来运行 polyfill😖😕😵🤦‍♂️.

* * *

胡迪尼是新兴技术，有越来越多的浏览器支持 T2，但是我们已经可以用我们的背景、边框和 div 做很多很酷的事情。能想象出来就能画出来！我希望这篇文章对你有所帮助，并能激发你使用`paint`来推进你的前端创造力。请留下评论，广泛分享！谢谢大家！