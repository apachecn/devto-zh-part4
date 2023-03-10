# Istio，SDS y 认证经理

> 原文：<https://dev.to/samcre/istio-sds-y-cert-manager-4a9e>

如果你到了这里，你就知道库柏人是什么。你甚至可能用迷你库布在本地建立了自己的群集。甚至是数百个公共和私人云团中的一个，它们提供了一种自我管理的库伯服务。按选项！

在这一系列帖子中，我们将探讨如何安装/更新 Instituto，使其与[cert manager](https://github.com/jetstack/cert-manager)配合使用，后者将通过 [Let's Encrypt](https://letsencrypt.org/) 和 [ExternalDNS](https://github.com/kubernetes-incubator/external-dns) 向我们提供免费证书，从而更新我们的 DNS

## 是 SDS 吗？mTLS？

SDS 是*秘密发现服务*的英文缩写。Instituto 1.1 之前，Instituto 生成的私有密钥和证书由*引自*处理，并作为卷安装在 pods 的 sidecar pods 上。这有一些缺点:

*   当您续订证书时，istio 会热重新启动装运机，从而导致性能下降。
*   私密金钥储存在库柏人的秘密中，这有‘t0’的风险。

简而言之，SDS 解决这些问题的方法是在引文中生成私钥和 CSR(其作用类似于*daemoiset*，即在所有库伯节点上)，并使用货运车发送的 JWT 令牌。*引自*检查令牌，如果一切正确，生成证书和私钥并发送到 sidecar。

你可以在本能博客和[发送文档](https://www.envoyproxy.io/docs/envoy/latest/configuration/secret#config-secret-discovery-service)中找到它如何工作的更多细节。

另一方面，mTLS (mutual TLS)将群集中的所有 pods 配置为使用 TLS 相互通信，因此所有微服务之间的通信都将加密，而 istio 组件将负责管理证书。

## 用 mTLS 和 SDS 安装本能

在本文中，我们从一个空群集开始。如果我们已经有一个群集在没有本能的情况下运行，我们必须考虑到，为了使 Instituto 将 pod sidecar 注入一个命名空间，我们必须将该命名空间*标记为*:** 

```
kubectl label namespace production istio-injection=enabled 
```

一旦应用，我们将不得不提高新的 pods，以使本能注入钢盔，并消除旧的。

* * *

为了安装本能，我们将使用*头盔*。下载最新版本的[本能](https://github.com/istio/istio/releases/tag/1.1.7)，并将其解压缩。

我们先在集群中安装了本能 CRDs。如果您已经安装了此步骤，则可以跳过此步骤:

```
helm install --name istio-init --namespace istio-system istio-1.1.7/install/kubernetes/helm/istio-init 
```

我们将创建一个类似于`values-istio-sds-auth.yaml`的*值文件，该文件将在 tar 中出现，但有一些修改，我将在文件中对其进行说明:* 

```
$ cat values-istio-sds-auth.yaml
global:
  controlPlaneSecurityEnabled: false

  mtls:
    enabled: true

  sds:
    enabled: true
    udsPath: "unix:/var/run/sds/uds_path"
    useNormalJwt: true

nodeagent:
  enabled: true
  image: node-agent-k8s
  env:
    CA_PROVIDER: "Citadel"
    CA_ADDR: "istio-citadel:8060"
    VALID_TOKEN: true
# Fin del fichero original

# Valores añadidos manualmente
# En la sección anterior, el parámetro global.sds.enabled=true activa SDS únicamente en los pods sidecar de Istio.
# En esta sección, configuramos el ingressgateway para que reciba los certificados con el mismo método.
gateways:
  istio-ingressgateway:
    enabled: true
    sds:
      enabled: true

# Esta sección inferior soluciona los livenessProbe y readinessProbe de Kubernetes configurados mediante httpGet,
# que fallan por el sidecar de Istio.
sidecarInjectorWebhook:
  enabled: true
  rewriteAppHTTPProbe: true 
```

我们以命令安装:

```
helm install \
  --name istio \
  --namespace istio-system \
  istio-1.1.7/install/kubernetes/helm/istio \
  --values values.yaml # values.yaml es nuestro fichero modificado 
```

紧张几分钟后，你应该有本能在你的集群中运作:

```
$ kubectl get pods -n istio-system
NAME                                       READY   STATUS    RESTARTS   AGE
istio-citadel-7f447d4d4b-f8lzt             1/1     Running   0          1m
istio-galley-84749d54b7-7sngg              1/1     Running   0          1m
istio-ingressgateway-57c97bb7f6-8294g      2/2     Running   0          1m
istio-nodeagent-nrl7v                      1/1     Running   0          1m
istio-nodeagent-vkchb                      1/1     Running   0          1m
istio-pilot-76899788b6-xcw7t               2/2     Running   0          1m
istio-policy-578bcb878f-ddwh7              2/2     Running   2          1m
istio-sidecar-injector-6895997989-5qsnn    1/1     Running   0          1m
istio-telemetry-5448cbd995-jcpdg           2/2     Running   2          1m 
```

∞天啊！你已经有了本能。喝点咖啡吧，你赢了。

* * *

## instalado 证书管理器

由于 cert-manager 和最新版本的 Helm 出现错误，我们将通过对 cert-manager 的 *chart* 进行建模来安装第一个。

# [![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) 用  #1702](https://github.com/jetstack/cert-manager/issues/1702) 安装时验证 WebhookConfiguration 时缺少“caBundle”

[![andrei-dascalu avatar](img/1564593e4fd0ce6c46055202c2e33fa0.png)](https://github.com/andrei-dascalu) **[andrei-dascalu](https://github.com/andrei-dascalu)** posted on [<time datetime="2019-05-19T23:23:36Z">May 19, 2019</time>](https://github.com/jetstack/cert-manager/issues/1702)

**描述 bug** :根据文档`helm install --name cert-manager --namespace cert-manager --version v0.7.2 jetstack/cert-manager`使用 helm 安装时(在集群(AKS)和 helm `v2.14.0`中运行`v1.12.7`的全新集群上按照文档逐步操作)

结果为:错误:验证失败:验证“”时出错:验证数据时出错:[validation Error(validatingwebhookconfiguration . web hooks[0]。client config):io . k8s . API . admission registration . v1beta 1 . webhookclientconfig，validation error(validatingwebhookconfiguration . web hooks[1]中缺少必填字段“caBundle”。client config):io . k8s . API . admission registration . v1beta 1 . webhookclientconfig，validation error(validatingwebhookconfiguration . web hooks[2]中缺少必填字段“caBundle”。client config):io . k8s . API . admission registration . v1 beta 1 . webhookclientconfig]中缺少必填字段“caBundle”

**预期行为**:安装了证书管理器

**重现 bug 的步骤**:按照[的步骤 https://docs . cert-manager . io/en/latest/getting-started/install . html # installing-with helm](https://docs.cert-manager.io/en/latest/getting-started/install.html#installing-with-helm)

还有什么我们需要知道的吗？:

**环境详情:**:

*   kuble 版本(例如 v1.12.7):
*   云提供商/供应商(例如 GKE、kops AWS 等):AKS
*   证书管理器版本(例如 v0.4.0): 0.7.2
*   安装方法(例如 helm 或静态清单):helm

/亲切的虫子

[View on GitHub](https://github.com/jetstack/cert-manager/issues/1702)

下载 cert manager 图表并解压缩:

```
helm fetch --untar stable/cert-manager 
```

因为我们不需要修改`values.yaml`的任何参数就能在群集中安装，所以，我们对其进行了建模并应用:

```
helm template . --name cert-manager --namespace istio-system > cert-manager.yaml
kubectl apply --file cert-manager.yaml 
```

**重要**:如你所见，我们在 Istio 命名空间中安装了 cert manager， *istio-system* 。这是一个要求，因为 SDS 要运行，包含证书的秘密必须位于您所在的相同的*命名空间*。istio 网关和 VirtualServices 可能位于其相应的*命名空间*。

**注**:在*图表*本身的本能中，我们可以指明安装证书管理器的选项。在本文中，我们单独安装了它，以便能够独立于本能设施进行处理。

一旦在 Istio 命名空间中安装了 cert-manager，我们将需要配置证书颁发者。我们将配置为使用 let’s encrypt，并通过解决一个‘t1’DNS 01 challenge’T2’来确认证书，这将使我们能够生成类型为‘T3’wild card〖T4〗的证书，即〖t0〗的证书。

在这种情况下，我们使用 **AWS Route 53** 作为 DNS，因此群集或 pos 将需要[iam](https://docs.cert-manager.io/en/latest/tasks/issuers/setup-acme/dns01/route53.html)的特定许可才能使用这项 AWS 服务。

生成包含以下内容的文件:`clusterissuer.yaml`:t1]

```
# clusterissuer.yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt
  labels:
    app: certmanager
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: <EMAIL> # Sustituir por un correo válido
    privateKeySecretRef:
      name: letsencrypt # Secreto donde cert-manager guardará datos
    # Configuramos el proveedor para Route 53\. En este caso no se indican credenciales de AWS,
    # dado que los pods y/o nodos de k8s tienen asignado un rol de IAM con permisos.
    dns01:
      providers:
      - name: route53
        route53:
          region: eu-west-1
#          # Descomenta esto, y modifica acorde,
#          # para usar unas credenciales de IAM que otorguen los permisos
#          accessKeyID: AKIAIOSFODNN7EXAMPLE
#          secretAccessKeySecretRef:
#            name: prod-route53-credentials-secret
#            key: secret-access-key 
```

并应用:

```
kubectl apply -f clusterissuer.yaml 
```

我们可以在群集级别使用 Let's Encrypt 证书创建证书。但首先我们要定义一个证书，让 cert manager 自动提示:

```
# wildcard.example.com.yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: wildcard.example.com
  namespace: istio-system # Recordad: es necesario que los certificados estén en el namespace de Istio
spec:
  secretName: wildcard.example.com
  issuerRef:
    kind: ClusterIssuer
    name: letsencrypt
  commonName: "*.example.com"
  dnsNames:
  - example.com
  acme:
    config:
    - dns01:
        provider: route53
      domains:
        - '*.example.com'
        - example.com 
```

```
kubectl apply -f wildcard.example.com.yaml 
```

如果一切配置正确，我们将在几分钟内提供证书:

```
$ kubectl describe certificates wildcard.example.com
[...]
Status:
  Conditions:
    Last Transition Time:  2019-05-30T09:27:56Z
    Message:               Certificate is up to date and has not expired
    Reason:                Ready
    Status:                True
    Type:                  Ready
  Not After:               2019-08-28T08:27:54Z
[...] 
```

∞天啊！我们拿到证书了。但是，我们是否让 istio 将流量发送到我们的 pods，并负责设置证书？为此，我们利用本能的网关和虚拟服务。

## 配置网关和虚拟服务

这很简单。我们只需生成一个本能网关，在其中配置 DNS 和证书，然后生成一个 VirtualService，将基于主机的流量发送到相应的 kubricks 服务。对于此示例，我们将使用服务`nginx` :

```
# route.yaml
apiVersion: networking.istio.io/v1alpha3
kind: Gateway
metadata:
  name: example-gateway
  namespace: production # Podemos crear los Gateways en el namespace que queramos
spec:
  selector:
    istio: ingressgateway # Gateway de Istio por donde entra el tráfico al service mesh
  servers:
  # Configuramos el puerto 80 para que Istio haga la redirección a HTTPS
  - port:
      number: 80
      name: http-redirect
      protocol: HTTP
    hosts:
    - "nginx.example.com"
    tls:
      httpsRedirect: true
  - port:
      number: 443
      name: nginx-https
      protocol: HTTPS
    tls:
      # En credentialName, indicamos el nombre del secreto configurado
      # en el Certificate anteriormente creado
      credentialName: wildcard.example.com
      mode: SIMPLE
      # En las dos siguientes claves, indicamos a Istio que use el sistema SDS
      # para obtener el certificado
      privateKey: sds
      serverCertificate: sds
    hosts:
      - "nginx.example.com"
---
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: nginx-route
  namespace: production
spec:
  gateways:
    - example-gateway # OJO, hace referencia al gateway creado más arriba
  hosts:
    - nginx.example.com
  http:
    - match:
      - uri:
          prefix: "/"
      route:
      - destination:
          host: nginx.production.svc.cluster.local
          port:
            number: 443 
```

我们用`kubectl apply -f routes.yaml`应用这个文件，就这样！istio 将在我们进入服务 DNS 时自动注入配置的证书(在本例中为野卡)`nginx.example.com`。

我们不是要配置 ExternalDNS 自动生成 53 号路由的条目吗？现在，手动操作，在下一个帖子中，我们将看到如何将 ExternalDNS 与 institution 和 Route 53 集成。

我保证会很短。

* * *

### 来源及更多信息

*   [Istio 1.1.7 +我们来加密:正在工作！](https://medium.com/@prune998/istio-1-1-7-lets-encrypt-working-9100cea9f503)
*   [证书管理器文档](https://docs.cert-manager.io/en/latest/)
*   [Istio Docs](https://istio.io/docs/)**