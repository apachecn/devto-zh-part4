# 用 Traefik 运行个人云，让我们加密和 Zookeeper

> 原文：<https://dev.to/rhanarion/run-a-personal-cloud-with-traefik-let-s-encrypt-and-zookeeper-4a7g>

# Kubernetes 用 Traefik 入口

正如我在[上一篇博文](https://rhazn.com/posts/run-a-personal-cloud-with-traefik-lets-encrypt-and-zookeeper/)中提到的，我希望专注于我自己的云的提供商中立设置，尽可能使用不绑定到任何云产品的技术。

虽然 google cloud 默认提供负载平衡的 HTTP ingress，但与运行小节点相比，它显然非常昂贵，我只听说过使用 Traefik 作为 kubernetes ingress 的好处。

为了设置 Traefik，我遵循了 Manuel 的优秀指南，做了一些小的修改(你可以在文章末尾找到最终文件。)

# HTTPs 和咱们加密

Traefik 内置了自动获取和更新 HTTPS 证书的支持，使用[让我们加密](https://letsencrypt.org/)。由于 HTTPS 是很好的实践，也是 HTTP2 和 PWAs 的需求，我使用 Traefik 文档中的[示例配置](https://docs.traefik.io/user-guide/examples/#onhostrule-option-with-http-challenge)来设置它。

因为我只为 Traefik 使用了一个节点，所以我选择简单地设置一个本地 acme.json 文件，该文件在节点运行时存储证书。

# GKE 抢占节点，自己的混沌猴子

为了节省成本，我选择使用“可预先部署的虚拟机”作为节点，为我在 GKE 的 kubernetes 集群供电。根据谷歌的文件:“可抢占的虚拟机是谷歌计算引擎虚拟机实例，最多持续 24 小时，不提供可用性保证。”这意味着我的 kubernetes 集群中的节点会随机停机，并且不会超过 24 小时。虽然这对于产品设置来说显然不是一个明智的决定，但我还是选择了接受它，并考虑了我自己的[“混沌猴”](https://netflix.github.io/chaosmonkey/)的节点，这迫使我编写有弹性的代码。

我遇到的一个具体例子是:Let's encrypt production API 的速率限制是一周内为同一个 URL 请求 5 个证书。因为我最初的简单设置没有保存证书，每当我的 Traefik 节点终止时，证书就会丢失。而 Traefik 在启动时会重新生成证书，不会出现任何问题...在五次创业后，我达到了我的速度极限，迎接我的是一个没有证书的不安全警告。

# 与 Zookeeper 共享 Traefik 的 K/V 库

输入 Traefik 的共享密钥/值存储。如果您无论如何都想在集群模式下运行 Traefik，那么使用一个是必需的(我认为我的设置很容易扩展)。这也意味着我可以将生成的证书存储在 K/V 存储中，当 Traefik 重新启动时，它将不再消失。

因为我以前有过使用 Zookeeper 的经验，而且设置相对来说没有痛苦，所以我使用了它。

# 用于设置的所有 Kubernetes yaml 文件

最后，我的完整设置作为 yaml 文件，您可以直接部署到您的 GKE 集群:

## 先设置动物园管理员

来自这个优秀的资源:[https://github . com/kow 3n s/kubernetes-zookeeper/blob/master/manifests/readme . MD](https://github.com/kow3ns/kubernetes-zookeeper/blob/master/manifests/README.md)

```
apiVersion: v1
kind: Service
metadata:
  name: zk-hs
  labels:
    app: zk
spec:
  ports:
  - port: 2888
    name: server
  - port: 3888
    name: leader-election
  clusterIP: None
  selector:
    app: zk
---
apiVersion: v1
kind: Service
metadata:
  name: zk-cs
  labels:
    app: zk
spec:
  ports:
  - port: 2181
    name: client
  selector:
    app: zk
---
apiVersion: apps/v1beta1
kind: StatefulSet
metadata:
  name: zk
spec:
  serviceName: zk-hs
  replicas: 1
  podManagementPolicy: Parallel
  updateStrategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: zk
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - zk
              topologyKey: "kubernetes.io/hostname"
      containers:
      - name: kubernetes-zookeeper
        imagePullPolicy: Always
        image: "gcr.io/google_containers/kubernetes-zookeeper:1.0-3.4.10"
        resources:
          requests:
            memory: "200M"
            cpu: "0.3"
        ports:
        - containerPort: 2181
          name: client
        - containerPort: 2888
          name: server
        - containerPort: 3888
          name: leader-election
        command:
        - sh
        - -c
        - "start-zookeeper  \
          --servers=1  \
          --data_dir=/var/lib/zookeeper/data  \
          --data_log_dir=/var/lib/zookeeper/data/log  \
          --conf_dir=/opt/zookeeper/conf  \
          --client_port=2181  \
          --election_port=3888  \
          --server_port=2888  \
          --tick_time=2000  \
          --init_limit=10  \
          --sync_limit=5  \
          --heap=512M  \
          --max_client_cnxns=60  \
          --snap_retain_count=3  \
          --purge_interval=12  \
          --max_session_timeout=40000  \
          --min_session_timeout=4000  \
          --log_level=INFO"
        readinessProbe:
          exec:
            command:
            - sh
            - -c
            - "zookeeper-ready  2181"
          initialDelaySeconds: 10
          timeoutSeconds: 5
        livenessProbe:
          exec:
            command:
            - sh
            - -c
            - "zookeeper-ready  2181"
          initialDelaySeconds: 10
          timeoutSeconds: 5
        volumeMounts:
        - name: datadir
          mountPath: /var/lib/zookeeper
      securityContext:
        runAsUser: 1000
        fsGroup: 1000
  volumeClaimTemplates:
  - metadata:
      name: datadir
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 5Gi 
```

Enter fullscreen mode Exit fullscreen mode

## Traefik 的权限

```
# create Traefik cluster role
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
 name: traefik-ingress-controller
rules:
  - apiGroups:
      - ""
    resources:
      - services
      - endpoints
      - secrets
    verbs:
      - get
      - list
      - watch
  - apiGroups:
      - extensions
    resources:
      - ingresses
    verbs:
      - get
      - list
      - watch
---
# create Traefik service account
kind: ServiceAccount
apiVersion: v1
metadata:
  name: traefik-ingress-controller
  namespace: default
---
# bind role with service account
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: traefik-ingress-controller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: traefik-ingress-controller
subjects:
- kind: ServiceAccount
  name: traefik-ingress-controller
  namespace: default 
```

Enter fullscreen mode Exit fullscreen mode

## Traefik config

请注意 zookeeper 的配置，它使用“客户端服务”(cs)的服务地址，这里我们来加密配置。

```
# define Traefik configuration
kind: ConfigMap
apiVersion: v1
metadata:
  name: traefik-config
data:
  traefik.toml: |
    # traefik.toml
    defaultEntryPoints = ["http", "https"]
    [entryPoints]
      [entryPoints.http]
        address = ":80"
        [entryPoints.http.redirect]
          entryPoint = "https"
      [entryPoints.https]
      address = ":443"
        [entryPoints.https.tls]

      [zookeeper]
        endpoint = "zk-cs.default.svc.cluster.local:2181"
        watch = true
        prefix = "traefik"

      [acme]
      email = "your@email.com"
      storage = "traefik/acme/account"
      onHostRule = true
      caServer = "https://acme-v02.api.letsencrypt.org/directory"
      acmeLogging = true
      entryPoint = "https"
        [acme.httpChallenge]
        entryPoint = "http"

      [[acme.domains]]
        main = "your.domain.com" 
```

Enter fullscreen mode Exit fullscreen mode

## 为 Traefik 部署

我在这里只运行了一个副本，以节省我的开发设置成本，但我也将其扩展到三个，以测试它是否会 100%的时间保持运行，即使随机节点关闭，一切都正常:)。

```
# declare Traefik deployment
kind: Deployment
apiVersion: extensions/v1beta1
metadata:
  name: traefik-ingress-controller
spec:
  replicas: 1
  template:
    metadata:
      labels:
        app: traefik-ingress-controller
    spec:
      serviceAccountName: traefik-ingress-controller
      terminationGracePeriodSeconds: 60
      volumes:
        - name: config
          configMap:
            name: traefik-config
      containers:
      - name: traefik
        image: "traefik:1.7.14"
        volumeMounts:
          - mountPath: "/etc/traefik/config"
            name: config
        args:
        - --configfile=/etc/traefik/config/traefik.toml
        - --kubernetes
        - --logLevel=INFO 
```

Enter fullscreen mode Exit fullscreen mode

## 流量服务

```
# Declare Traefik ingress service
kind: Service
apiVersion: v1
metadata:
  name: traefik-ingress-controller
spec:
  selector:
    app: traefik-ingress-controller
  ports:
    - port: 80
      name: http
    - port: 443
      name: tls
  type: LoadBalancer 
```

Enter fullscreen mode Exit fullscreen mode

# 最终结果

trafik 和 zookeeper 的最终工作量

[![Traefik and Zookeeper workloads](img/9b09f24537a7d8fbc024b8281bd85da6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--f4wdYXMm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rhazn.com/img/posts/run-a-personal-cloud-with-traefik-lets-encrypt-and-zookeeper/workloads.png)

和 kubernetes 入口(忽略我用作演示的应用程序)

[![Kubernetes ingresses](img/7bb5ffb69aee7a6ae6bfd0416f8c06fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--alKXaAjp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://rhazn.com/img/posts/run-a-personal-cloud-with-traefik-lets-encrypt-and-zookeeper/ingress.png)

# 关于我

我是一个全栈开发者和数字产品爱好者，我可以自由工作，并总是在寻找下一个令人兴奋的项目:)。

你可以通过电子邮件(pheltweg@gmail.com 或者推特 https://twitter.com/rhanarion T2 联系我。