# 水疗太费事了

> 原文：<https://dev.to/dropconfig/spas-are-way-too-much-work-50ca>

一个简单的 HTML + CSS 应用程序中夹杂着少量的 Javascript 代码，这是一件非常美妙的事情。

我将谈到我喜欢这个系统的几点。

## 速度

发送给用户的 HTML 是该页面的实际内容。

#### 考虑大多数水疗

1.  我从服务器加载页面。
2.  我必须下载所有的 JS
3.  当 JS 为页面获取数据时，我看到了一个加载器
4.  虚拟 DOM 被创建并放入真实 DOM 中
5.  浏览器呈现页面

这需要很多步骤，并且需要不可忽略的处理能力。在手机上可能是一场噩梦。

#### 考虑 SSR 页面

1.  我从服务器加载页面。
2.  浏览器呈现页面
3.  如果做得好，javascript 就会被加载

在 SSR 的情况下，我们可以更快地获得第一次渲染，并且在用户设备上的工作量要少得多。

额外的好处是:无论我们的 Javascript 状态如何，我们希望用户看到的内容都会显示出来。

当然，你可以预渲染 react 和其他 SPA 技术，但我会谈到这一点。

## 洒落

现在我们可以开始有趣的部分了，JavaScript！我喜欢 JavaScript。我喜欢写它。我也喜欢写 spa。然而，在某些情况下，我们可能过于依赖它了。

那么 JavaScript 里怎么撒？你可以只使用 JQuery 或者普通的 JavaScript，但是信不信由你，至少有一个框架是为此而设计的: [Stimulusjs](https://stimulusjs.org/handbook/introduction) 。它是为了给你已经有的 HTML 增加功能。

我不会对它进行过多的描述，但它是一个非常简单且易于学习的框架。它也不会做太多。基本上给你一个简单的方法附加到你的 HTML。

现在你可以加糖屑了。我们用它来提交表单和激活模态等。

## 更好的流程

如果你有懂 HTML 和 CSS 的设计师，他们可以用 HTML 而不是 photoshop 制作模型。这提供了一条从设计器 HTML 到 HTML 模板的简单路径。

因为现在你的 HTML 和 CSS 已经处理好了，你不需要转换它来作出反应。标记完成了！你只需要用你的模板语言注释 HTML。

如果有什么需要改变的，在一天结束时，它只是 HTML，你的设计师可以自己编辑文件进行调整。

这是我认为普通 HTML 胜过服务器端渲染 React 或 Vue 的重要方式之一。(虽然 Vue 挺整齐的)。

## 手机应用程序

如果你看到 [Basecamp 如何创建他们的移动应用](https://m.signalvnoise.com/basecamp-3-for-ios-hybrid-architecture/) SSR 和普通 HTML 是小团队快速前进的关键。
你可以在你的应用程序中加载普通的 HTML 页面，并增加本地组件。它比 React Native 更轻量级，也更容易更新。使用过 React Native 之后，我无论如何也不会真的希望它运行；)只要你的页面是移动优化的，你就有了移动应用的基础。你总是使用相同的标记。

你怎么想呢?

## 我什么时候用 React？

有些地方反应有道理。数据和反应性重的东西(呵呵)确实能从中受益。在线图像编辑器或视频网站，如 YouTube 和 Hulu。

我们可以用 React 来扩充我们站点的一部分，而不是整个站点的 SPA。以我们的视频网站为例。也许我们只想在视频页面加载 React，但仍然只是 SSR 其他列表页面。

在视频页面上，我们的 JavaScript sprinkles 可以加载 React 代码。Webpack 中的代码分割非常适合这一点！

这里有一个使用刺激的例子

```
//Video React Loader controller

export default class extends Controller {
    static targets = ["react"]
    intialize(){
        this.videoJS = import("./video.js");
    }
    connect(){
        const reactParent = this.reactTarget;

        this.videoJS.init(reactParent);
    }
    disconnect(){
        const reactParent = this.reactTarget;
        this.videoJS.destroy(reactParent);
    }
} 
```

```
// Video.js
import VideoStuff from "./whatever"

function init(node){
    return ReactDom.render(<VideoStuff />, node);
}

Function destroy(node){
    return ReactDom.unmountComponentAtNode(node);
} 
```

```
//video.html
<div data-controller="video">
    <div data-target="video.react"></div>
</div> 
```

这样，我们只在需要的时候加载 React，并且仍然可以 SSR 渲染我们想要的所有其他东西。

你怎么想呢?