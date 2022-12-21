# 局部测试角度构建

> 原文：<https://dev.to/leonelngande/test-angular-build-locally-2249>

要在本地测试您的角度构建:

1.  构建您的应用:`ng build --prod`
2.  安装 http-服务器为应用服务:`npm i -g http-server`
3.  [cd](https://en.wikipedia.org/wiki/Cd_(command)) (更改目录)到构建位置，并使用`http-server` 运行应用程序
4.  打开附加了/index.html 的 http-server url，应该看起来像这样`http://127.0.0.1:8080/index.html`

🎉