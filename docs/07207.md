# 更好的解释

> 原文：<https://dev.to/revelmind/a-better-explaination-26mh>

## 你好，又来了！

由于我之前在 Snippet 上的帖子很模糊，我决定做一个新的，描述它的用途和它可能具有的可能性。

## 对片段的描述

([发布链接](https://github.com/opensource-matrix/snippet/issues/3))所以，你找到了 Snippet。你能用它做什么？

就像 Github 的`Gist`，但是完全离线，除非你在更新库。

*   它每天都会更新，以确保一切正常！

它有多种编程语言的各种“片段”，参见[支持的语言](#supported-languages)

## 真实世界用法举例

假设你正在寻找一个例子，如何用 c 语言写一个字符串。

(网上可能有很多这方面的教程！)网上查不到，但是听说过一个节点。名为“Snippet”的 JS 包。它有多种编程语言的代码示例，如 Javascript、Java、C (C#和 C++)、Python 等。
你查看一下，在`snippets`文件夹里，你看到一个文件夹叫`c`，打开它*嘣*；你找到了某个`hello_world_c.c`剧本。

这正是你要找的！现在你可以把这段简单的代码珍藏在心里，也可以用它。

## 安装

查看[这篇文章](https://dev.to/revelmind/new-node-package-2ond)或者使用命令:

```
npm install @matrixoc/snippet 
```

或者从 [GitHub](https://github.com/opensource-matrix/snippet) 获取，如果你不想使用下载功能，使用命令:

```
git remote add origin https://github.com/opensource-matrix/snippet.git
git pull 
```

## 支持的语言

> 在这篇文章中
> 
> *   C
> *   C#
> *   C++
> *   JavaScript/节点
> *   红宝石
> *   透明的
> *   计算机编程语言
> *   超文本标记语言
> *   一批
> *   壳
> *   Java 语言(一种计算机语言，尤用于创建网站)

添加一门语言很*超级*简单，下面就让我们来深入了解一下[这里](#creating-a-language)！

# 创造语言

1.  用编程语言的名称在`snippets`中创建一个新文件夹。
2.  运行命令` node add language[语言名称][语言文件扩展名(用空格分隔)]

所以例如，要添加 Objective-C，我们可以这样做:
 ``node addLanguage objective-c .objc .m`` 

 ``## 结论

如果你有任何问题，创建一个[期](https://github.com/opensource-matrix/snippet/issues/new)，或查看[这一期](https://github.com/opensource-matrix/snippet/issues/3)，就这样，感谢阅读！

如果你想投稿，去 GitHub 库，链接[这里](#installation)，感谢帮忙的投稿人！``