# 面向移动响应设计的 CSS 媒体类型和查询

> 原文：<https://dev.to/gvinod1991/css-media-types-and-queries-for-mobile-responsive-design-4f4c>

如今，移动响应网站甚至网络应用非常普遍。每个人都想开发自己的网站，网络应用应该是移动响应的，这意味着在每个手持设备上都清晰可见和交互。由于智能手机革命，手机浏览器用户多于桌面浏览器。为了瞄准移动浏览器用户，每个组织/个人最喜欢开发移动响应网站或 web 应用程序。每一个 UI/UX 开发者都应该知道响应性和交互性更强的设计。在响应式设计中，CSS 媒体查询起着非常关键的作用，因为使用媒体查询我们可以编写特定于设备的 CSS 代码，这是响应式设计的基础。

让我们深入研究 CSS 媒体类型和查询的细节

在 CSS2 中，首次引入了@media rule 来定义不同媒体类型的规则，例如计算机屏幕、手持设备、打印机和电视屏幕，但是除了打印机之外，许多设备都不支持它。

后来在 CSS3 中，通过扩展 CSS2 中的@media 引入了媒体查询。媒体查询寻找屏幕的分辨率，而不是设备。根据屏幕的宽度和高度，将应用媒体查询规则。

## 媒体类型

有四种媒体类型

*   全部(用于所有媒体类型)
*   屏幕(用于屏幕)
*   打印(用于打印设备)
*   语音(用于屏幕阅读器)

我们可以在 HTML 文件的头部分链接特定于媒体的外部样式表。

```
<link rel="stylesheet" media='print' href="print.css">
<link rel="stylesheet" media='screen' href="styles.css"> 
```

Enter fullscreen mode Exit fullscreen mode

## 媒体查询

媒体查询规则用于编写设备特定的样式和语法

```
@media mediatype not|only|and|, (expressions){
    /*css code here*/
} 
```

Enter fullscreen mode Exit fullscreen mode

“、”、“和”、“非”、“仅”用于组合两个或多个媒体查询，以进行复杂的媒体查询。

### 和

让我们为小于 480px(iPhone)的移动浏览器写一些 CSS

```
@media screen and (max-width:480px){
    .nav{
        font-size:12px;
        display:block
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上述媒体查询与“与”条件结合，当设备屏幕宽度小于或等于 480 像素时，上述样式将应用于导航类

现在我们将屏幕宽度的范围作为目标

```
@media screen and (min-width:480px) and (max-width:768px){
    .nav{
        color:green
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当屏幕宽度介于 480 像素和 768 像素(平板电脑屏幕宽度)之间时，字体颜色将为绿色

对于平板电脑、iPads(纵向)屏幕，我们可以编写以下媒体查询

```
@media screen (min-width:480px) and (min-width:1024px){
    .nav{
        color:grey
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

适用于笔记本电脑/台式机屏幕

```
@media screen and (min-width:1224px){
    .nav{
        color:blue
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于大型桌面屏幕

```
@media screen and (min-width:1884px){
    .nav{
        color:red
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

对于屏幕方向

```
@media screen and (min-device-width: 481px) and (max-device-width: 1024px) and (orientation:portrait) {
    .nav{
        color:blue;/*Color will be blue,When screen orientation is portrait and width in between 481px and 1024px*/
    }
}

@media screen and (min-device-width: 481px) and (max-device-width: 1024px) and (orientation:landscape) {
    .nav{
        color:tomato;/* color will be tomato ,When screen orientation is landscape and width in between 481px and 1024px*/
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 或(，)

```
@media (max-width:480px), (min-width:768px){
    .html{
        color:green
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

两个单独的媒体查询用“，”连接，当屏幕宽度为 480px 或更小以及 768px 或更大时，将应用样式。

### 仅

“only”关键字用于不支持媒体查询的浏览器不加载样式表。

```
@media only screen (max-width:480px){
    html{
        color:green
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 不

如果我们用“not”关键字编写一个媒体查询，那么媒体查询为真而 not 将变为假。不支持媒体查询的浏览器无法理解“not”关键字，因此不会应用关联的样式。

```
@media not screen (max-width:480px){
    html{
        color:green
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

用于媒体查询的设备屏幕宽度

*   手机屏幕宽度:480px
*   平板电脑(ipad)屏幕宽度:768 像素
*   笔记本电脑和台式机屏幕宽度> 1024 像素

注意:-首先设计手机屏幕的布局，然后使用平板电脑和桌面屏幕的媒体查询，这样开发会更快。