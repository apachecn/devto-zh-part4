# #CodepenChallenge 5 行:将被扭曲的笑脸

> 原文：<https://dev.to/takaneichinose/codepenchallenge-5-lines-smiley-that-will-be-distorted-23c9>

用了 5 行 HTML，5 行 CSS，5 行 JavaScript，我试着创建了一个点击(或者应该说鼠标按下)会变形的笑脸。

首先，用 hack，我试着用滤镜复制笑脸的第二只眼睛:投影。我用了它，因为你实际上不能用阴影来复制眼睛。

我使用 Bootstrap 添加了背景色，并将 SVG 元素居中对齐。

我使用 jQuery 将代码最小化为 5 行 Javascript。如果我使用 vanilla，将需要 6 行代码，是的，老实说，我认为 jQuery 可读性更好。

我使用 GSAP 动画的 SVG 路径。事实上，我只打算使用 CSS，但因为它不能在其他浏览器上工作，我决定使用 Javascript 和 GSAP。

使用 Sass(不是 SCSS)也帮助我节省了行。因为它不需要我用括号。

还有 Pug HTML 预处理器，因为它不需要我在每个元素中放一个结束标签。

[https://codepen.io/takaneichinose/embed/YzKarye?height=600&default-tab=result&embed-version=2](https://codepen.io/takaneichinose/embed/YzKarye?height=600&default-tab=result&embed-version=2)