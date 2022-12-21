# 使用 Angular 6+创建新库

> 原文：<https://dev.to/rudolfolah/creating-a-new-library-with-angular-6-30a0>

在 v6 中，Angular 项目的目录结构发生了变化，因此下面是为 Angular 6+创建新库的过程:

```
~/Code$ ng new my-angular-library --create-application false 
```

当以这种方式创建一个新的 Angular 项目时，Angular CLI 将*而不是*生成任何应用程序文件，只是基本项目工作空间。

然后你这样做:

```
~/Code$ cd my-angular-library
~/Code$ ng g library my-lib 
```

根目录`my-angular-library`可以包含许多项目，这些项目将位于`projects`目录中。你的新图书馆的路径将是`projects/my-lib`。

您可以通过在顶层工作区目录或在`projects/my-lib`目录中运行`ng build`来构建这个库。