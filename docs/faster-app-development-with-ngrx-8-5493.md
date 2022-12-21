# 使用 NgRx 8 加快应用程序开发

> 原文：<https://dev.to/auth0/faster-app-development-with-ngrx-8-5493>

TL；DR: NgRx 8 引入了许多改进，包括 createAction、createReducer 和 createEffect。这些助手函数极大地减少了样板文件(没有双关语的意思)，从而提高了开发人员的生产力。

NgRx 8:巨大的胜利
Angular 8 发布后不久，NgRx 团队发布了他们自己的版本 8。这是一个对库进行了大量改进的大版本。虽然我会让你阅读 Tim Deschryver 的官方发布公告，但这里有一些亮点:

*   约翰·帕帕和沃德·贝尔的 angular-ngrx-data 现已作为@ngrx/data 正式成为 ngrx 的一部分
*   运行时检查现在可以检查状态和动作没有变异并且是可序列化的(这些取代了 ngrx-store-freeze 包)
*   由于模拟选择器，完全隔离的单元测试现在是可用的
*   @ngrx/router 有一些改进，包括一个新的最小 routerState 选项
*   现在在约翰爸爸的 Angular Snippets VS 代码扩展中有 NgRx 片段
*   Alex Okrushko 和 Wes Grimes 已经加入 NgRx 战队(当之无愧！)

不过，我把最好的留到了最后，这也是本文的主题。
[继续阅读📖](https://auth0.com/blog/faster-app-development-with-ngrx-8/?utm_source=dev&utm_medium=sc&utm_campaign=ngrx8_fastdev)