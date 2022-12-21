# 在容器中托管运行时函数时设置秘密

> 原文：<https://dev.to/kaiwalter/setting-secrets-when-hosting-functions-runtime-in-a-container-4obp>

Functions runtime v2 使得利用一个预装的[映像](https://hub.docker.com/_/microsoft-azure-functions-base)变得非常容易，将你的 Functions 应用打包到一个映像中，并将其托管在 Docker、Kubernetes 或 [my case - on Service Fabric](https://dev.to/kaiwalter/azure-functions-on-service-fabric-40n8) 上。

然而，当调用受`AuthorizationLevel.Function`或`AuthorizationLevel.Admin`保护的 HTTP 触发函数以及调用其中一个[管理端点](https://github.com/Azure/azure-functions-host/wiki/Admin-API)时，您需要知道各自的密钥。在容器 CI/CD 场景中，可能需要您预先设置密钥，以便对主机进行健康检查&管理调用，或者将函数与一些 API 网关等连接起来。

但是如果没有向函数主机提供键，它将只生成新的键——这将导致不同的键，当相同的函数主机在多个容器实例上操作时。如果不进入正在运行的容器，就不可能确定这些代码供以后使用。

# 如何提供秘密？

第一个元素是一个`host.json`文件，它设置了所需的键——一个主键，一个默认功能键，如果需要的话，还可以为每个功能设置单独的键。

```
{  "masterKey":  {  "name":  "master",  "value":  "asGmO6TCW/t42krL9CljNod3uG9aji4mJsQ7==",  "encrypted":  false  },  "functionKeys":  [  {  "name":  "default",  "value":  "asGmO6TCW/t42krL9CljNod3uG9aji4mJsQ7==",  "encrypted":  false  }  ]  } 
```

Enter fullscreen mode Exit fullscreen mode

第二步是将该文件放在一个文件夹中，以便函数主机可以提取它。

在[功能主机版本 2.0.12701](https://github.com/Azure/azure-functions-host/releases/tag/v2.0.12701) 之前，在`Dockerfile`

*   必须将`host.json`复制到`runtime\secrets`文件夹
*   必须授予对文件夹的写访问权限，以便函数主机可以修改文件
*   主机必须配置为使用文件系统中的机密

```
ADD Secrets\\host.json C:\\runtime\\Secrets\\host.json
USER ContainerAdministrator
RUN icacls "c:\runtime\secrets" /t /grant Users:M
USER ContainerUser
ENV AzureWebJobsSecretStorageType=files 
```

Enter fullscreen mode Exit fullscreen mode

在版本 *2.0.12701* 中，这可以通过简单地将文件放在一个常规文件夹中并设置环境变量`FUNCTIONS_SECRETS_PATH`来实现，而不需要与 ACLS:

```
ADD Secrets\\host.json C:\\Secrets\\host.json
ENV FUNCTIONS_SECRETS_PATH=C:\Secrets
ENV AzureWebJobsSecretStorageType=files 
```

Enter fullscreen mode Exit fullscreen mode

# 附加奖金

新版本现在还允许将日志放在您选择的文件夹中——而不是运行时的子文件夹之一:

```
ENV FUNCTIONS_LOG_PATH=C:\Logs 
```

Enter fullscreen mode Exit fullscreen mode