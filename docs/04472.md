# 在多环境设置中，使用 kubectl、kustomize 和 helm 在 Kubernetes 上部署应用程序

> 原文：<https://dev.to/ama/react-app-deployment-on-kubernetes-with-kubectl-kustomize-and-helm-in-a-multi-environment-setup-5b1o>

大多数应用程序依赖于外部因素，这些因素根据其部署的环境而具有不同的值。我们通常使用环境变量来表示[。你猜怎么着？React 的大部分
app 也有这个需求。在这篇博文中，介绍了一种在 Kubernetes 集群上多阶段部署 Create React 应用程序的简洁(er)方法。您可以使用这种方法无缝集成到您的持续部署管道中。](https://en.wikipedia.org/wiki/Environment_variable)

首先，我将向您展示如何设置 React 应用程序，然后指导您在 Kubernetes 上进行多种部署。你将使用原生`kubectl`命令，使用[头盔](https://helm.sh/)，使用 [kustomize](https://kustomize.io/) ，并最终使用 [skaffold](https://skaffold.dev/) 。

[示例 app]((https://github.com/CodepediaOrg/multi-stage-react-app-example)) 显示 [www.bookmarks.dev](https://www.bookmarks.dev) 上发布的最新公共书签。根据应用程序构建的环境，它会在导航栏中显示环境名称，并且标题的颜色会有所不同。

源代码可在 [Github](https://github.com/CodepediaOrg/multi-stage-react-app-example) 上获得

## TLDR；

创建一个 *config.js* 文件，将环境变量注入到`window`对象(如*窗口)中。REACT _ APP _ API _ URL = '【https://www.bookmarks.dev/api/public/bookmarks】T4'*)。将该文件添加到 react 应用程序的 *public* 文件夹中。对 react 应用程序进行 Dockerize，并在 Kubernetes 部署时覆盖容器中的 *config.js* 文件——您可以使用 Kubernetes configMaps，通过本地 kubectl 命令、kustomize 或 helm 来实现。

*   目录{:目录}

## 先决条件

要在本地的 Kubernetes 上运行这个应用程序，请确保你的 [Docker Desktop](https://github.com/kubernetes/minikube) 启用了 Kubernetes，这是我用来测试的，或者安装了 [minikube](https://github.com/kubernetes/minikube) 。如果您有帐户，也可以直接在云中部署它。

## React App 设置

本教程中介绍的 react 应用程序是用 [create-react-app](https://github.com/facebook/create-react-app) 构建的。

### `public`文件夹

你需要在[公共文件夹](https://create-react-app.dev/docs/using-the-public-folder)中添加一个 [config.js](https://github.com/CodepediaOrg/multi-stage-react-app-example/blob/master/public/config.js)
。webpack 不会对此进行处理。相反，它将原封不动地复制到 *build* 文件夹中。要引用`public`文件夹中的文件，需要使用名为`PUBLIC_URL` :
的特殊变量

```
 <head>
       .....
       React App
       <script src="%PUBLIC_URL%/config.js"></script>
     </head> 
```

Enter fullscreen mode Exit fullscreen mode

*config.js* 文件的内容:

```
window.REACT_APP_API_URL='https://www.bookmarks.dev/api/public/bookmarks'
window.REACT_APP_ENVIRONMENT='LOCAL'
window.REACT_APP_NAVBAR_COLOR='LightBlue' 
```

Enter fullscreen mode Exit fullscreen mode

> 通常 API_URL 会根据环境的不同而指向不同的 URL，但这里总体上是一样的。

你可以在`window`对象上设置你的环境变量。这些就是上面提到的属性。确保它们是唯一的，所以一个好的做法是添加前缀`REACT_APP_`，如[添加定制环境变量](https://create-react-app.dev/docs/adding-custom-environment-variables)中所建议的。

> 警告:不要在你的 React 应用中存储任何秘密(比如私有 API 密匙)!环境变量嵌入到构建中，这意味着任何人都可以通过检查应用程序的文件来查看它们。

此时，您可以按照您所知道的方式在本地运行和构建应用程序:

```
npm install npm start 
```

Enter fullscreen mode Exit fullscreen mode

> 我推荐使用 [nvm](https://github.com/nvm-sh/nvm) 在本地运行 NodeJS

然后在 [http://localhost:3000](http://localhost:3000) 访问它

### 为什么不使用[中介绍的`process.env`方法添加自定义环境变量](https://create-react-app.dev/docs/adding-custom-environment-variables)

静态 web 应用程序的运行时是浏览器，在那里你不能访问`process.env`，因此依赖于环境的值必须在此之前设置，即在**构建时**。
如果您从本地机器进行部署，您可以轻松控制环境变量，为您需要的环境构建应用程序，然后部署它。kustomize 和 skaffold 之类的工具，让这在 Kubernetes 世界中变得轻而易举，您将在本文后面发现这一点。

但是如果您遵循连续部署方法，您通常会有几个步骤，这形成了所谓的**管道**:

1.  将你的代码提交给一个存储库，托管在类似于 [GitHub](https://github.com/) 的地方
2.  您的构建系统会得到通知
3.  构建系统编译代码并运行单元测试
4.  创建图像并将其推送到注册中心，例如 [Docker Hub](https://hub.docker.com/) 。
5.  从那里，您可以部署映像

这个想法是在不同的环境下尽可能少的重复步骤。使用这篇博文中介绍的方法，只需要第五步**(部署)**，这里我们有特定于环境的配置。

## 将应用容器化

首先，让我们构建一个 docker 容器，用于 Kubernetes 上的部署。容器化应用程序需要一个基础映像来创建容器的实例。

### 创建 Dockerfile

项目根目录
中的[Docker 文件](https://github.com/CodepediaOrg/multi-stage-react-app-example/blob/master/Dockerfile)包含构建 Docker 镜像所需的步骤:

```
# build environment
FROM node:12.9.0-alpine as build
WORKDIR /app

ENV PATH /app/node_modules/.bin:$PATH
COPY package.json /app/package.json
RUN npm install --silent
RUN npm config set unsafe-perm true #https://stackoverflow.com/questions/52196518/could-not-get-uid-gid-when-building-node-docker
RUN npm install react-scripts@3.0.1 -g --silent
COPY . /app
RUN npm run build

# production environment
FROM nginx:1.17.3-alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"] 
```

Enter fullscreen mode Exit fullscreen mode

它使用[多阶段构建](https://docs.docker.com/develop/develop-images/multistage-build/)来构建 docker 映像。第一步，在[节点 alpine 映像](https://hub.docker.com/_/node/)上构建 React 应用，第二步，将它部署到 [nginx-alpine 映像](https://hub.docker.com/_/nginx)。

### 构建 docker 图像

要构建 docker 映像，在项目的根目录下运行以下命令:

```
docker build --tag multi-stage-react-app-example:latest . 
```

Enter fullscreen mode Exit fullscreen mode

此时，您可以通过发出以下命令在 docker 中运行应用程序:

```
docker run -p 3001:80 multi-stage-react-app-example:latest 
```

Enter fullscreen mode Exit fullscreen mode

我们将 nginx 端口`80`转发到`3001`。现在您可以在 [http://localhost:3001](http://localhost:3001) 访问该应用程序

> 注意，该环境是本地的，因为它使用“原始的” *config.js* 文件

### 推送到码头仓库

您还可以将图像推送到 docker 存储库中。下面是一个将它推送给 dockerhub 上的 codepediaorg 组织的例子:

```
docker tag multi-stage-react-app-example codepediaorg/multi-stage-react-app-example:latest
docker push codepediaorg/multi-stage-react-app-example:latest 
```

Enter fullscreen mode Exit fullscreen mode

## 部署到 Kubernetes

现在，您可以基于您创建的图像获取一个 docker 容器，并将其部署到 kubernetes。

为此，您需要做的就是创建一个 Kubernetes 服务和部署:

```
apiVersion: v1
kind: Service
metadata:
  labels:
    app.kubernetes.io/component: service
  name: multi-stage-react-app-example
spec:
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: 80
  selector:
    app: multi-stage-react-app-example
  type: NodePort
--------
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app.kubernetes.io/component: service
  name: multi-stage-react-app-example
spec:
  replicas: 1
  selector:
    matchLabels:
      app: multi-stage-react-app-example
  template:
    metadata:
      labels:
        app.kubernetes.io/component: service
        app: multi-stage-react-app-example
    spec:
      containers:
        - name: multi-stage-react-app-example
          image: multi-stage-react-app-example:latest
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 80 
```

Enter fullscreen mode Exit fullscreen mode

### Kubernetes 上下文和名称空间

在运行任何`kubectl apply`命令之前，重要的是要知道要对什么[上下文](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/#context)
和[名称空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)应用命令。

验证这一点最简单的方法是安装 [kubectx](https://github.com/ahmetb/kubectx) ，然后发出`kubectx`来获取
当前上下文和`kubens`当前 namespac。默认的名称空间通常被称为`default`。在这篇博文中，我们对本地`docker-desktop`上下文和`default`名称空间进行操作。

现在您已经知道了 kubernetes 对象将被应用到哪里，您可以将它们添加到一个文件中，比如
[deploy-to-kubernetes . YAML](https://dev.toTODO%20add%20link)并应用下面的命令:

```
kubectl apply -f deploy-to-kubernetes.yaml 
```

Enter fullscreen mode Exit fullscreen mode

这将创建类型为[节点端口](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types)的`multi-stage-react-app-example`服务。
您可以通过列出所有服务来验证其存在

```
kubeclt get svc 
```

Enter fullscreen mode Exit fullscreen mode

或者用`kubectl get svc | grep multi-stage-react-app-example`grep

### 端口转发

要访问 Kubernetes 集群中的应用程序，您可以使用[端口转发](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/)。转发之前创建的服务的命令是

```
kubectl port-forward svc/multi-stage-react-app-example 3001:80 
```

Enter fullscreen mode Exit fullscreen mode

> 注意服务名前的`svc`

这个命令将本地端口`3001`转发到部署文件中指定的容器端口`80`。

现在您可以在 [http://localhost:3001](http://localhost:3001) 访问容器内部的应用程序，其中
使用了**本地**环境。

> 你可能想点击`Ctrl + Shift + R`来强制刷新浏览器中的网站(Chrome 可能已经缓存了旧版本)

### 拆掉创建的 Kubernetes 对象

要删除创建的服务和部署，发出以下命令

```
kubectl delete -f deploy-to-kubernetes.yaml 
```

Enter fullscreen mode Exit fullscreen mode

### 让应用部署感知环境

记住我们持续交付管道的目的:让应用程序在部署到集群时“意识到”环境。

#### 创建配置图

您首先创建一个[配置图](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)。
我们将从[环境/开发属性](https://github.com/CodepediaOrg/multi-stage-react-app-example/blob/master/environment/dev.properties)文件:
中为`dev`环境创建一个

```
kubectl create configmap multi-stage-react-app-example-config --from-file=config.js=environment/dev.properties 
```

Enter fullscreen mode Exit fullscreen mode

这将创建一个 configMap，然后您可以通过`config.js`键引用它，内容是环境变量。

您可以通过发出以下 kubectl 命令来检查这一点:

```
kubectl get configmaps multi-stage-react-app-example-config -o yaml 
```

Enter fullscreen mode Exit fullscreen mode

结果应该如下所示:

```
apiVersion: v1
data:
  config.js: |
    window.REACT_APP_API_URL='https://www.bookmarks.dev/api/public/bookmarks'
    window.REACT_APP_ENVIRONMENT='DEV'
    window.REACT_APP_NAVBAR_COLOR='LightGreen'
kind: ConfigMap
metadata:
  creationTimestamp: "2019-08-25T05:20:17Z"
  name: multi-stage-react-app-example-config
  namespace: default
  resourceVersion: "13382"
  selfLink: /api/v1/namespaces/default/configmaps/multi-stage-react-app-example-config
  uid: 06664d35-c6f8-11e9-8287-025000000001Å 
```

Enter fullscreen mode Exit fullscreen mode

#### 在容器中挂载配置图

现在的技巧是通过一个卷将配置图挂载到容器中，并用来自配置图的
值覆盖 config.js 文件。现在将服务和部署资源的配置移动到 [kubernetes](https://github.com/CodepediaOrg/multi-stage-react-app-example/tree/master/kubernetes) 文件夹中的单独文件中。
[部署](https://github.com/CodepediaOrg/multi-stage-react-app-example/blob/master/kubernetes/deployment.yaml)文件:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app.kubernetes.io/component: service
  name: multi-stage-react-app-example
spec:
  replicas: 1
  selector:
    matchLabels:
      app: multi-stage-react-app-example
  template:
    metadata:
      labels:
        app.kubernetes.io/component: service
        app: multi-stage-react-app-example
    spec:
      containers:
        - name: multi-stage-react-app-example
          image: multi-stage-react-app-example:latest
          imagePullPolicy: IfNotPresent
          ports:
            - containerPort: 80
          volumeMounts:
            - name:  multi-stage-react-app-example-config-volume
              mountPath: /usr/share/nginx/html/config.js
              subPath: config.js
              readOnly: true
      volumes:
        - name: multi-stage-react-app-example-config-volume
          configMap:
            name: multi-stage-react-app-example-config 
```

Enter fullscreen mode Exit fullscreen mode

在规范的`volumes`部分，基于您刚刚创建的配置图定义一个卷:

```
 volumes:
        - name: multi-stage-react-app-example-config-volume
          configMap:
            name: multi-stage-react-app-example-config 
```

Enter fullscreen mode Exit fullscreen mode

然后将它挂载到 nginx 交付文件的文件夹的容器中:

```
spec:
  ...
  template:
  ...
    metadata:
      labels:
        app.kubernetes.io/component: service
        app: multi-stage-react-app-example
    spec:
      containers:
        ...
          volumeMounts:
            - name:  multi-stage-react-app-example-config-volume
              mountPath: /usr/share/nginx/html/config.js
              subPath: config.js
              readOnly: true 
```

Enter fullscreen mode Exit fullscreen mode

> 注意:你需要使用`subpath`只覆盖 *config.js* 文件，否则文件夹的内容被这个文件替换

#### 部署在 kubernetes“开发”集群上

我们将使用同一个本地集群来测试我们的**开发**部署。你现在申请`kubectl`上
目录下`kubernetes`的所有文件:

```
kubectl apply -f kubernetes 
```

Enter fullscreen mode Exit fullscreen mode

通过连接到 pod:
验证 _config.js 文件已被替换

```
#first export list the pod holding our application
export MY_POD=`kubectl get pods | grep multi-stage-react-app-example | cut -f1 -d ' '`

# connect to shell in alpine image
kubectl exec -it $MY_POD -- /bin/sh 

# display content of the config.js file
less /usr/share/nginx/html/config.js 
```

Enter fullscreen mode Exit fullscreen mode

它应该包含 **dev** 环境的变量:

```
window.REACT_APP_API_URL='https://www.bookmarks.dev/api/public/bookmarks'
window.REACT_APP_ENVIRONMENT='DEV'
window.REACT_APP_NAVBAR_COLOR='LightGreen' 
```

Enter fullscreen mode Exit fullscreen mode

但是最好通过端口转发应用程序来查看它的运行情况。你现在知道是怎么回事了

```
kubectl port-forward svc/multi-stage-react-app-example 3001:80 
```

Enter fullscreen mode Exit fullscreen mode

导航到 [http://localhost:3001](http://localhost:3001) ，现在您应该在导航栏上看到 **DEV** 环境。

在连续交付管道中，您可以有两个步骤:

1.  基于 *dev.properties* 文件创建配置图
2.  使用上面指定的`kubectl`在目标集群上部署

#### 拆掉

```
kubectl delete -f kubernetes 
```

Enter fullscreen mode Exit fullscreen mode

您可以对其他环境采用相同的方法，比如测试或试运行。

## 用 Kustomize 部署在 Kubernetes 上

如果现在部署到 **prod** 集群时，您希望有两个 pod，而不是一个为 web 应用服务，该怎么办？当然
您可以修改 *deployment.yaml* 文件，指定 2 个副本而不是 1 个，然后进行部署。但是你可以通过使用 Kustomize 以一种优雅的
方式解决这个问题，它也提供了其他的优势。

> [Kustomize](https://github.com/kubernetes-sigs/kustomize) 是一个独立的工具，通过
> 一个[Kustomize 文件](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#kustomization)来定制 Kubernetes 对象。从 1.14 开始，Kubectl 还支持使用 kustomization 文件管理 Kubernetes 对象，所以您不需要额外安装[它。
> 对于本教程，我建议你这样做，因为你以后会在 Skaffold - on MacOS 上用到它`brew install kustomize`](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/INSTALL.md)

使用 Kustomize，您可以在所谓的**基础**(环境中可用的交叉关注点)中定义基础资源，并在**覆盖**中定义特定于不同部署的属性。
这里我们将 kustomize 相关文件放在 [kustomize](https://github.com/CodepediaOrg/multi-stage-react-app-example/blob/master/) 文件夹- `tree kustomize` :

```
kustomize/
├── base
│   ├── deployment.yaml
│   ├── kustomization.yaml
│   └── service.yaml
└── overlays
    ├── dev
    │   ├── dev.properties
    │   └── kustomization.yaml
    ├── local
    │   ├── kustomization.yaml
    │   └── local.properties
    └── prod
        ├── deployment-prod.yaml
        ├── kustomization.yaml
        └── prod.properties 
```

Enter fullscreen mode Exit fullscreen mode

在基本文件夹中，我们定义了**服务**和**部署**，因为在这种情况下，它们总体上是相同的(除了生产的两个副本，但我们将在后面处理)。

### 部署到具有 Kustomize 的**开发**集群

假设我们想要使用 Kustomize 部署到我们的**开发**集群。为此，我们将使用`dev`叠加。
在 dev [草绘文件](https://github.com/CodepediaOrg/multi-stage-react-app-example/blob/master/kustomize/overlays/dev/kustomization.yaml) :

```
bases:
  - ../../base

configMapGenerator:
  - name: multi-stage-react-app-example-config
    files:
      - dev.properties 
```

Enter fullscreen mode Exit fullscreen mode

我们指向之前定义的`bases`，并使用 *dev.properties* 文件[生成配置图](https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization/#generating-resources)。

在我们将`dev`覆盖应用到集群之前，我们可以通过发出以下命令来检查它生成了什么:

```
kubectl kustomize kustomize/overlays/dev 
```

Enter fullscreen mode Exit fullscreen mode

> 请注意，生成的配置图名称有一个后缀(类似于- `multi-stage-react-app-example-config-gdgg4f85bt`)，通过对文件内容进行哈希运算来添加这个后缀
> 。这确保了当内容改变时生成新的配置映射。在*Deployment . YAML*文件中，configMap 仍然被`multi-stage-react-app-example-config`引用，但是在生成的部署对象中，它具有生成的名称。

要应用“开发库定制”，使用以下命令:

```
kubectl apply -k kustomize/overlays/dev # <kustomization directory> 
```

Enter fullscreen mode Exit fullscreen mode

现在端口转发(`kubectl port-forward svc/multi-stage-react-app-example 3001:80`)并转到 [http://localhost:3001](http://localhost:3001)

#### 更新一个环境变量值

例如，如果你想更新一个环境变量的值，比如说， *dev.properties* 文件中的`window.REACT_APP_NAVBAR_COLOR='Blue'`，
你需要做的是应用增益 **dev** 覆盖:

```
kubectl apply -k kustomize/overlays/dev

#result similar to the following
configmap/multi-stage-react-app-example-config-dg44f5bkhh created
service/multi-stage-react-app-example unchanged
deployment.apps/multi-stage-react-app-example configured 
```

Enter fullscreen mode Exit fullscreen mode

请注意，将创建一个新的配置图，并将其应用于部署。重新加载，现在导航栏是蓝色的。

#### 拆掉

```
kubectl delete -k kustomize/overlays/dev 
```

Enter fullscreen mode Exit fullscreen mode

#### 使用 kustomize 部署到生产

如前所述，对于生产，您可能希望有两个副本来提供应用程序以实现高可用性。为此，您可以创建一个 **prod** 覆盖图，该覆盖图派生自公共的 **base** ，类似于 **dev** 覆盖图。

它额外定义了一个 [deployment-prod.yaml](https://github.com/CodepediaOrg/multi-stage-react-app-example/blob/master/kustomize/overlays/prod/deployment-prod.yaml) 文件:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: multi-stage-react-app-example
spec:
  replicas: 2 
```

Enter fullscreen mode Exit fullscreen mode

这是部分部署资源，我们在 [`patchesStrategicMerge`](https://github.com/kubernetes-sigs/kustomize/blob/master/docs/fields.md#patchesstrategicmerge) :
下的 [prod kustomization.yaml](https://github.com/CodepediaOrg/multi-stage-react-app-example/blob/master/kustomize/overlays/prod/kustomization.yaml) 文件
中引用

```
bases:
  - ../../base

patchesStrategicMerge:
  - deployment-prod.yaml

configMapGenerator:
  - name: multi-stage-react-app-example-config
    files:
      - config.js=prod.properties 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过运行:
看到它正在被修改

```
kubectl kustomize kustomize/overlays/prod 
```

Enter fullscreen mode Exit fullscreen mode

然后应用:

```
kubectl apply -k kustomize/overlays/prod 
```

Enter fullscreen mode Exit fullscreen mode

如果您运行`kubectl get pods`，您现在应该会看到两个条目，类似于:

```
NAME                                             READY   STATUS    RESTARTS   AGE
multi-stage-react-app-example-59c5486dc4-2mjvw   1/1     Running   0          112s
multi-stage-react-app-example-59c5486dc4-s88ms   1/1     Running   0          112s 
```

Enter fullscreen mode Exit fullscreen mode

> 现在，您可以进行端口转发，并以您熟悉的方式访问应用程序

##### 拆掉

```
kubectl delete -k kustomize/overlays/prod 
```

Enter fullscreen mode Exit fullscreen mode

## 用[掌舵](https://helm.sh/)部署在库伯内特斯上

什么是头盔？根据文件:

> Helm 是一个简化安装和管理 Kubernetes 应用程序的工具。你可以把它想象成适合 Kubernetes 的 apt/yum/homebrew。

Helm 使用所谓的 Kubernetes 图表。图表是预先配置的 Kubernetes 资源的包。如果你想了解更多关于赫尔姆的信息，请阅读[文档](https://helm.sh/docs/)，我们不会在此赘述，只在需要的地方守时。

目前 Helm 有一个客户端(`helm`)和一个服务器(`tiller`)。Tiller 在您的 Kubernetes 集群中运行，并管理您的图表的发布(安装)
。

### 舵安装

在 MacOS 上，你可以用自制软件安装客户端:

```
brew install kubernetes-helm 
```

Enter fullscreen mode Exit fullscreen mode

对于其他平台，参见[安装 Helm 客户端](https://helm.sh/docs/using_helm/#installing-helm)。

要在本地 Kubernetes 集群上安装 Tiller 进行测试，只需调用以下命令:

```
helm init

#result should something similar to the following:
Creating /Users/ama/.helm 
Creating /Users/ama/.helm/repository 
Creating /Users/ama/.helm/repository/cache 
Creating /Users/ama/.helm/repository/local 
Creating /Users/ama/.helm/plugins 
Creating /Users/ama/.helm/starters 
Creating /Users/ama/.helm/cache/archive 
Creating /Users/ama/.helm/repository/repositories.yaml 
Adding stable repo with URL: https://kubernetes-charts.storage.googleapis.com 
Adding local repo with URL: http://127.0.0.1:8879/charts 
$HELM_HOME has been configured at /Users/ama/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
To prevent this, run `helm init` with the --tiller-tls-verify flag.
For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation 
```

Enter fullscreen mode Exit fullscreen mode

要检查 helm 版本，您可以运行以下命令:

```
$ helm version
Client: &version.Version{SemVer:"v2.14.3", GitCommit:"0e7f3b6637f7af8fcfddb3d2941fcc7cbebb0085", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.14.3", GitCommit:"0e7f3b6637f7af8fcfddb3d2941fcc7cbebb0085", GitTreeState:"clean"} 
```

Enter fullscreen mode Exit fullscreen mode

### 项目中的舵设置

对于本项目，舵配置显示在[舵图表](https://github.com/CodepediaOrg/multi-stage-react-app-example/tree/master/helm-chart)中。

这最初是通过`helm create helm-chart`命令创建的，并根据应用程序的需求进行了调整。

#### 模板

这个难题最重要的部分是模板/目录。Helm 在这里找到了您的
服务、部署和其他 Kubernetes 资源的 YAML 定义。
我们来看看[服务](https://github.com/CodepediaOrg/multi-stage-react-app-example/blob/master/helm-chart/templates/service.yaml)定义:

```
apiVersion: v1
kind: Service
metadata:
  name: {{ include "helm-chart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "helm-chart.name" . }}
    helm.sh/chart: {{ include "helm-chart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app.kubernetes.io/name: {{ include "helm-chart.name" . }}
    app.kubernetes.io/instance: {{ .Release.Name }} 
```

Enter fullscreen mode Exit fullscreen mode

它看起来类似于使用 Kubectl 或 Kustomize 安装时使用的值，只是这些值在部署时被 Helm 替换为来自 Helm 特定对象的值。

#### 值

值提供了一种用您自己的配置覆盖模板默认值的方法。如上所示，它们通过`.Values`对象出现在模板中。

可以在`helm install`和`helm upgrade`操作期间设置值，或者直接传入，或者上传一个 [`values.yaml`](https://github.com/CodepediaOrg/multi-stage-react-app-example/blob/master/helm-chart/values.yaml) 文件。

#### 配置图

这一次我们将把[配置图](https://github.com/CodepediaOrg/multi-stage-react-app-example/blob/master/helm-chart/templates/configMap.yaml)创建为一个 Kubernetes 对象:

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: multi-stage-react-app-example-config
  annotations:
    # https://github.com/helm/helm/blob/master/docs/charts_hooks.md
    "helm.sh/hook-delete-policy": "before-hook-creation"
    "helm.sh/hook": pre-install, pre-upgrade
data:
  config.js: {{ toYaml .Values.configValues | indent 4 }} 
```

Enter fullscreen mode Exit fullscreen mode

> 在安装或升级舵图(`"helm.sh/hook": pre-install, pre-upgrade`)之前，我们使用[舵钩](https://github.com/helm/helm/blob/master/docs/charts_hooks.md)创建配置图

事实是，钩子创建的资源并不作为发布的一部分被跟踪或管理。一旦 Tiller 确认钩子已经到达就绪状态，它将离开钩子资源——因此你不能依靠`helm delete`来移除资源。销毁资源的一种方法是将`"helm.sh/hook": pre-install, pre-upgrade`注释添加到钩子模板文件中。

### 用 helm 部署到本地集群

在使用 helm 部署之前，您可能需要检查图表中可能存在的问题，并执行`helm lint` :

```
helm lint helm-chart 
```

Enter fullscreen mode Exit fullscreen mode

并执行一次试运行，从图表中查看生成的资源

```
helm install -n local-release helm-chart/ --dry-run --debug 
```

Enter fullscreen mode Exit fullscreen mode

结果应该如下所示:

```
# result
[debug] Created tunnel using local port: '64528'

[debug] SERVER: "127.0.0.1:64528"

[debug] Original chart version: ""
[debug] CHART PATH: /Users/ama/projects/multi-stage-react-app-example/helm-chart

NAME:   local-release
REVISION: 1
RELEASED: Fri Aug 30 06:30:55 2019
CHART: helm-chart-0.1.0
USER-SUPPLIED VALUES:
{}

COMPUTED VALUES:
affinity: {}
configValues: |
  window.REACT_APP_API_URL='https://www.bookmarks.dev/api/public/bookmarks'
  window.REACT_APP_ENVIRONMENT='LOCAL with helm'
  window.REACT_APP_NAVBAR_COLOR='LightBlue'
fullnameOverride: ""
image:
  imagePullSecrets: cfcr
  pullPolicy: IfNotPresent
  repository: multi-stage-react-app-example
  tag: latest
ingress:
  annotations: {}
  enabled: false hosts:
  - chart-example.local
  paths: []
  tls: []
nameOverride: ""
nodeSelector: {}
replicaCount: 1
resources: {}
service:
  port: 80
  type: NodePort
tolerations: []

HOOKS:
--------
# local-release-helm-chart-test-connection
apiVersion: v1
kind: Pod
metadata:
  name: "local-release-helm-chart-test-connection"
  labels:
    app.kubernetes.io/name: helm-chart
    helm.sh/chart: helm-chart-0.1.0
    app.kubernetes.io/instance: local-release
    app.kubernetes.io/managed-by: Tiller
  annotations:
    "helm.sh/hook": test-success
spec:
  containers:
    - name: wget
      image: busybox
      command: ['wget']
      args:  ['local-release-helm-chart:80']
  restartPolicy: Never
--------
# local-release-multi-stage-react-app-example-config
apiVersion: v1
kind: ConfigMap
metadata:
  name: local-release-multi-stage-react-app-example-config
  annotations:
    # https://github.com/helm/helm/blob/master/docs/charts_hooks.md
    "helm.sh/hook-delete-policy": "before-hook-creation"
    "helm.sh/hook": pre-install, pre-upgrade
data:
  config.js:     |
      window.REACT_APP_API_URL='https://www.bookmarks.dev/api/public/bookmarks'
      window.REACT_APP_ENVIRONMENT='LOCAL with helm'
      window.REACT_APP_NAVBAR_COLOR='LightBlue'
MANIFEST:

--------
# Source: helm-chart/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: local-release-helm-chart
  labels:
    app.kubernetes.io/name: helm-chart
    helm.sh/chart: helm-chart-0.1.0
    app.kubernetes.io/instance: local-release
    app.kubernetes.io/managed-by: Tiller
spec:
  type: NodePort
  ports:
    - port: 80
      targetPort: http
      protocol: TCP
      name: http
  selector:
    app.kubernetes.io/name: helm-chart
    app.kubernetes.io/instance: local-release
--------
# Source: helm-chart/templates/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: local-release-helm-chart
  labels:
    app.kubernetes.io/name: helm-chart
    helm.sh/chart: helm-chart-0.1.0
    app.kubernetes.io/instance: local-release
    app.kubernetes.io/managed-by: Tiller
spec:
  replicas: 1
  selector:
    matchLabels:
      app.kubernetes.io/name: helm-chart
      app.kubernetes.io/instance: local-release
  template:
    metadata:
      labels:
        app.kubernetes.io/name: helm-chart
        app.kubernetes.io/instance: local-release
    spec:
      imagePullSecrets:
        - name: cfcr
      containers:
        - name: helm-chart
          image: "multi-stage-react-app-example:latest"
          imagePullPolicy: IfNotPresent
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          volumeMounts:
            - name:  multi-stage-react-app-example-config-volume
              mountPath: /usr/share/nginx/html/config.js
              subPath: config.js
              readOnly: true resources:
            {}

      volumes:
        - name: multi-stage-react-app-example-config-volume
          configMap:
            name: local-release-multi-stage-react-app-example-config 
```

Enter fullscreen mode Exit fullscreen mode

> 注意为服务和部署生成的名称`local-release-helm-chart`(从`{{ include "helm-chart.fullname" . }})`和
> 和`local-release-multi-stage-react-app-example-config`生成)(从`{{ .Release.Name }}-multi-stage-react-app-example-config`生成)

现在运行没有`--dry-run`标志的安装，用于实际安装:

```
helm install -n local-release helm-chart/ 
```

Enter fullscreen mode Exit fullscreen mode

通过列出舵释放(`helm ls` ):
确认舵释放存在

```
helm ls NAME            REVISION        UPDATED                         STATUS          CHART                   APP VERSION     NAMESPACE
local-release   1               Fri Aug 30 06:46:09 2019        DEPLOYED        helm-chart-0.1.0        1.0             default 
```

Enter fullscreen mode Exit fullscreen mode

现在端口转发服务(你知道如何从上面的`local-release-helm-chart` )
中调用服务)

```
kubectl port-forward svc/local-release-helm-chart 3001:80 
```

Enter fullscreen mode Exit fullscreen mode

并在 [http://localhost:3001](http://localhost:3001) 访问应用程序，将环境设置为“本地带舵”

### 拆掉舵释放

```
helm delete --purge local-release 
```

Enter fullscreen mode Exit fullscreen mode

### 使用“开发”值部署

现在，假设您想要部署到“开发”群集。为此，您可以在一个 [config-dev.yaml](https://github.com/CodepediaOrg/multi-stage-react-app-example/blob/master/helm-chart/config-values/config-dev.yaml) 文件中配置环境值:

```
configValues: |
  window.REACT_APP_API_URL='https://www.bookmarks.dev/api/public/bookmarks'
  window.REACT_APP_ENVIRONMENT='DEV'
  window.REACT_APP_NAVBAR_COLOR='LightGreen' 
```

Enter fullscreen mode Exit fullscreen mode

这将在部署时用来覆盖来自 *values.yaml* 文件的`configValues`。这次使用
up sert 变体，这意味着如果发布不存在，它将被创建:

```
helm upgrade dev-release ./helm-chart/ --install --force --values helm-chart/config-values/config-dev.yaml 
```

Enter fullscreen mode Exit fullscreen mode

现在端口转发`kubectl port-forward svc/dev-release-helm-chart 3001:80`并在[访问应用程序 http://localhost:3001](http://localhost:3001) et
瞧，你已经部署了开发环境。

### 拆掉`dev-release`

```
helm delete --purge dev-release 
```

Enter fullscreen mode Exit fullscreen mode

## 斯卡福德

> 这篇文章最初发表在[Kubernetes 上的一个更干净的多阶段连续部署，使用 kustomize、helm 和 skaffold](https://www.codepedia.org/ama/a-cleaner-multi-stage-deployment-on-kubernetes-of-a-create-react-app-with-kustomize-helm-and-skaffold) 创建一个 React 应用程序，看看 skaffold 如何改进您在 Kubernetes 上的本地开发和部署。

如果你能看一看最初的 [www.bookmarks.dev](https://www.bookmarks.dev) 应用程序并试一试(你可能会喜欢它)，并在 https://github.com/CodepediaOrg/bookmarks[启动生成的公共书签，我将不胜感激。](https://github.com/CodepediaOrg/bookmarks)