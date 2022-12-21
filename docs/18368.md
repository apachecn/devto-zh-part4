# 雨果的回应图像

> 原文：<https://dev.to/stereobooster/responsive-images-for-hugo-dn9>

这里的演示是[这里的](https://stereobooster.netlify.com/posts/typography/)。源代码是[这里的](https://github.com/stereobooster/hugo-pipes-parcel/tree/basic-theme)。

## 根据视口调整大小

我们不需要太多来建立一个基本的响应图像。

```
<img src="..." alt="..." > 
```

Enter fullscreen mode Exit fullscreen mode

```
img {
  width: 100%;
  height: auto;
} 
```

Enter fullscreen mode Exit fullscreen mode

图像将占用所有给定的空间，并根据视窗按比例调整大小，

## 尺寸

在不知道图像浏览器的尺寸(比例)的情况下，浏览器最初会以高度 0 绘制图像，然后一旦图像加载，它会以适当的高度重新绘制图像，页面会不愉快地“跳跃”。为了避免这种情况，我们需要提供宽度和高度

```
<img src="..." alt="..." width="{{ $img.Width }}" height="{{ $img.Height }}"> 
```

Enter fullscreen mode Exit fullscreen mode

在 Hugo 中，我们需要使用 [`imageConfig`](https://kodify.net/hugo/functions/imageconfig-function/) :

```
{{ $img := imageConfig (path to file) }} 
```

Enter fullscreen mode Exit fullscreen mode

## `srcset`

当前的实现是响应性的，因为图像的大小随着屏幕的大小而变化，但是调整大小是由浏览器完成的。相反，我们可以提供多种尺寸的图像，以便较小屏幕的客户端(最有可能是移动设备)可以通过下载较小的图像来减少流量。

为此我们可以使用 [`srcset`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#attr-srcset) img 属性和 Hugo 内置功能来调整图像大小。比如像在[这条](https://laurakalbag.com/processing-responsive-images-with-hugo/) :

```
{{ $src := .Page.Resources.GetMatch (printf "*%s*" (image path)) }}

{{ $tiny := $src.Resize $tinyw }}
{{ $small := $src.Resize $smallw }}
{{ $medium := $src.Resize $mediumw }}
{{ $large := $src.Resize $largew }}

<img
  srcset='
  {{ if ge $src.Width "500" }}
    {{ with $tiny.RelPermalink }}{{.}} 500w{{ end }}
  {{ end }}
  {{ if ge $src.Width "800" }}
    {{ with $small.RelPermalink }}, {{.}} 800w{{ end }}
  {{ end }}
  {{ if ge $src.Width "1200" }}
    {{ with $medium.RelPermalink }}, {{.}} 1200w{{ end }}
  {{ end }}
  {{ if ge $src.Width "1500" }}
    {{ with $large.RelPermalink }}, {{.}} 1500w {{ end }}
  {{ end }}'
  {{ if .Get $medium }}
    src="{{ $medium.RelPermalink }}"
  {{ else }}
    src="{{ $src.RelPermalink }}"
  {{ end }}
  ... 
```

Enter fullscreen mode Exit fullscreen mode

## 懒装

我们可以通过推迟图像下载来节省更多的带宽，除非它们在视窗中。我们可以用 [`lazysizes`](https://github.com/aFarkas/lazysizes) 来完成这个。

```
import "lazysizes"; 
```

Enter fullscreen mode Exit fullscreen mode

将`src`改为`data-src`，`srcset`改为`data-srcset`，增加`class="lazyload"`T5

```
<img
  class="lazyload"
  data-sizes="auto"
  data-srcset=... 
```

Enter fullscreen mode Exit fullscreen mode

这种技术被称为[延迟加载](https://addyosmani.com/blog/lazy-loading/)。

## LQIP

如果我们使用延迟加载，用户的网络很慢或关闭，用户将看到空白的矩形而不是图像，这可以被视为一个破碎的网站。相反，我们可以提供低质量的图像占位符——实际内容的模糊预览。为此我们可以内嵌 [base64 编码的小版本原始图像](https://mwellner.de/en/2018/10/30/image-handling-with-hugo/) :

```
{{ $lqip := $src.Resize $lqipw }}

<div class="img" style="background: url(data:image/jpeg;base64,{{ $lqip.Content | base64Encode  }}); background-size: cover">
  
  <img
    class="lazyload"
    ... 
```

Enter fullscreen mode Exit fullscreen mode

和一点 CSS

```
.img svg,
.img img {
  margin: 0;
  width: 100%;
  height: auto;
}

.img {
  position: relative
}

.img img {
  position: absolute;
  top:0;
  left:0;
} 
```

Enter fullscreen mode Exit fullscreen mode

## noscript

延迟加载的另一个缺点是没有 JS 图像就不能工作。让我们通过在`<noscript>`
中提供默认的 img 来解决这个问题

```
<noscript>
  <img
    loading="lazy"
    ... 
```

Enter fullscreen mode Exit fullscreen mode

和

```
.nojs .img .lazyload {
  display: none;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 最终版本

让我们创建一个短代码

```
{{/* get file that matches the filename as specified as src="" in shortcode */}}
{{ $src := .Page.Resources.GetMatch (printf "*%s*" (.Get "src")) }}

{{/* set image sizes, these are hardcoded for now, x dictates that images are resized to this width */}}

{{ $lqipw := default "20x" }}
{{ $tinyw := default "500x" }}
{{ $smallw := default "800x" }}
{{ $mediumw := default "1200x" }}
{{ $largew := default "1500x" }}

{{/* resize the src image to the given sizes */}}

{{ $lqip := $src.Resize $lqipw }}
{{ $tiny := $src.Resize $tinyw }}
{{ $small := $src.Resize $smallw }}
{{ $medium := $src.Resize $mediumw }}
{{ $large := $src.Resize $largew }}

{{/* only use images smaller than or equal to the src (original) image size, as Hugo will upscale small images */}}
{{/* set the sizes attribute to (min-width: 35em) 1200px, 100vw unless overridden in shortcode */}}

{{ $img := imageConfig ($src.RelPermalink | printf "content/%s" ) }}

<div class="img" style="background: url(data:image/jpeg;base64,{{ $lqip.Content | base64Encode  }}); background-size: cover">
  
  <img
    class="lazyload"
    data-sizes="auto"
    data-srcset='
    {{ if ge $src.Width "500" }}
      {{ with $tiny.RelPermalink }}{{.}} 500w{{ end }}
    {{ end }}
    {{ if ge $src.Width "800" }}
      {{ with $small.RelPermalink }}, {{.}} 800w{{ end }}
    {{ end }}
    {{ if ge $src.Width "1200" }}
      {{ with $medium.RelPermalink }}, {{.}} 1200w{{ end }}
    {{ end }}
    {{ if ge $src.Width "1500" }}
      {{ with $large.RelPermalink }}, {{.}} 1500w {{ end }}
    {{ end }}'
    {{ if .Get $medium }}
      data-src="{{ $medium.RelPermalink }}"
    {{ else }}
      data-src="{{ $src.RelPermalink }}"
    {{ end }}
    width="{{ $img.Width }}" height="{{ $img.Height }}"
    {{ with .Get "alt" }}alt='{{.}}'{{ end }}>
  <noscript>
    <img
      loading="lazy"
      {{ with .Get "sizes" }}sizes='{{.}}'{{ else }}{{ end }}
      srcset='
      {{ if ge $src.Width "500" }}
        {{ with $tiny.RelPermalink }}{{.}} 500w{{ end }}
      {{ end }}
      {{ if ge $src.Width "800" }}
        {{ with $small.RelPermalink }}, {{.}} 800w{{ end }}
      {{ end }}
      {{ if ge $src.Width "1200" }}
        {{ with $medium.RelPermalink }}, {{.}} 1200w{{ end }}
      {{ end }}
      {{ if ge $src.Width "1500" }}
        {{ with $large.RelPermalink }}, {{.}} 1500w {{ end }}
      {{ end }}'
      {{ if .Get $medium }}
        src="{{ $medium.RelPermalink }}"
      {{ else }}
        src="{{ $src.RelPermalink }}"
      {{ end }}
      width="{{ $img.Width }}" height="{{ $img.Height }}"
      {{ with .Get "alt" }}alt='{{.}}'{{ end }}>
  </noscript>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

用法，而不是:

```
![test](./image.jpg) 
```

Enter fullscreen mode Exit fullscreen mode

我们需要写

```
{{< img src="image.jpg" alt="test" >}} 
```

Enter fullscreen mode Exit fullscreen mode

## PS

之前:

[![](img/dc1b51de6d7d3b97505f76016b9cff96.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jcBIXpYg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/yebs9vhpimw86he88bzu.png)

之后:

[![](img/5f465aa1e61d57269aba003258748b82.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yR1DplWU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/om6rvgokhp2z6937ba3l.png)

令人难过的是，没有 markdown 预处理程序允许我使用传统的图像语法来代替短代码😞。

我不能在主题文件中使用短代码，因此我将代码从短代码复制粘贴到主题文件中😞。