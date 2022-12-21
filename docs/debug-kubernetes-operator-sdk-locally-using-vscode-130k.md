# 使用 Vscode 在本地调试 Kubernetes Operator-sdk

> 原文：<https://dev.to/austincunningham/debug-kubernetes-operator-sdk-locally-using-vscode-130k>

我最近在 Openshift 4.1 上开始使用 Kubernetes 操作符。学到了很多东西，但还有更多的东西要学。

## 什么是运算符

Kubernetes 操作符是一种利用 Kubernetties API 创建自己的 Kubernetes 定制资源的方式。

我正在做的项目[正在使用](https://github.com/integr8ly/integreatly-operator) [Operator-sdk](https://github.com/operator-framework/operator-sdk) 框架来构建操作符，这降低了构建 Kubernetes 操作符的复杂性。

我不会去安装 Operator-sdk [docs](https://github.com/operator-framework/operator-sdk#create-and-deploy-an-app-operator) 中记录的操作符。

## 本地运行

您可以在本地运行 operator-sdk，并将其指向您的操作符的名称空间

```
operator-sdk up local --namespace=integreatly-operator 
```

> *注*:命令随 v0.15.0 变化`operator-sdk run --local --namespace=integreatly-operator`

这意味着，您不必构建容器映像并将其推送到 quay.io 或 dockerhub.com 等应用程序注册表，您只需使用本地代码库作为源代码，而不是容器映像，并使用它将您的操作员部署到您的 Openshift 4 集群。

## 设置 Vscode 进行调试

我使用 Vscode，那么如何使用`operator-sdk up local`进行调试呢？

Delve 是 golang 的调试工具，可以在这里下载[https://github . com/go-delve/delve/tree/master/Documentation/installation](https://github.com/go-delve/delve/tree/master/Documentation/installation)或者直接使用 go

```
go get -u github.com/go-delve/delve/cmd/dlv 
```

这个 Vscode Go 插件[https://marketplace.visualstudio.com/items?使用了 DelveitemName=ms-vscode。开始](https://marketplace.visualstudio.com/items?itemName=ms-vscode.Go)

你需要在`up local`命令
上用命令行开关`--enable-delve`运行 delve，例如我正在操作的操作符叫做`integreatly-operator`，所以运行它的命令如下

```
# You need to set the namespace to watch 
$ export WATCH_NAMESPACE=integreatly-operator
# You can then run the up local with delve enabled
$ operator-sdk up local --namespace=integreatly-operator --enable-delve
# you will see something like
INFO[0000] Running the operator locally.                
INFO[0000] Using namespace integreatly-operator.        
INFO[0000] Delve debugger enabled with args [--listen=:2345 --headless=true --api-version=2 exec build/_output/bin/integreatly-operator-local --] 
API server listening at: [::]:2345 
```

> **注意** : `WATCH_NAMESPACE`应该始终指向操作符的名称空间

您将需要一个针对 Vscode 的启动 json 来与 delve
的这种无头模式进行交互

```
{  "version":  "0.2.0",  "configurations":  [  {  "name":  "Integreatly Operator",  "type":  "go",  "request":  "launch",  "mode":  "auto",  "program":  "${workspaceFolder}/cmd/manager/main.go",  "env":  {  "WATCH_NAMESPACE":  "integreatly-operator"  },  "args":  []  }  ]  } 
```

启动 vscode 调试器。打开`Debug console`，你将看到来自`operator-sdk up local`命令的标准输出，你的调试器将在设置的断点上停止。
T3![Alt Text](img/5e73ec04ab084e0bc1fbac8c6efc10d3.png)T5】