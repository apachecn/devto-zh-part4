# Javascript 文件串联

> 原文：<https://dev.to/scottishross/javascript-file-concatenation-19n5>

嘿伙计们，

我想在我的一个应用程序中使用 [SweetAlert2](https://github.com/sweetalert2/sweetalert2) ,但是我的 JS 文件有问题。SweetAlert2 的开发人员有几个 JS 文件，似乎将相关的 JS 组合在一起。但是我使用的是 Oracle APEX，它不太允许 JS 文件相互引用(因为它们假设它们在同一个目录中)。

谁能给我指点一下如何连接这些文件，或者有没有更简单的方法？最后必须是一个文件。