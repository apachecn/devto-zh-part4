# Kubernetes 无头服务在 Kubernetes 构建无头服务

> 原文：<https://dev.to/eddiehale3/building-a-headless-service-in-kubernetes-3bk8>

你好！这是我在 dev.to 上的第一篇帖子，很高兴成为这个伟大社区的一员。

几周前，在我们的一个应用程序的开发过程中，我们遇到了一个问题，我们需要与所有相关的 pod 进行通信，而不是进行负载平衡。我们的后端有 7 个豆荚，都有相同的图像，但不同的秘密，以更新不同位置的数据库。

与其为每个位置编写一个服务并用重复的 yaml 文件产生大量开销，不如将部署的服务改为一个[无头服务](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services)是最好的方法。

* * *

### 什么是无头服务？

无头服务是一种具有服务 IP 的服务，但它将返回我们相关 Pods 的 IP，而不是负载平衡。这允许我们直接与豆荚互动，而不是代理。这就像为`.spec.clusterIP`指定`None`一样简单，无论有没有选择器都可以使用——稍后您将看到一个使用选择器的例子。

请查看这个示例配置:

```
apiVersion: v1
kind: Service
metadata:
  name: my-headless-service
spec:
  clusterIP: None # <--
  selector:
    app: test-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 见其行动

创建一个包含五个单元的部署。

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-deployment
  labels:
    app: api
spec:
  replicas: 5
  selector:
    matchLabels:
      app: api
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
      - name: api
        image: eddiehale/hellonodeapi
        ports:
        - containerPort: 3000 
```

Enter fullscreen mode Exit fullscreen mode

创建常规服务

```
apiVersion: v1
kind: Service
metadata:
  name: normal-service
spec:
  selector:
    app: api
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000 
```

Enter fullscreen mode Exit fullscreen mode

和一个无头服务

```
apiVersion: v1
kind: Service
metadata:
  name: headless-service
spec:
  clusterIP: None # <-- Don't forget!!
  selector:
    app: api
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000 
```

Enter fullscreen mode Exit fullscreen mode

应用 yaml 并验证所有部署是否正确:

```
$ kubectl apply -f deployment.yaml
$ kubectl get all
NAME                                 READY   STATUS    RESTARTS   AGE
pod/api-deployment-f457fbcf6-6j8f9   1/1     Running   0          5s
pod/api-deployment-f457fbcf6-9gvbp   1/1     Running   0          5s
pod/api-deployment-f457fbcf6-kqbds   1/1     Running   0          5s
pod/api-deployment-f457fbcf6-m76l9   1/1     Running   0          5s
pod/api-deployment-f457fbcf6-qzhxw   1/1     Running   0          5s

NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/headless-service   ClusterIP   None             <none>        80/TCP    5s
service/kubernetes         ClusterIP   10.96.0.1        <none>        443/TCP   45h
service/normal-service     ClusterIP   10.109.192.226   <none>        80/TCP    5s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/api-deployment   5/5     5            5           5s

NAME                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/api-deployment-f457fbcf6   5         5         5       5s 
```

Enter fullscreen mode Exit fullscreen mode

现在它已经全部运行了，部署一个 Pod 并执行一些命令进行测试。

```
$ kubectl run --generator=run-pod/v1 --rm utils -it --image eddiehale/utils bash
If you don't see a command prompt, try pressing enter.
root@utils:/# 
```

Enter fullscreen mode Exit fullscreen mode

让我们在每个服务上运行`nslookup`,看看存在哪些 DNS 条目。如果我们`nslookup normal-service`返回一个 DNS 条目和 IP，其中`nslookup headless-service`返回与服务 DNS 关联的 Pod IPs 列表:

```
root@utils:/# nslookup normal-service
Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   normal-service.default.svc.cluster.local
Address: 10.109.192.226

root@utils:/# nslookup headless-service
Server:         10.96.0.10
Address:        10.96.0.10#53

Name:   headless-service.default.svc.cluster.local
Address: 10.1.0.41
Name:   headless-service.default.svc.cluster.local
Address: 10.1.0.39
Name:   headless-service.default.svc.cluster.local
Address: 10.1.0.38
Name:   headless-service.default.svc.cluster.local
Address: 10.1.0.40
Name:   headless-service.default.svc.cluster.local
Address: 10.1.0.37 
```

Enter fullscreen mode Exit fullscreen mode

* * *

### 清理干净

退出实用程序窗格:

```
root@utils:/# exit
exit
Session ended, resume using 'kubectl attach utils -c utils -i -t' command when 
the pod is running
pod "utils" deleted 
```

Enter fullscreen mode Exit fullscreen mode

删除服务和部署

```
$ kubectl delete svc headless-service normal-service && kubectl delete deployment api-deployment
service "headless-service" deleted
service "normal-service" deleted
deployment.extensions "api-deployment" deleted 
```

Enter fullscreen mode Exit fullscreen mode

无头服务允许我们直接到达每个 Pod，而不是充当负载平衡器或代理的服务。这可以有许多用例，我很乐意在评论中听到你的经验和想法！