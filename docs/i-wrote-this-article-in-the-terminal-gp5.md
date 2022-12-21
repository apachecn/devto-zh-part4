# 我在终端写了这篇文章

> 原文：<https://dev.to/hagnerd/i-wrote-this-article-in-the-terminal-gp5>

这篇文章是用我的新工具 [devto](https://github.com/hagnerd/devto#readme) 写的，这是一个创建新文章并把它们发布到 Dev.to 的 CLI。它非常简单，可能有点粗糙。

## 现有技术

这个工具很大程度上受到了 Tim Deschryver 的启发，他写了这篇[文章](https://dev.to/timdeschryver/writing-a-dev-to-blog-post-with-vscode-1fn)关于一个叫做[新博客](https://marketplace.visualstudio.com/items?itemName=timdeschryver.new-blog-post)的 VS 代码扩展。如果你用的是 VS 代码，那就去看看吧！

* * *

如果你对使用 CLI 应用感兴趣，你可以在`@hagnerd/devto`全局安装它，或者用`npx @hagnerd/devto`运行它。

安装后，它将通过`devto new`在全球范围内提供。

以下命令可用:

1.  用 frontmatter 创建一篇新文章。
2.  `devto publish` -将文章推送到您的开发人员账户*
3.  `devto dashboard` -在浏览器中快速打开 dev.to/dashboard。如果你想推进一份草稿，并进行修改或预览，这是一个不错的选择。
4.  `devto reset` -删除您的全局~/。devto 文件(存储您的 API 密钥)

*我还没有用组织测试过它，所以如果某个组织的某个成员试用了它
,而 cli 表现不正常，请告诉我！

*   [npm](https://www.npmjs.com/package/@hagnerd/devto) 链接
*   [Github](https://github.com/hagnerd/devto#readme) 链接