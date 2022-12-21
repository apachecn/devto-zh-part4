# 特权 Pod 调试 kubernetes 节点

> 原文：<https://dev.to/dannypsnl/privileged-pod-debug-kubernetes-node-5129>

只是一个记录。

大多数时候，如果我们想进入 kubernetes 集群的一个节点，我们可以使用`ssh`。或者我们有一个主节点有公共 IP，那么我们首先访问主节点，然后访问工作节点进行调试。然而，在某些环境中，这是不可能的。在我的例子中，AKS 集群不能直接访问节点，因为我们没有给它一个公共 IP。我们现在能做什么？特权舱！

YAML 的定义是:

```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: privileged
spec:
  selector:
    matchLabels:
      name: privileged-container
  template:
    metadata:
      labels:
        name: privileged-container
    spec:
      containers:
      - name: busybox
        image: busybox
        resources:
          limits:
            cpu: 200m
            memory: 100Mi
          requests:
            cpu: 100m
            memory: 50Mi
        stdin: true
        securityContext:
          privileged: true
        volumeMounts:
        - name: host-root-volume
          mountPath: /host
          readOnly: true
      volumes:
      - name: host-root-volume
        hostPath:
          path: /
      hostNetwork: true
      hostPID: true
      restartPolicy: Always 
```

重点是:

```
 volumeMounts:
        - name: host-root-volume
          mountPath: /host
          readOnly: true 
```

我们挂载容器的主机路径`/`到`/host`。我们使用主机网络:`hostNetwork: true`和主机 PID: `hostPID: true`。

使用`DaemonSet`确保我们可以在每个节点上调试。

当我们想使用 pod 时，使用命令:`kubectl exec -ti privileged-xk23n chroot /host`。