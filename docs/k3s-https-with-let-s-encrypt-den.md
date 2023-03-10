# k3s HTTPS 与让我们加密

> 原文：<https://dev.to/pascalw/k3s-https-with-let-s-encrypt-den>

[K3s](https://k3s.io/) 是经过认证的 Kubernetes 发行版，专为无人值守、资源有限、远程位置或物联网设备内部的生产工作负载而设计。
它提供了一个现成的 Kubernetes 实例，打包成一个二进制文件。

本指南将向您展示如何通过“让我们加密”来轻松设置 k3s 与 HTTPS。

(阅读更多)

K3s 自带 [Traefik](https://traefik.io/) 开箱。Traefik 本身支持通过“让我们加密”实现自动 HTTPS，但是用 k3s 设置这个相当麻烦。相反，使用优秀的[证书管理器](https://github.com/jetstack/cert-manager)插件，这是一件轻而易举的事！

## 0:设置 k3s。

本指南假设您有一个工作的 k3s 实例，并且`kubectl`被配置为与您的 k3s 实例对话。如果您还没有，只需跟随[文档](https://github.com/rancher/k3s/blob/master/README.md)，您将在几分钟内启动并运行。

## 1。安装舵

Helm 是 Kubernetes 的一名包装经理。它由一个本地客户端和一个服务器组件组成。
安装舵非常简单。

安装客户端:

```
$ brew install kubernetes-helm 
```

创建服务帐户，以便 Helm 可以与您的 k3s 实例交互:

```
$ kubectl create serviceaccount tiller --namespace=kube-system 
```

授予 it 管理员权限:

```
$ kubectl create clusterrolebinding tiller-admin --serviceaccount=kube-system:tiller --clusterrole=cluster-admin 
```

注意:根据集群的设置，授予 Helm 管理员权限可能不是一个好主意。阅读[此处](https://helm.sh/docs/using_helm/#role-based-access-control)了解更多详情。

在 k3s 上安装舵:

```
$ helm init --service-account=tiller 
```

## 2。安装证书管理器

好了，现在我们已经安装了头盔，让我们继续安装`cert-manager`。
`cert-manager`是一个 Kubernetes 插件，用于自动管理和发布来自各种发布源的 TLS 证书，其中包括加密。

从安装`cert-manager` s CRDs:
开始

```
$ kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml 
```

添加`cert-manager`舵回购:

```
$ helm repo add jetstack https://charts.jetstack.io && helm repo update 
```

安装`cert-manager` :

```
$ helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.8.1 \
  jetstack/cert-manager 
```

这可能需要一分钟左右的时间。通过检查所有`cert-manager`吊舱正在运行来验证安装:

```
$ kubectl get all -n cert-manager 
```

## 3。配置证书管理器

安装了`cert-manager`之后，我们需要通过创建一个*证书颁发者* :
来配置它使用 Let's Encrypt

```
$ cat <<EOF > letsencrypt-prod-issuer.yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Issuer
metadata:
 name: letsencrypt-prod
spec:
 acme:
   # The ACME server URL
   server: https://acme-v02.api.letsencrypt.org/directory
   # Email address used for ACME registration, update to your own.
   email: user@example.com
   # Name of a secret used to store the ACME account private key
   privateKeySecretRef:
     name: letsencrypt-prod
   # Enable the HTTP-01 challenge provider
   http01: {} EOF 
```

并应用:

```
$ kubectl apply -f letsencrypt-prod-issuer.yaml 
```

到目前为止，`cert-manager`已经做好了准备，让我们根据需要加密证书。

## 4。使用自动 HTTPS 部署服务

为了验证这一点，让我们部署一个简单的服务。您应该有一个 DNS 名称指向您的 k3s 群集，这样才能工作。下面的例子使用了`bootcamp.k3s.example.org`，请确保将其更新为您自己的主机名！

创建清单:

```
$ cat <<EOF > k8s-bootcamp.yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: k8s-bootcamp
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: k8s-bootcamp
  template:
    metadata:
      labels:
        app: k8s-bootcamp
    spec:
      containers:
      - name: k8s-bootcamp
        image: gcr.io/google-samples/kubernetes-bootcamp:v1
---
apiVersion: v1
kind: Service
metadata:
  name: k8s-bootcamp
  namespace: default
spec:
  ports:
  - name: http
    targetPort: 8080
    port: 80
  selector:
    app: k8s-bootcamp
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: k8s-bootcamp
  annotations:
    kubernetes.io/ingress.class: "traefik"
    certmanager.k8s.io/issuer: "letsencrypt-prod"
    certmanager.k8s.io/acme-challenge-type: http01

spec:
  tls:
  - hosts:
    # Change this to your own hostname
    - bootcamp.k3s.example.org
    secretName: bootcamp-k3s-example-org-tls
  rules:
  # Change this to your own hostname
  - host: bootcamp.k3s.example.org
    http:
      paths:
      - path: /
        backend:
          serviceName: k8s-bootcamp
          servicePort: http
EOF 
```

并应用:

```
$ kubectl apply -f k8s-bootcamp.yaml 
```

等一分钟左右，您的端点应该可以使用 Let's Encrypt 证书了！🎉