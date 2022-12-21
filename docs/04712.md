# 画布上的自举

> 原文：<https://dev.to/takaneichinose/bootstrap-off-canvas-2g58>

# 拉起画布

只是一个在 Bootstrap 上的简单实现。

* * *

# 基本用法

在源代码中导入这些行。

```
<link rel="stylesheet" href="css/bootstrap-off-canvas.css">
<script src="js/bootstrap-off-canvas.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

然后用这几行 HTML 代码包装通常的引导代码。

```
<div class="off-canvas-wrapper">
  <div id="offcanvasid" class="off-canvas">
    <div class="off-canvas-nav">
      <div class="nav nav-pills nav-fill flex-column">
        <a href="#" class="text-left nav-item nav-link active">Link 1</a>
        <a href="#" class="text-left nav-item nav-link">Link 2</a>
        <a href="#" class="text-left nav-item nav-link disabled">Disabled Link</a>
      </div>
      <div class="off-canvas-content" data-target="#offcanvasid">
        <a href="#" class="off-canvas-toggler" type="button" data-target="#offcanvasid" aria-controls="offcanvasSupportedContent" aria-expanded="false" aria-label="Toggle off canvas menu">Toggle off canvas</a>
        <!-- Your usual Bootstrap code goes here -->
        <div class="container">
          ...
        </div>
      </div>
    </div>
  </div>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

* * *

# 选项

在“非画布导航”类中，您可以使用引导程序的颜色实用程序:

[颜色](https://getbootstrap.com/docs/4.3/utilities/colors/)

在" off-canvas-content "类中，您可以添加" overlay "类，以便在源代码的内容部分添加覆盖遮罩。

# 储存库

请点击下面的链接去我的 Github 库

## ![GitHub logo](img/292a238c61c5611a7f4d07a21d9e8e0a.png)/[画外引导](https://github.com/takaneichinose/bootstrap-off-canvas)

### 只是一个在 Bootstrap 上的简单实现。

<article class="markdown-body entry-content container-lg" itemprop="text">

# 画布上的自举

只是一个在 Bootstrap 上的简单实现。

# 基本用法

在源代码中导入这些行。

```
<link rel="stylesheet" href="css/bootstrap-off-canvas.css">
<script src="js/bootstrap-off-canvas.js"></script>
```

Enter fullscreen mode Exit fullscreen mode

然后用这几行 HTML 代码包装通常的引导代码。

```
<div class="off-canvas-wrapper">
  <div id="offcanvasid" class="off-canvas">
    <div class="off-canvas-nav">
      <div class="nav nav-pills nav-fill flex-column">
        <a href="#" class="text-left nav-item nav-link active">Link 1</a>
        <a href="#" class="text-left nav-item nav-link">Link 2</a>
        <a href="#" class="text-left nav-item nav-link disabled">Disabled Link</a>
      </div>
      <div
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/takaneichinose/bootstrap-off-canvas)

* * *

# 结论

如果有人想用这个，你可以随意使用。我认为，仍然有很大的改进空间，所以如果你们中的任何人发现了一个 bug，或者有一个功能改进的建议，请在下面评论。

* * *

# 演示

[https://codepen.io/takaneichinose/embed/pGxRLQ?height=600&default-tab=result&embed-version=2](https://codepen.io/takaneichinose/embed/pGxRLQ?height=600&default-tab=result&embed-version=2)