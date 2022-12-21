# 使用 FullClip(jquery 插件)在背景之间切换

> 原文：<https://dev.to/straleb/using-fullclip-jquery-plug-in-to-switch-between-backgrounds-2eo4>

最近我开始更多地使用 Jquery 和 Jquery 提供的插件，比如 SuperSlides、EasyPieChart、CountUp。尝试了其中一些之后，我发现了一个非常方便、易于安装的软件，它的名字叫 FullClip。

## 关于全剪辑

这个简单的插件只做一件事，而且做得很好——它通过指定所需的图像(无论是本地图像还是远程图像)来创建一个响应性的全屏人体背景图像。此外，它可以通过指定图像数组来创建一个漂亮的背景图像幻灯片。
*通过 GitHub*

## 设置

插件的设置非常简单，我们要做的第一件事就是从 [GitHub Repo](https://github.com/bdimitrovski/full_clip) 下载它。
**从 repo 下载**的文件后，我们会得到下面的文件
[![alt text](img/ebea08b7f28e411b8396c738f92720d4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vcmNnsVe--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/qghJSFj/prvi-Screnshot.png) 
我们需要的文件在 **src** 文件夹
[![alt text](img/50157e31caa4f21964a39409325b0aeb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--LBWJR4Rm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.ibb.co/HGQMS8p/drugi-Folder-Screnshot.png) 
把两个文件都复制到你的根文件夹，把 fullclip.js 链接到你的索引文件，你就可以开始了

```
<script src="fullclip.js"></script> 
```

## 用插件编码

我将用一些简单的例子来说明如何使用这个插件。首先，我们将在 Html 中创建一个简单的容器和一个名为**的类。全背景**T3】

```
<section class="container">
  <div class="fullBackground"></div>
  <h2 class="caption">The future is now</h2>
</section> 
```

## 造型

设置完 Html 后，我们需要在 CSS 中设置一些样式，你可以根据自己的意愿设置你的部分或标题，我将用它作为一个例子。

```
.fullBackground {
  background-position: center center;
  background-attachment: fixed;
  background-size: cover;
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
} 
```

## Jquery 代码

在代码中，我们设置了一个图像数组，将用作幻灯片。
我们可以设置图像之间的过渡时间和过渡延迟。
最终，由您根据自己的需求进行定制🙂

```
$('.fullBackground').fullClip({
  images: ['images/house.jpg', 'images/road.jpg', 'images/winter.jpg'],
  transitionTime: 1000,
  wait: 7000
}); 
```

要查看该插件的完整效果及其工作原理，可以从 [GitHub](https://github.com/bdimitrovski/full_clip) 下载官方回购。
另外，我有一个我用 FullClip 制作的模板，你也可以从我的 [GitHub 账户](https://github.com/StraleB/Bar-page-template-with-jquery-plugin)下载😊

希望你喜欢读这篇文章，如果你对你认为有用的插件有更多的建议，一定要让我知道😊