# 流星 vs 网络包 vs 包裹

> 原文：<https://dev.to/trusktr/meteor-vs-webpack-vs-parcel-39d7>

对，没错，我说的是[流星](https://www.meteor.com/)！

流星经常被忽视。出于我无法解释的原因，它被严重低估了。事实上，这只是 dev.to 上标记为`meteor`的第十篇帖子！

## 流星是一款神奇的软件。

本纽曼-目前维护流星的人之一-在流星发展组之前在脸书工作。在脸书，他写了再生器。再生器是巴别塔在引擎盖下用来传输的工具`async functions`。

*很有可能你在某个时候写了`async function` s，它们是由[本·纽曼的再生器在幕后处理的！](https://github.com/facebook/regenerator)* 。你们中的一些人可能还在为旧的浏览器移植 T1，因此今天还在使用再生器。

这个叫 Ben 的家伙现在在 Meteor 上工作，他的代码非常干净。一些鼓舞人心的事情。

## 流星 vs [Webpack](https://webpack.js.org/) ？流星 vs [包裹](https://parceljs.org/)？什么？

您可能会将 Meteor 视为一个应用程序框架。

嗯，你可能是对的。

Meteor 是库和工具的集合，当一起使用时(例如在 CLI 工具为您搭建的默认应用程序中),可以认为是一个应用程序框架。

但是你不需要一次使用所有的零件。

出于一些奇怪的原因，人们认为使用流星是全有或全无。也许这就是为什么它被低估了，并导致人们浪费时间重新实现所有令人敬畏的事情流星已经可以为你做。

这篇文章可以叫做“流星 vs [余烬](https://emberjs.com/) vs [棱角](https://angular.io/)”或者“流星 vs [意思是](http://meanjs.org/)”或者“流星火焰 vs [反应](https://reactjs.org/) vs [Vue](https://vuejs.org/) ”或者“流星 vs [快递](https://expressjs.com/) vs [Koa.js](https://koajs.com) ”或者“流星 vs [护照. js](http://www.passportjs.org/) vs [许可证](https://github.com/ianstormtaylor/permit)”或者“流星 vs [离子框架”](https://ionicframework.com/)

你知道吗，在 Meteor 的核心中有一个令人惊叹的构建系统和捆绑器，它能够构建你的 JavaScript/TypeScript/coffee script/Vue/JSX/Angular/Sass/Less/等等，不仅适用于 web(旧目标和新目标)，还适用于 iOS 和 Android 的移动应用？

你可以使用 Meteor 将你的代码和现存的最复杂的基于网络的构建系统和捆绑器捆绑在一起，这样可以节省你的时间。

这个事实本身就是你应该考虑的！

## 好吧，那有什么区别呢？

[Meteor 的构建系统](https://guide.meteor.com/build-tool.html)是开箱即用的，在某种意义上类似于包裹，你可以使用默认设置运行，没有任何麻烦。

Meteor 的构建系统也像 Webpack 一样是高度可插拔的。很容易让 Meteor [“构建插件”](https://docs.meteor.com/api/packagejs.html#build-plugin-api)处理任何文件类型(相当于 Webpack 中的“加载器”)。

### 让我告诉你，流星有什么是其他捆绑者没有的:

Meteor 有一个太空时代的系统，用于为新旧目标构建代码，并且*根据它检测到的环境*自动为运行时环境提供特定的包，不需要任何配置！

Meteor 允许你将基于网络的应用程序发布到 iOS 或 Android 上。您只需要几个简单的命令来添加构建目标:

```
meteor add-platform android
meteor add-platform ios 
```

在你的操作系统(macOS 上的 XCode，或者任何 OS 上的 Android Studio)中安装了构建移动应用的先决条件之后，你就可以*简单地在移动模拟器或者实际设备*中运行你的应用，只需简单的命令:

```
meteor run ios # runs in an iOS emulator
meteor run ios-device # runs on your connected iOS device
meteor run android # runs in an Android emulator
meteor run android-device # runs in your connected Android device
meteor run ios android-device # run on multiple targets 
```

你不能从其他捆绑商那里得到这个！🚀

老实说，我鼓励你去看一看。Meteor 的所有方面(非框架*工具集合*)，包括[构建系统](https://guide.meteor.com/build-tool.html)，都有文档记录。

* * *

我就写这么多了。如果你有任何问题，我很乐意尝试回答！:)

要了解更多关于“Meteor vs Webpack vs Parcel”的信息，我推荐阅读一篇关于 [Webpack vs Parcel](https://medium.com/@ibrahimbutt/if-youve-ever-configured-webpack-parcel-will-blow-your-mind-b615468cee78) 的文章(如果你还不知道的话，可以查找“Webpack vs Browserify”和“Webpack vs Rollup”)，并查看 [Meteor 的构建系统](https://guide.meteor.com/build-tool.html)。

* * *

7 月 9 日周二来[流星之夜，听听本纽曼等人对流星的讨论。](https://www.meetup.com/Meteor-SFBay/events/262463643/)