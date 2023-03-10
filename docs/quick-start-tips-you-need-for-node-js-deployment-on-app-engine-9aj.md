# 在 App Engine 上部署 Node.js 所需的快速入门技巧

> 原文：<https://dev.to/foureyedraven/quick-start-tips-you-need-for-node-js-deployment-on-app-engine-9aj>

当尝试在 App Engine 上部署节点应用程序时，您可能会遇到各种难以诊断的阻塞。

昨天，我的应用程序收到了 502 和 500 个错误，这取决于调整，应用引擎上的官方 Google 文档留下了需要的部署要求。

[以下是在 App Engine 上部署 nodejs 项目的官方文档:

*   [https://cloud . Google . com/app engine/Docs/standard/nodejs/config/appref](https://cloud.google.com/appengine/docs/standard/nodejs/config/appref)
    文档说你需要“至少运行时:nodejs10”，但是我需要指定的处理程序(见下文)。

*   [https://cloud . Google . com/app engine/docs/standard/nodejs/configuring-your-app-with-app-YAML](https://cloud.google.com/appengine/docs/standard/nodejs/configuring-your-app-with-app-yaml)

*   [https://cloud . Google . com/app engine/docs/standard/nodejs/Quick Start](https://cloud.google.com/appengine/docs/standard/nodejs/quickstart)
    app . YAML 未在快速入门中提及】

简而言之，我的解决方案是确保我在项目根目录中有一个 app.yaml 文件，带有 url 处理程序:

```
# [START runtime]
runtime: nodejs10
# [END runtime]

# [START handlers]
handlers:
 - url: /
   static_files: build/index.html
   upload: build/index.html

 - url: /(.*)
   static_files: build/\1
   upload: build/(.*)
# [END handlers] 
```

Enter fullscreen mode Exit fullscreen mode

您需要检查的其他事项:

*   您的 app.yaml 文件位于项目的根目录中
*   如果你正在付款，请确保付款已设置好
*   确保你在`gcloud app deploy`之前跑了`npm run build`(或同等速度)
*   在`start.js`(或等效物)中，将默认端口设置为 8080

如果这里有其他提示或过时的信息，请告诉我。