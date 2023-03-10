# Vue CLI 3 全栈应用程序结构

> 原文：<https://dev.to/anthonygore/vue-cli-3-full-stack-app-structure-20l8>

如果你正在用 Vue.js 创建一个应用程序，你很可能想要利用 Vue CLI 3 提供的最佳实践框架。

但是，如果 Vue 应用程序是全栈 JavaScript 应用程序的客户端层，例如，在“MEVN”配置(Mongo，Express，Vue，Node)中，则不完全清楚 Vue CLI 3 应该如何集成到这样的结构中。

有几种方法你可以合理地采用:

1.  把你的服务器放在一个完全独立的仓库里
2.  为您的服务器创建一个 repo，并将您的 Vue CLI 3 scaffold 放在一个子文件夹中
3.  通过修改您的 Vue CLI 3 scaffold 以与您的服务器共享，创建一个“通用”结构
4.  得出结论，不适合将 Vue CLI 3 用于全堆栈结构并自行配置。

选择其中一个选项的困难在于，在平衡最佳实践、可维护性、易用性、测试和部署的便利性等方面，每个选项都有自己的优缺点。

做出这个选择让我特别感兴趣，因为我在思考我的 [Enterprise Vue](https://vuejsdevelopers.com/courses/enterprise-vue?utm_source=vjd-blog&utm_medium=article) 课程的最佳方法，该课程的中心是创建一个 MEVN 应用程序。我希望有可能建立这个应用程序，并仍然利用 Vue CLI 3。

## 顺从权威和经验

据我所知，还没有 Vue CLI 3 在全栈配置中的“官方”例子，我想也不太可能有。

但是我们可以看看其他 JavaScipt 框架是如何解决这个问题的。

一个流行且备受推崇的全栈应用样板是在 [mean.io](https://github.com/linnovate/mean) 中为全栈 Angular 应用概述的那个。

这个样板文件，事实上我能找到的几乎所有的全栈 JavaScript 样板文件，都使用了我提到的*通用*方法，其中客户端和服务器共享同一个目录，给你一个这样的结构:

```
- client
  - components
    ...
  main.js
- server
  - routes
    ...
  index.js
  ...
package.json
... 
```

有趣的是，mean.io 样板文件还包括 Angular CLI。

## 通用文件夹结构利弊

通过观察野外的例子，这种方法似乎是构建全栈 JS 应用程序最流行的方式。

我能看到的一些优势是:

*   它是高效的，因为它允许你共享`package.json`、`node_modules`、环境变量，并且在客户机和服务器之间开放公共代码的可能性。
*   它使安装和部署变得容易，因为`package.json`中的一个命令可以用来安装/部署整个应用程序。
*   很容易阅读和理解。

缺点是，如果你想在 Vue CLI 3 中使用这种应用程序结构，你需要修改 scaffold，这并不是没有缺点。

## 在 Vue CLI 3 脚手架中容纳服务器

Vue CLI 3 并未设计为与服务器共享其空间。沿着这条路走下去，你将面临的一些问题包括:

*   它不喜欢你改变文件结构。例如，`src`文件夹不容易被重命名。
*   它劫持了`.env`文件和重要的环境变量，比如通常为服务器保留的端口。
*   一些现成的配置如 ESLint 不适合服务器文件，所以你必须手动更新它们。

不过，这些问题没有一个是不可克服的，如果你能克服它们，你将拥有一个干净的、可维护的文件夹结构，拥有 Vue CLI 3 的最佳实践和零配置优势。

* * *

喜欢这篇文章吗？

通过 *Vue.js 开发者简讯*每周在您的收件箱中获取更多类似的文章。

[点击这里加入！](https://vuejsdevelopers.com/newsletter?utm_source=devto&utm_medium=article)

* * *