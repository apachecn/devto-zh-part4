# markpedia

> 原文：<https://dev.to/rognoni/markpedia-179p>

降价格式的维基百科:

1.  克隆项目[github.com/rognoni/markpedia](https://github.com/rognoni/markpedia)
2.  创建一个新的 MediaWiki 标记文件，例如 [wiki/Anti-pattern.wiki](https://en.wikipedia.org/w/index.php?title=Anti-pattern&action=edit)
3.  执行命令`pandoc -s Anti-pattern.wiki -o Anti-pattern.md`
4.  编辑风格和内容，例如添加`.md`扩展到维基链接
5.  推送和浏览[https://raw . githubusercontent . com/rognoni/markpedia/master/wiki/Anti-pattern . MD](https://monastic.netlify.com/#/url/https://raw.githubusercontent.com/rognoni/markpedia/master/wiki/Anti-pattern.md)
6.  提出拉取请求