# 用 Angular 和 RxJS 构建音频播放器应用程序

> 原文：<https://dev.to/auth0/building-an-audio-player-app-with-angular-and-rxjs-4c6n>

TL；DR:在这篇文章中，你将学习如何使用 Angular 和 RxJS 开发一个音频播放器应用程序。您将使用 RxJS 处理音频操作和应用程序状态。为了保护您的应用程序，您将使用 Auth0。如果需要，您可以在这个 GitHub 资源库中找到最终代码。

创建一个音频播放器总是一项令人生畏的任务，尤其是当你考虑管理媒体的状态，对媒体事件做出反应，并在 UI(用户界面)上正确反映这些变化的时候。因此，在本文中，您将使用 Angular 和 Angular Material(以及其他一些库)来轻松应对这些挑战。

为了以一种被动的方式处理媒体回放，您将使用 RxJS 可观察对象包装 JavaScript 的音频对象，并且您还将使用 RxJS 来管理您的音频播放器的状态。

为了通过您的应用程序提供安全的用户体验，您将通过 Auth0 添加用户身份验证。

先决条件
因为您将使用 Angular，所以您需要在您的开发机器上安装 Node.js。因此，如果您还没有这样做，请转到 Node.js 的下载页面，并按照那里的说明进行操作。

[继续阅读📖](https://auth0.com/blog/building-an-audio-player-app-with-angular-and-rxjs/?utm_source=dev&utm_medium=sc&utm_campaign=angular_rxjs)