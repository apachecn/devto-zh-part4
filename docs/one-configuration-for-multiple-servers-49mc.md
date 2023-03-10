# 多台服务器的一种配置

> 原文：<https://dev.to/marekdano/one-configuration-for-multiple-servers-49mc>

*您是否曾经问过自己，如何将同一个应用程序部署到具有不同设置的不同服务器上？当我们开始开发前端应用程序时，我们也问过同样的问题。*

## 要求

在我的公司，我们开发 web 应用程序。其中一个应用程序是用 Angular 8 编写的，必须部署到我们的几个客户的本地内部服务器或 Azure one 上。该应用程序必须配置为与我们的 web API 通信，还必须定义全局设置。对于每个应用程序来说，web API 所处的位置是不同的。因此，我们决定有一个本地配置文件，其中包含应用程序所需的所有设置。这些设置应该在我们的应用程序中使用。

我们拥有的类似文件如下:

```
{  "version":  "v1.0.0",  "ops_environment":  "production",  "authentication":  true,  "authenticationType":  "azure",  "apiUrl":  "https://sample-app.azurewebsites.net/ws",  "tenant":  "sample-tenant.com",  "signalr":  {  "url":  "https://sample-app.azurewebsites.net/ws/signalr",  "hubName":  "SampleHub"  }  } 
```

在哪里

*   **身份验证类型**可以是 **azure** 或 **windows** 取决于应用程序部署的位置——选项是 windows 服务器上的 azure 或 IIS。
*   **apiUrl** 和 **singlar.url** 是所需 web API 的 Url。

## 我们的实现

经过研究，我们发现对我们来说最好的解决方案是为我们的所有客户端构建并发布一次应用程序，并在应用程序中的某个位置使用上面已经定义的设置创建一个配置文件 **config.json** 。

我们决定将 config.json 文件放在 **assets** 文件夹下，而不是放在构建根目录或其他地方。如果我们在**资产**文件夹下没有它，那么我们将不得不创建 web API 来获取配置文件并在初始化整个应用程序时读取设置。因为我们将 config.json 文件放在 assets 下，所以每次应用程序启动时都会加载到浏览器中，我们可以在应用程序中设置所需的设置。

角度服务中使用 **apiUrl** 的设置，认证过程中使用 **authenticationType** ，我们的 app 中使用 **signalr** 设置 signal-R 客户端。

我们的应用程序结构中包含 config.json 文件的部分是:

```
...

├── SampleApp
|   ├── src
|   |   ├── app
|   |   |   ├── pages
|   |   |   ├── shared

...

|   |   ├── assets
|   |   |   └── config.json
|   |   ├── environments

...

|   |   └── index.html

...

|   ├── package.json
|   └── angular.json

...

└── README.md 
```

## 结论

我们相信可能有更好的解决方案来处理我们的问题。如果您认为我们可以用不同的方式完成，请告诉我们；)