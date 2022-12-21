# 降价格式的维基百科

> 原文：<https://dev.to/rognoni/wikipedia-in-markdown-format-838>

你认为使用 markdown 格式对维基百科项目有什么贡献？

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [罗格诺尼](https://github.com/rognoni) / [马克佩迪亚](https://github.com/rognoni/markpedia)

### 降价格式的维基百科

<article class="markdown-body entry-content container-lg" itemprop="text">

# markpedia

降价格式的维基百科:

1.  克隆项目[github.com/rognoni/markpedia](https://github.com/rognoni/markpedia)
2.  创建一个新的 MediaWiki 标记文件，例如 [wiki/Anti-pattern.wiki](https://en.wikipedia.org/w/index.php?title=Anti-pattern&action=edit)
3.  执行命令`pandoc -s Anti-pattern.wiki -o Anti-pattern.md`
4.  编辑风格和内容，例如添加`.md`扩展到维基链接
5.  更新 [data/redirects.json](https://github.com/rognoni/markpedia/blob/master/data/redirects.json) 并修复维基链接
6.  添加页脚
7.  推送和浏览[https://raw . githubusercontent . com/rognoni/markpedia/master/wiki/Anti-pattern . MD](https://monastic.netlify.com/#/url/https://raw.githubusercontent.com/rognoni/markpedia/master/wiki/Anti-pattern.md)
8.  提出拉取请求

## 内容规则

1.  英语信息学文章
2.  转换和更新现有文章
3.  按照维基百科命名惯例创建新文章(大写，下划线)

</article>

[View on GitHub](https://github.com/rognoni/markpedia)