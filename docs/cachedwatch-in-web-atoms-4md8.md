# Web 原子中的缓存观察

> 原文：<https://dev.to/web-atoms/cachedwatch-in-web-atoms-4md8>

在 Web Atoms 中，我们可以简单地在 getter 上写`@Watch`，它返回`Promise<T>`，当任何引用的属性改变时，getter 将被 UI 自动读取。

## 古老的方式

```
 export default class TaskListViewModel extends AtomViewModel {

   public search: string = "";

   @Inject
   private taskService: TaskService;

   @Watch
   public get tasks(): Promise<ITaskModel[]> {
      return this.taskService.list(this.search);
   }

} 
```

Enter fullscreen mode Exit fullscreen mode

有一个小问题，如果我们在 UI 的不同部分反复读取`tasks`属性，每次它都会将 REST API 发送到后端，结果仍然是一样的。

所以我们引入了,`@CachedWatch`,它将缓存上一次成功的 api 调用的结果，除非任何输入参数发生了变化。

## 新方式

```
 export default class TaskListViewModel extends AtomViewModel {

   public search: string = "";

   @Inject
   private taskService: TaskService;

   @CachedWatch
   public get tasks(): Promise<ITaskModel[]> {
      return this.taskService.list(
         this.search,
         this.newCancelToken("tasks"));
   }

} 
```

Enter fullscreen mode Exit fullscreen mode

我们在这里引入两个概念，首先是`@CachedWatch`和`CancelToken`。假设用户在搜索文本框中输入内容，并且在每次键盘事件中，该属性都会刷新。但是一旦用户立即输入下一个字符，我们想取消以前的搜索。

我们可以通过使用与属性名相同的键创建`newCancelToken`来实现这一点，它将创建并返回一个新的令牌，并取消相同键的先前令牌。

这将中止先前的 REST API。

Web Atoms 智能地将 REST API 调用延迟 100 毫秒，因此如果立即取消之前的令牌，它将根本不会启动之前的请求。

* * *

Web Atoms 是功能强大的 MVVM JavaScript 框架，完全用 TypeScript 编写，可以扩展到任何其他平台。目前在 Web 和 Xamarin 上支持。形式

## 潜入样本

[https://www.webatoms.in/samples.html#contextId=0](https://www.webatoms.in/samples.html#contextId=0)

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[we B- atoms](https://github.com/web-atoms)/[core](https://github.com/web-atoms/core)

### MVVM 浏览器 JavaScript 框架。表单，写 TSX/TypeScript 代替 Xaml 和 C#，热重装 Live 发布的 Xamarin。表单应用程序。

<article class="markdown-body entry-content container-lg" itemprop="text">

[![Action Status](img/cfa2fdbb651ac020b318dc872b0e8974.png)](https://github.com/web-atoms/core/actions)[![npm version](img/281097dbfee702a2db5f84f4ea1dd388.png)](https://badge.fury.io/js/%40web-atoms%2Fcore)[![codecov](img/6cd68ebe76fa6f8061493408508cc5a5.png)](https://codecov.io/gh/web-atoms/core)

# 网络原子核心

Web Atoms Core 是一个 UI 抽象框架和强大的 MVVM 模式，用于设计现代 Web 和移动应用程序。

## Xamarin。表单功能

1.  使用 VS 代码开发 Xamarin。形式
2.  编写 TypeScript 而不是 C#
3.  写 TSX (JSX)而不是 Xaml
4.  已发布应用的实时热重新加载

## Web 功能

1.  抽象原子成分
2.  抽象设备 API(浏览器服务、消息广播)
3.  没有 CSS 的主题和样式支持
4.  一次性、单向和双向绑定支持
5.  简单依赖注入
6.  内置简单的单元测试框架
7.  UMD 模块支持
8.  具有强大验证功能的全功能 MVVM 框架

## 文件夹结构

1.  网站的所有视图必须放在“src”文件夹内的“web”文件夹下。
2.  Xamarin 表单的所有视图必须放在“src”文件夹内的“xf”文件夹下。

### 示例文件夹结构

```
src
+--images
|  +--AddButton.svg
|
+--view-Models
|  +--TaskListViewModel.ts
|  +--TaskEditorViewModel.ts
|
+--web
|  +--tasks
|     +--TaskListView.tsx
|     +--TaskEditorView.tsx
|
+--xf
   +--tasks
      +--TaskListView.tsx
      +--TaskEditorView.tsx 
```

### 示例视图

…</article>

[View on GitHub](https://github.com/web-atoms/core)