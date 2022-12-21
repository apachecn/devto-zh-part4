# 如何在 markdown 中对齐图像

> 原文：<https://dev.to/davidwells/how-to-align-images-in-markdown-n0c>

> 最初发布于 [davidwells.io](https://davidwells.io/snippets/how-to-align-images-in-markdown)

降价太棒了。这是一种快速编写内容的奇妙方式，没有使用所见即所得编辑器格式化的开销。如果你对`.md`文件不熟悉，在这里&[这里](https://daringfireball.net/projects/markdown/)检查一下基础知识。

在 markdown 中写文本超级快速简单，但是**如何对齐图像**？

请继续阅读，了解如何操作！完整的代码可以在这个 [github gist](https://gist.github.com/DavidWells/7d2e0e1bc78f4ac59a123ddf8b74932d/) 中找到

# 降价中的图像对齐

普通的 markdown 图像标签不允许任何对齐属性，当你试图在 github 上使你的`README.md`文件变得漂亮时，这是一个令人失望的事情。

```
<!-- No alignment options -->
![GitHub Logoimg/logo.png) 
```

幸运的是，我们可以使用`html`图像标签来增强我们的文档。

```
<!-- Alignment options!!!!! -->
<img align="left" width="100" height="100" src="http://www.fillmurray.com/100/100"> 
```

## 左对齐

这是你需要向左对齐图像的代码:

```
<img align="left" width="100" height="100" src="http://www.fillmurray.com/100/100"> 
```

## 右对齐

这是您需要将图像向右对齐的代码:

```
<img align="right" width="100" height="100" src="http://www.fillmurray.com/100/100"> 
```

## 中心对齐示例

将图像以`p`或`div`方式环绕到中央。

```
<p align="center">
  <img width="460" height="300" src="http://www.fillmurray.com/460/300">
</p> 
```

## 大幅降价格式

如果你喜欢这个，你可能会喜欢[减价魔术](https://github.com/davidwells/markdown-magic)。我构建它是为了自动格式化 markdown 文件，并允许人们从外部来源同步文档/代码/数据。