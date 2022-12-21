# 如何用 Cypress 测试获取 API 请求

> 原文：<https://dev.to/mjsarfatti/how-to-test-your-fetch-requests-with-cypress-bh5>

柏树[牛逼](https://dev.to/noriste/front-end-productivity-boost-cypress-as-your-main-development-browser-5cdk)。有了 Cypress，你可以做任何事情，包括通过 stub requests 模拟你的后端，或者在你每天早上醒来的时候把新煮的咖啡送到你的床上 <sup>[1](#note-1)</sup> 。

不幸的是，Cypress 目前不支持拦截用更现代的 *[Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)* 发出的请求，而只支持不太好的老式 *XMLHttpRequest* 。

从[他们自己的文档](https://docs.cypress.io/guides/guides/network-requests.html#Testing-Strategies):

> 请注意，Cypress 目前只支持拦截 XMLHttpRequests。使用 Fetch API 的请求和其他类型的网络请求(如页面加载和`<script>`标签)将不会被拦截，也不会在命令日志中显示。见 [#95](https://github.com/cypress-io/cypress/issues/95) 了解更多细节和临时解决办法。

幸运的是，有一个非常简单但非常有效的解决方法:包含一个`fetch` polyfill，然后删除`window.fetch`以确保浏览器退回到 *XMLHttpRequest* 。

下面是简单的 3 步流程:

1.  在你的项目中安装`fetch` polyfill 包，如果你还没有

    ```
    $ npm install whatwg-fetch --save
    ~or~  
    $ yarn add whatwg-fetch 
    ```

2.  将其导入到您的项目中(您可能会在您的`index.js`文件中执行)

    ```
    /* file: src/index.js */

    import 'whatwg-fetch'; 
    ```

3.  指示 Cypress 在每次窗口加载
    前删除`window.fetch`

    ```
    /* file: cypress/support/index.js */

    // Delete window.fetch on every window load
    Cypress.on('window:before:load', win => {
      delete win.fetch;
    }); 
    ```

就这样，祝你愉快🚀

* * *

<small>备注

T3】1-尚未确认</small>