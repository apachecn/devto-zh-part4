# 在 Node.js 中调用 Kubernetes API

> 原文：<https://dev.to/craicoverflow/invoking-the-kubernetes-api-in-node-js-fbi>

如果您可以直接调用 Kubernetes API，生活会变得简单一点。这就是为什么 [GoDaddy](https://www.godaddy.com/) 决定让 [kubernetes-client](https://github.com/godaddy/kubernetes-client) 成为 kubernetes 的一个易于使用的 Node.js 客户端。

它被列为官方支持的 Kubernetes JavaScript 客户端库。这一点很重要，因为它得到了 [Kubernetes SIG API 机制](https://github.com/kubernetes/community/tree/master/sig-api-machinery)的支持，这意味着它可以跟上 Kubernetes API 规范的变化。这也意味着您的支持请求和问题更有可能得到及时的响应。

## 安装

使用 npm 安装:

```
npm install kubernetes-client --save 
```

## 配置

kubernetes-client 在运行时基于 Swagger/OpenAPI 定义生成一个 Kubernetes API 客户端。

kubernetes-client 将自动配置自己，首先尝试从`KUBECONFIG`环境变量加载配置，然后在`~/.kube/config`中加载。如果它还没有找到任何东西，那么它将尝试使用一个集群内服务帐户，并最终采用默认的代理配置作为最后的手段。

简单的配置:

```
const { Client, KubeConfig } = require('kubernetes-client');
const Request = require('kubernetes-client/backends/request');

async function initKubeClient() {
  const kubeconfig = new KubeConfig();

  if (process.env.NODE_ENV === 'production') {
    kubeconfig.loadFromCluster();
  } else {
    kubeconfig.loadFromDefault();
  }

  const backend = new Request({ kubeconfig });
  const kubeclient = new Client({ backend });

  await kubeclient.loadSpec();

  return kubeclient;
} 
```

## 使用

kubernetes-client 将路径项对象映射到以 HTTP 方法结尾的对象链。例如，`api/v1/namespaces/myproject/configmaps`映射到`.api.v1.namespaces('myproject').configmaps`。这种映射逻辑可用于所有资源类型。

您可以参考 [Kubernetes API 文档](https://kubernetes.io/docs/home/)来查找特定资源的 API 端点。

## 部署

让我们学习如何与[部署](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.13/#deployment-v1-apps)进行交互。我选择部署作为例子，因为它们是常用的，同样的逻辑可以应用于所有其他资源。

### 创建部署

您可以通过向 Kubernetes API 发出`POST`请求来创建部署。

```
const deploymentManifest = require('./nginx-deployment.json');

const createdDeployment = await kubeclient.apis.apps.v1.namespaces(namespace).deployments.post({ body: deploymentManifest });

console.log('NGINX Deployment created:', createdDeployment); 
```

您还可以验证部署是使用`kubectl`创建的。

```
$ kubectl get deployments
NAME               DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   1         1         1            0           1m 
```

### 修改部署

要修改资源的一部分，您可以发送一个`PATCH`请求。

```
const labels = {
  metadata: {
    labels: {
      environment: 'DEVELOPMENT'
    }
  }
};

const modified = await kubeclient.apis.apps.v1.namespaces(namespace).deployments(deploymentManifest.metadata.name).patch({ body: labels });

console.log('Deployment modified:', modified.body.metadata); 
```

### 更新部署

通过发出一个`PUT`请求，你可以替换整个资源。

```
const updated = await kubeclient.apis.apps.v1.namespaces(namespace).deployments(deploymentManifest.metadata.name).put({ body: deploymentManifest });

console.log('Deployment updated:', updated); 
```

### 提取部署

获取命名空间中的所有部署。

```
const deployment = await kubeclient.apis.apps.v1.namespaces(namespace).deployments(deploymentManifest.metadata.name).get();

console.log('Deployment:', deployment); 
```

通过在命名空间中提取单个部署。

```
const deployments = await kubeclient.apis.apps.v1.namespaces(namespace).deployments.get();

console.log('Deployments:', deployments); 
```

获取所有命名空间中的所有部署。

```
const deployments = await kubeclient.apis.apps.v1.deployments.get();

console.log('Deployments (all namespaces):', deployments); 
```

您可以选择指定一个查询字符串对象`qs`来获取请求。例如，对[标签选择器](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)进行过滤。

```
const deployments = await kubeclient.apis.apps.v1.namespaces(namespace).deployments.get({ qs: { labelSelector: 'app=nginx'}});

console.log(deployments); 
```

您可以使用 Kubernetes API 文档来查看资源还有哪些查询参数可用。

### 删除一个部署

可以使用`DELETE`删除部署。

```
const removed = await kubeclient.apis.apps.v1.namespaces(namespace).deployments(deploymentManifest.metadata.name).delete();

console.log('Deployment deleted:', removed); 
```

## 自定义资源

使用 kubernetes-client，可以用 CustomResourceDefinition 扩展 Kubernetes API。

在这个例子中，我为 GitHub 帐户创建了一个 CustomResourceDefinition。

**githubaccount-crd.json**

```
{  "kind":  "CustomResourceDefinition",  "spec":  {  "scope":  "Namespaced",  "version":  "v1",  "versions":  [{  "name":  "v1",  "served":  true,  "storage":  true  }],  "group":  "craicoverflow.github.io",  "names":  {  "shortNames":  [  "ga"  ],  "kind":  "GitHubAccount",  "plural":  "githubaccounts",  "singular":  "githubaccount"  }  },  "apiVersion":  "apiextensions.k8s.io/v1beta1",  "metadata":  {  "name":  "githubaccounts.craicoverflow.github.io"  }  } 
```

### 创建自定义资源定义

```
const crd = require('./githubaccount-crd.json');

const createCRD = await kubeclient.apis['apiextensions.k8s.io'].v1beta1.customresourcedefinitions.post({ body: crd });

console.log('CustomResourceDefinition created:', createCRD); 
```

然后，您需要为新的 CustomResourceDefinition 添加端点到`kubeclient`。

```
kubeclient.addCustomResourceDefinition(crd); 
```

### 创建自定义资源

现在我们已经创建了`GitHubAccount` CRD，我们将能够创建一个`GitHubAccount`定制资源。

**githubaccount-cr.json**

```
{  "apiVersion":  "craicoverflow.github.io/v1",  "kind":  "GitHubAccount",  "metadata":  {  "name":  "craicoverflow"  },  "spec":  {  "login":  "craicoverflow",  "blog":  "https://endaphelan.me",  "bio":  "// TODO: Add a bio",  "type":  "User",  "public_repos":  "52"  }  } 
```

```
const customResource = require('./githubaccount-crd.json');

const createdAccount = await kubeclient.apis[crd.spec.group].v1.namespaces(namespace).githubaccounts.post({ body: customResource });

console.log('Created GitHubAccount:', createdAccount); 
```

### 获取自定义资源

获取 GitHubAccount 自定义资源。

```
const githubAccount = await kubeclient.apis[crd.spec.group].v1.namespaces(namespace).githubaccounts(customResource.metadata.name).get();

console.log('GitHubAccount:', githubAccount); 
```

获取命名空间中的所有 GitHubAccount 自定义资源。

```
const allAccounts = await kubeclient.apis[crd.spec.group].v1.namespaces(namespace).githubaccounts.get();

console.log('GitHubAccountList:', allAccounts); 
```

### 删除自定义资源

```
const deleteAccounts = await kubeclient.apis[crd.spec.group].v1.namespaces(namespace).githubaccounts(customResource.metadata.name).delete();

console.log('Deleted GitHubAccount:', deleteAccounts); 
```

### 删除自定义资源定义

```
const deletedCRD = await kubeclient.apis['apiextensions.k8s.io'].v1beta1.customresourcedefinitions(crd.metadata.name).delete();

console.log('GitHubAccount CRD deleted:', deletedCRD); 
```

## 错误处理

当请求失败时，kubernetes-client 输出一个 HTTP 错误。下面的示例通过在创建部署时处理一个`409 Conflict`错误并替换资源来模拟`kubectl apply`。

```
try {
  const createdDeployment = await kubeclient.apis.apps.v1.namespaces(namespace).deployments.post({ body: deploymentManifest });

  console.log('Deployment created:', createdDeployment);
} catch (err) {

  if (err.statusCode === 409) {
    const updatedDeployment = await kubeclient.apis.apps.v1.namespaces(namespace).deployments(deploymentManifest.metadata.name).put({ body: deploymentManifest });

    console.log('Updated updated:', updatedDeployment);
  }
} 
```

## 观看资源

您可以使用监视端点从资源中流式传输事件。常见的事件类型有`ADDED`、`MODIFIED`、`DELETED`，它们表示该端点上有新的或已更改的资源。

### 观看部署

```
const deploymentStream = await kubeclient.apis.apps.v1.watch.namespaces(namespace).deployments.getObjectStream();

deploymentStream.on('data', event => {
  if (event.type === 'ADDED') {
    console.log('Deployment created:', event.body);
  }
}); 
```

### 观看定制资源

```
const githubAccountStream = await kubeclient.apis[crd.spec.group].v1.watch.namespaces(namespace).githubaccounts.getObjectStream();

githubAccountStream.on('data', event => {
  if (event.type === 'CLOSED') {
    console.log('GitHub account deleted:', event);
  }
}); 
```

要查看`watch`的运行，从终端创建或更新 GitHubAccount 自定义资源，您将看到事件流在 Node.js 应用程序中输出一个新事件。

```
cat <<EOF | kubectl apply -n myproject -f -
apiVersion: craicoverflow.github.io/v1
kind: GitHubAccount
metadata:
  name: testuser
spec:
  bio: ''
  blog: https://example.com
  login: testuser
  public_repos: "100"
  type: User EOF 
```

## 附加资源

*   godaddy/kubernetes-client -这个库的文档非常出色。除此之外，还有许多伟大的[例子](https://github.com/godaddy/kubernetes-client/tree/master/examples)。
*   craicoverflow/kubernetes-client-nodejs-example-包含本指南所有代码的示例项目。
*   Kubernetes 文档-Kubernetes 的官方文档。