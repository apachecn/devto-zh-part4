# Typescript 和 Google Apps 脚本

> 原文：<https://dev.to/bugmagnet/typescript-and-google-apps-script-194k>

今天我学会了如何使用[打字稿](https://www.typescriptlang.org/)。我有一对稳步增长的 [Google Apps Script](https://developers.google.com/apps-script/) (GAS)项目，Typescript 帮助我在一些令人讨厌的意外出现之前抓住它们。

这也是一个简单的过程:安装 npm 模块并设置几个配置文件。在那之后，我重新命名了我所有的。js 到。ts 并开始放入类型注释。很快，VSCode 中的“问题”选项卡开始被通知填满。修复后，我把代码上传到 Google Sheets，然后在在线脚本编辑器里看了看，半信半疑地认为会一团糟。但是不乱，只清理 ES3 JavaScript。

我还对 [ts2gas](https://github.com/grant/ts2gas) 感到惊喜——在 [clasp](https://developers.google.com/apps-script/guides/clasp) 管道中介于 VSCode 和 Google Sheets 之间的代码。ts2gas 是一个“将文件类型脚本转换为 Google Apps 脚本的功能”，它也做了出色的工作，包括将 ES6(包括类、let、const 等)转换为 gas(当前)使用的 ES3。

在我之前关于 javascript 的帖子中，我已经用 Babel 完成了到 ES3 的转换(对于那些仍然困在那个星球上的人。)看来我也要打字和复印它们了。

务必小心使用 ts2gas。我刚刚遇到了一个关于它的转换的问题，我刚刚[记录了这个问题](https://github.com/grant/ts2gas/issues/45)。我也有一个 [StackOverflow](https://stackoverflow.com/questions/57250920/why-does-this-es6-function-have-this-typescript-error-and-what-can-i-do-about-it) 的相关问题。