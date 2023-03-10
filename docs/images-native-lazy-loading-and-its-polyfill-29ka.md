# 图像本地延迟加载及其聚合填充

> 原文：<https://dev.to/islam/images-native-lazy-loading-and-its-polyfill-29ka>

## 什么是懒加载

图像和视频会消耗大量数据，并影响 web 性能。如果您的网页包含许多图像(或视频)，那么它们中的一些(如果不是很多的话)确实在视窗之外。任何浏览器的正常行为是在网页加载期间加载所有图像，这可能会减慢加载时间。

延迟加载用于延迟图像加载，直到图像即将进入视窗，并且只加载网页加载后显示的图像。从而减少网页首次加载所需的时间。

* * *

## 本机懒加载

开发者使用 javascript 插件进行懒加载。好消息是 Chrome 75 现在支持原生延迟加载。使用起来很简单。您只需将属性`loading="lazy"`添加到`<img>`元素中。

```
<img src="image.jpg" loading="lazy" alt="..." /> 
```

#### 属性`loading`的值可以是:

*   懒惰 = > *告诉浏览器在图像显示在屏幕上之前加载图像。*
*   **急切** = > *让浏览器尽快加载图像。这可以添加到网页加载后将出现在视窗内的图像中。*
*   **auto** = > *让浏览器决定是否延迟加载。*

* * *

## 懒加载插件

有很多 javascript 插件可以实现懒加载。它们依靠用`data-src`属性替换`src`属性来阻止浏览器加载图像。

```
<img data-src="image.jpg" alt="..." /> 
```

然后使用 javascript 检测图像何时靠近视口，将`data-src`属性的值复制到`src`属性，以便浏览器可以加载它。

* * *

#### 此类库的例子:

*   香草冰淇淋。
*   懒人。

* * *

## 混合懒加载

正如 Andrea Verlicchi 在他的关于 Smashing 杂志的[文章](https://www.smashingmagazine.com/2019/05/hybrid-lazy-loading-progressive-migration-native/)中所解释的那样:
*混合延迟加载是一种在支持它的浏览器上使用本地延迟加载的技术，否则，依赖 JavaScript 来处理延迟加载。*

```
<!--Load this hero image as soon as possible-->
<img src="hero.jpg" loading="eager" alt=".."/>

<!--Lazy load these images as user scroll down-->
<img data-src="image1.jpg" loading="lazy" alt=".." class="lazyload"/>

<img data-src="image2.jpg" loading="lazy" alt=".." class="lazyload"/>

<img data-src="image3.jpg" loading="lazy" alt=".." class="lazyload"/> 
```

```
//Check for browser support.
if ('loading' in HTMLImageElement.prototype) {
    const images = document.querySelectorAll('lazyload')
    //copy the value of the data-src to the src.
    images.forEach(img => img.src = img.dataset.src)
} else {
    //if no support, async load the lazysizes plugin
    let script = document.createElement("script");
    script.async = true;
    script.src =
      "https://cdnjs.cloudflare.com/ajax/libs/lazysizes/4.1.8/lazysizes.min.js";
    document.body.appendChild(script);
} 
```

* * *

<figure>

[https://www.youtube.com/embed/bE2jCvZAdqs](https://www.youtube.com/embed/bE2jCvZAdqs)

<figcaption>Lazy loading in action.</figcaption>

</figure>

* * *

## 资源

*   面向 web 的本地映像延迟加载！
*   混合延迟加载:向本地延迟加载的渐进迁移