# 使用 Docker、Kubernetes 和 Minikube 运行 Node.js 应用程序

> 原文：<https://dev.to/molamk/running-a-node-js-app-with-docker-kubernetes-and-minikube-10d4>

## 设置好你的工具

```
# Virtualbox (for virtualization)
brew cask install virtualbox

# HyperKit
brew install hyperkit

# Docker
brew cask install docker

# Kubernetes CLI & kubectl
brew install kubernetes-cli

# Minikube => Local Kubernetes
brew cask install minikube

# Helm => Chart management (optional)
brew install kubernetes-helm 
```

## 编写一个(最小的)Node.js app

在本教程中，我们不会深入讨论如何编写一个好的 Node.js 应用程序的细节。我们的应用程序将有一个最小的服务器，只有一个路线和一个方法，即`GET /`。当然，我们可以添加任意多的特性，但是出于本教程的目的，我们将更多地关注 Docker、Kubernetes 和 Minikube 的基础设施。这是我们的应用程序的样子:

```
const express = require('express');

// Constants
const PORT = process.env.PORT || 3000;
const HOST = '0.0.0.0'

// App
const app = express();
app.get('/', (req, res) => {
    res.send('Hello world\n');
});

app.listen(PORT, HOST);
console.log(`Running on http://${HOST}:${PORT}`); 
```

我们只需要一个`npm`包，就是`express`。要安装它，运行:

```
npm install --save express 
```

## 将应用程序归档

我们可以通过编写一个`Dockerfile`来对我们的应用程序进行 Docker 化，这是 Docker 将运行来捆绑它的一组步骤。看起来是这样的:

```
FROM node:10

# Create app directory
WORKDIR /usr/src/app

# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./

RUN npm install
# If you are building your code for production
# RUN npm ci --only=production

# Bundle app source
COPY . .

EXPOSE 80
CMD [ "node", "index.js" ] 
```

我们还会忽略一些文件，比如本地安装的`node_modules`。为此，我们创建了一个`.dockerignore`文件:

```
node_modules
Dockerfile
.dockerignore
npm-debug.log 
```

现在我们已经设置好了，我们需要实际构建 Docker 映像，然后运行容器。因为我总是忘记这样做的确切命令，所以我更喜欢把它们放在`Makefile`中。下面是它可能的样子:

```
image-name="molamk/node-app"

build:
    docker build -t $(image-name) .

run:
    docker run -p 3000:80 -d $(image-name) 
```

现在我们将构建图像，然后运行我们的容器。当我们用`curl`调用它时，它应该给我们一个带有`200`状态的`"Hello World"`响应。

```
# Build the image
make build

# Run the container
make run

# Call our API
curl -v localhost:3000

# HTTP/1.1 200 OK
# X-Powered-By: Express
# Content-Type: text/html; charset=utf-8
# Content-Length: 12
# ETag: W/"c-M6tWOb/Y57lesdjQuHeB1P/qTV0"
# Date: Sat, 24 Aug 2019 21:00:43 GMT
# Connection: keep-alive

# Hello world 
```

酷！现在我们的应用程序已经被 dockerized，我们可以标记并推送图片到 [Dockerhub](https://hub.docker.com) 。我们将在我们的`makefile`中添加一些东西来完成这个任务:

```
tag:
    docker tag molamk/node-app molamk/node-app:latest

push:
    docker push molamk/node-app 
```

## 本地库伯人带头盔&迷你库伯

我们将使用 Helm 将我们的应用程序打包成一个包，准备部署在 Kubernetes 上。以下是关于 Helm 的更多信息:

> “Helm 帮助您管理 Kubernetes 应用程序——Helm Charts 帮助您定义、安装和升级最复杂的 Kubernetes 应用程序。图表很容易创建、版本化、共享和发布——所以开始使用 Helm，停止复制粘贴吧。”- [掌舵团队](https://helm.sh)

首先，我们需要初始化 Helm，下面是我们如何做的

```
# Fire up Minikube
minikube start
minikube addons enable ingress

# Initialization
helm init

# Update the repositories to their latest versions
helm repo update 
```

之后，我们创建一个图表，其中包含 Kubernetes 部署和服务的清单文件。

```
helm create node-app 
```

现在让我们进入图表生成文件夹`node-app`并编辑一些`yaml`。我们将存储库设置为我们自己的 Docker 映像，这是我们之前推送的。

```
# Chart.yaml

apiVersion: v1
appVersion: "1.0"
description: Running a Node.js app with Docker, Kubernetes, and Minikube
name: node-app
version: 0.1.0 
```

```
# values.yaml

replicaCount: 1

image:
  repository: molamk/node-app
  tag: latest
  pullPolicy: Always

env:
  containerPort: "80"

imagePullSecrets: []
nameOverride: ""
fullnameOverride: ""

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: true
  annotations: {}
  hosts:
    - host: minikube-node-app.local
      paths: ["/"]

  tls: []

resources: {}

nodeSelector: {}

tolerations: []

affinity: {} 
```

我们还将稍微修改一下`deployment.yaml`文件来推断我们的自定义端口。

```
# deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "node-app.fullname" . }}
  labels:
{{ include "node-app.labels" . | indent 4 }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "node-app.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "node-app.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
    {{- with .Values.imagePullSecrets }}
      imagePullSecrets:
        {{- toYaml . | nindent 8 }}
    {{- end }}
      containers:
        - name: {{ .Chart.Name }}
          image: "{{  .Values.image.repository  }}:{{  .Values.image.tag  }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: "PORT"
              value: "{{  .Values.env.containerPort  }}"
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
          resources:
            {{- toYaml .Values.resources | nindent 12 }}
      {{- with .Values.nodeSelector }}
      nodeSelector:
        {{- toYaml . | nindent 8 }}
      {{- end }}
    {{- with .Values.affinity }}
      affinity:
        {{- toYaml . | nindent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
        {{- toYaml . | nindent 8 }}
    {{- end }} 
```

现在让我们在 Kubernetes 上部署它。我们将使用 Helm CLI 来执行此操作，然后我们将验证所有设置是否正确。

```
# Deploy
helm install node-app

# Verify that it's been set-up
helm ls

# NAME                    REVISION        UPDATED                         STATUS          CHART           APP VERSION     NAMESPACE
# wondering-cricket       1               Sun Aug 25 18:24:51 2019        DEPLOYED        node-app-0.1.0  1.0             default 
```

## 测试整个调教

现在让我们告诉我们的`/etc/hosts`文件关于我们的定制主机`minikube-node-app.local`，这样我们就可以调用我们的端点。我们将用`curl`调用它，它应该返回一个 *Hello world* 响应，带有一个 *200* 状态代码。

```
# Putting our custom host into the host file
echo "$(minikube ip) minikube-node-app.local" | sudo tee -a /etc/hosts

# Calling our endpoint
curl -i minikube-node-app.local

# HTTP/1.1 200 OK
# X-Powered-By: Express
# Content-Type: text/html; charset=utf-8
# Content-Length: 12
# ETag: W/"c-M6tWOb/Y57lesdjQuHeB1P/qTV0"
# Date: Sat, 24 Aug 2019 21:00:43 GMT
# Connection: keep-alive

# Hello world 
```

## 抓取代码

[molamk/minikube-node-app](https://github.com/molamk/minikube-node-app)