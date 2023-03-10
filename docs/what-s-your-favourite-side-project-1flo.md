# 你最喜欢的副业是什么？

> 原文：<https://dev.to/aadibajpai/what-s-your-favourite-side-project-1flo>

你知道，那个你自己做的东西很酷。

我先说:我对不得不每个月左右手动更新我的简历感到非常恼火。像下载数量这样的东西，很快就会过时。

输入 DynamicLaTeX。每天自动更新内容并部署到 Heroku。很漂亮，我觉得。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)/[dynamic atex](https://github.com/aadibajpai/DynamicLaTeX)

### 🔮用最新的数字和信息自动更新 LaTeX 文档

<article class="markdown-body entry-content container-lg" itemprop="text">

# 动态测试<g-emoji class="g-emoji" alias="crystal_ball" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f52e.png">🔮</g-emoji>

您的 LaTeX 文档在被点击时总是有最新的数字。最初是为了更新 LaTeX 简历而创建的。

高度可扩展。

## <g-emoji class="g-emoji" alias="scroll" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4dc.png">📜</g-emoji>用法

*   在 LaTeX 文档中定义这个新命令:

```
\newcommand{\py}[1]{\textit{\%#1\%}}
```

*   无论您想在何处格式化动态变量，请使用以下命令:

```
\py{<variable_name>}
```

*   在`resume.tex`中添加您的 LaTeX 代码。

*   在`main.py`中，给`template_values`添加相同的变量名和更新函数。就是这样！

运行`main.py`提供您的 pdf。我在端点中使用了我的名字，所以请用您自己的名字进行更改:)

在`updater.py`中也已经提供了一些更新帮助函数。

## <g-emoji class="g-emoji" alias="grey_question" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/2754.png">❔</g-emoji> 为什么？

我用它来获得 SwagLyrics 和 SwSpotify 的最新下载数，然后写下来，这样简历总是最新的。以前，我必须手动更新下载数。现在，它是准确的…

</article>

[View on GitHub](https://github.com/aadibajpai/DynamicLaTeX)

你做了什么东西来自动化这些无聊的东西？