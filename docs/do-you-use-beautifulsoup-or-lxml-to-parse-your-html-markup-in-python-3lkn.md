# 在 Python 中你是用 BeautifulSoup 还是 LXML 解析你的 HTML 标记？

> 原文：<https://dev.to/prahladyeri/do-you-use-beautifulsoup-or-lxml-to-parse-your-html-markup-in-python-3lkn>

多年来，它一直是我的 html 解析库，在 python 世界中它对 DOM 解析很有用(就像 jquery 在 JavaScript 世界中一样)，并且它支持多种 html 解析器，比如 lxml 和 html5lib。

但是我今天遇到了这个有趣的 [StackOverflow 答案](https://stackoverflow.com/a/19548832/849365)，它暗示 BeautifulSoup 可能不是性能最好的，lxml 自己的模块`soupparser`要快得多。

你用什么来解析 html，你有没有遇到过 BeautifulSoup 的性能瓶颈？我，我没有。