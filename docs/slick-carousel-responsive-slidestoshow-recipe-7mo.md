# 光滑的转盘响应幻灯片显示食谱

> 原文：<https://dev.to/jimfrenette/slick-carousel-responsive-slidestoshow-recipe-7mo>

设计通常需要比演示提供的更多的东西，这是可以理解的。圆滑的传送带演示将带你了解大部分内容，并提供各种设置的例子。这篇文章是一个如何在调整视窗大小时动态更新光滑值的例子，[演示](https://jimfrenette.com/demo/slides-to-show/)。

### 入门

在本教程中，我们将使用宗地 web 应用程序 bundler 编译所有源代码并将其加载到开发 web 服务器中。我们所有的标记、JavaScript 和 CSS 都将在名为 src 的子目录中创建，src 是 source 的缩写。在项目根目录下，创建`src`文件夹。然后，用下面的 HTML 在`src`文件夹中创建一个`html`文件。这个标记包含 4 个用于轮播的幻灯片内容容器。内容容器包括 FPO 图像和标题。

##### index.html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    Responsive slidesToShow | Slick Carousel Demo
    <link rel="stylesheet" type="text/css" href="index.css">
</head>
<body>
    <main>
        <div class="carousel">
            <div>
                <div class="slide-content">
                    <h3>First Slide</h3>
                    <img src="https://via.placeholder.com/260x200/9932CC/FFFFFF" />
                </div>
            </div>
            <div>
                <div class="slide-content">
                    <h3>Second Slide</h3>
                    <img src="https://via.placeholder.com/260x200/7FFFD4/000000" />
                </div>
            </div>
            <div>
                <div class="slide-content">
                    <h3>Third Slide</h3>
                    <img src="https://via.placeholder.com/260x200/87CEFA/000000" />
                </div>
            </div>
            <div>
                <div class="slide-content">
                    <h3>Fourth Slide</h3>
                    <img src="https://via.placeholder.com/260x200/F4A460/000000" />
                </div>
            </div>
        </div>
    </main>
    <script src="index.js"></script>
</body>
</html> 
```

> 使用 VS 代码中内置的[的](https://code.visualstudio.com/docs/editor/emmet) [Emmet](https://emmet.io/) ，您可以通过在第一行输入感叹号然后选择 tab 键来创建`index.html`内容。

### 光滑的旋转木马

因为我们使用 [npm](https://www.npmjs.com/) 来安装 slick carousel 及其依赖项，所以在项目根中，运行带有`-y`标志的`npm init`来生成一个带有默认设置的空项目。

```
npm init -y 
```

安装光滑的传送带。

```
npm install slick-carousel 
```

Slick carousel 依赖于 jQuery，我们安装一下吧。

```
npm install jquery 
```

在项目中创建一个`src/index.css`文件，如下所示。注意，我们正在从 npm 安装 slick 的文件夹`node_modules`中导入 slick 样式表。当我们稍后运行构建时，这些导入的样式表将与下面的`index.css`一起编译成一个样式表，以减少 web 应用程序发出的 http 请求。

##### index.css

```
/* node_modules */
@import "../../node_modules/slick-carousel/slick/slick.css";
@import "../../node_modules/slick-carousel/slick/slick-theme.css";

body {
  margin: 0;
}

main {
  max-width: 1440px;
  margin: auto;
  padding: 10px;
  background: #cfcfcf;
}

.carousel {
  margin-right: -10px;
}

.slide-content {
  display: inline-block;
  background: #fff;
  padding: 15px;
  text-align: center;
}

@media screen and (min-width: 980px) {
  main {
    padding: 50px;
  }

  .carousel {
    max-width: 980px;
    margin: auto;
  }
} 
```

这是我们的 JavaScript 代码。将其添加到一个`src/index.js`文件中。

##### index.js

```
import $ from 'jquery'
import 'slick-carousel'

(function () {

  var slidesToShow = 1.1,
      slideWidth = 290,
      slideSpacing = 30;

  var $el = $('.carousel');

  init();

  function init() {
    $el.on('init', (event, slick, currentSlide) => carouselInit(event, slick));

    $el.slick({
      arrows: false,
      dots: true,
      infinite: false,
      slidesToScroll: 1,
      slidesToShow: slidesToShow,
      mobileFirst: true,
      responsive: [{
        breakpoint: 980,
        settings: {
          arrows: true,
          slidesToShow: 3
        }
      }]
    });

    $el.css('padding-left', slideSpacing / 2 + 'px');

    var resizeTimeout;
    $(window).on('resize', () => {
      clearTimeout(resizeTimeout);
      resizeTimeout = setTimeout(setSlidesToShow(), 500);
    })
  };

  function carouselInit(event, slick) {
    // https://github.com/kenwheeler/slick/issues/1802
    setTimeout(() => setSlidesToShow(), 0);
  };

  function setSlidesToShow() {
    if ($(window).width() >= 980) {
      return;
    }

    var num1, num2,
        slickListWidth = $el.find('.slick-list').width(),

    num1 = slickListWidth / slideWidth;
    num2 = Math.floor(num1) * slideSpacing;
    num1 = (slickListWidth - num2) / slideWidth;
    num1 = Math.floor(num1 * 100) / 100;

    console.log('slickListWidth', slickListWidth);
    console.log('slideWidth', slideWidth);
    console.log('slidesToShow', num1);

    $el.slick('slickSetOption', 'slidesToShow', num1);

    // refresh to apply slick-slide classes, dots etc. as needed
    $el.slick('resize');

    slidesToShow = num1;
  };

})(); 
```

在 JavaScript 中，我们导入了 slick 库和 jquery。然后，我们将所有其他东西包装在一个[life](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)(立即调用的函数表达式)中。

然后，我们为`slidesToShow`计算声明一些幻灯片默认值，当 slick 初始化时，以及当浏览器窗口调整大小时，将再次运行这些默认值。例如

```
var slidesToShow = 1.1,
    slideWidth = 290,
    slideSpacing = 30; 
```

用 jQuery 选择器为 slick carousel 将要绑定的 HTML 元素设置了`$el`变量。例如

```
var $el = $('.carousel'); 
```

slick `responsive`选项的断点设置为 980，常量`slidesToShow`设置可在浏览器窗口等于或大于该宽度时使用。为了使该响应设置保持不变，在继续之前，在`setSlidesToShow`功能中检查 980 宽度值。例如

```
if ($(window).width() >= 980) {
  return;
} 
```

现在我们已经准备好构建 web 应用程序了。安装[包](https://parceljs.org/)来完成此操作。如

```
npm install parcel-bundler -D 
```

> 如果您计划使用 package 来捆绑其他项目，您可能希望将其安装为一个全局模块，而不是使用`npm install -g parcel-bundler`。

#### [NPM-运行-脚本](https://docs.npmjs.com/cli/run-script)

`package.json`文件中的这些 npm 脚本命令是我们将用来运行包命令的。

要添加的第一个脚本命令，我们将命名为`dev:slidestoshow`。这将启动 package dev 服务器，监视文件更改并根据需要进行重建。

要添加的第二个脚本命令名为`build:slidestoshow`。这将缩小生产文件。注意`--public-url`选项，它在构建过程中将资产路径设置为相对路径。

##### package.json

```
"scripts": {
  "dev:slidestoshow": "parcel ./src/slides-to-show/slides-to-show.html",
  "build:slidestoshow": "parcel build ./src/slides-to-show/slides-to-show.html --public-url ./",
  "test": "echo \"Error: no test specified\" && exit 1"
}, 
```

还有一个需要安装的是[包裹-插件-清理-分发](https://www.npmjs.com/package/parcel-plugin-clean-dist)。这将在为生产重建时删除所有以前的文件。

#### 运行命令

发展

```
npm run dev:slidestoshow 
```

如果一切按预期进行，终端输出将包括一个到开发服务器的 URL。例如在 [运行的服务器 http://localhost:1234](http://localhost:1234) 。

在此模式下运行时所做的任何代码更改都将在服务器上重新生成和呈现。

生产

```
npm run build:slidestoshow 
```

#### 下载代码

本教程的所有源代码都可以在 GitHub 上获得，作为我的 ui cookbookrepo 的一部分。

##### *[源代码](https://github.com/jimfrenette/uiCookbook/tree/master/slick)*

 *最初发布于[jimfrenette . com/2019/04/slick-carousel-responsive-slides-to-show](https://jimfrenette.com/2019/04/slick-carousel-responsive-slides-to-show/)*