# 构建高性能 Hugo 主题

> 原文：<https://dev.to/stereobooster/building-high-performance-hugo-theme-3b9>

这个帖子的前一个版本已经死了，因为我不小心用另一个故事覆盖了它。感觉懒的恢复完整版。主要思想是:要构建一个加载速度快的 Hugo 主题，我们可以遵循 AMP 项目的[建议(我不想使用 AMP 本身，但是清单是有意义的)。](https://css-tricks.com/taking-amp-for-a-spin/)

这里的演示是[这里的](https://stereobooster.netlify.com/)。源代码是[这里的](https://github.com/stereobooster/hugo-pipes-parcel/tree/basic-theme)。

### 只有异步脚本。非异步脚本会阻止 DOM 构造并延迟页面呈现。

我有一个也是唯一一个与包裹(包裹是可选的)捆绑在一起的脚本，并放在 body 的结束标记之前。

```
{{ if (fileExists "./assets/output/index.js") }}
  {{ $js := resources.Get "output/index.js" }}
  {{ with $js }}
    {{ $secureJS := . | resources.Fingerprint }}
    <script src="{{ $secureJS.Permalink }}" integrity="{{ $secureJS.Data.Integrity }}" crossorigin="anonymous" async></script>
  {{ end }}
{{ end }} 
```

Enter fullscreen mode Exit fullscreen mode

### 外部资源需要有设定的维度。

对于 iframes 用户(博客作者)需要提供尺寸，但对于本地图像，我们可以在短代码的帮助下自动计算尺寸。见[我关于这个主题的另一篇文章](https://dev.to/stereobooster/responsive-images-for-hugo-dn9)。

### CSS 必须内联，大小绑定。

我使用了最少量的 CSS:

*   任何其他规格化器也可以工作
*   [晕厥](http://nowodzinski.pl/syncope)为垂直节律
*   用`hugo gen chromastyles --style=manni > assets/css/code.css`生成的高亮代码

然后我[内联](https://discourse.gohugo.io/t/hugo-inline-css-optimization-for-google-pagespeed/6032/21)它直接在头:

```
{{ with resources.Get "output/index.css" | minify }}
<style>{{ .Content | safeCSS }}</style>
{{ end }} 
```

Enter fullscreen mode Exit fullscreen mode

### 字体加载必须高效。

我不使用自定义字体。我用所谓的[系统字库](https://css-tricks.com/snippets/css/system-font-stack/)。其他选项将是自托管网络字体与 [`font-display: swap;`](https://developer.mozilla.org/en-US/docs/Web/CSS/@font-face/font-display)

### 只在 GPU 上运行动画。

我现在没有任何动画，但我会在未来考虑这一点。

### 其他

我添加了[快速链接](https://github.com/GoogleChromeLabs/quicklink)来加快页面间的导航。