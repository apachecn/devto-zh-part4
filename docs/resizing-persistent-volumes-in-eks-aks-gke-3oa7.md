# 调整 EKS/阿克苏/GKE 的持久卷的大小

> 原文：<https://dev.to/7d1/resizing-persistent-volumes-in-eks-aks-gke-3oa7>

Kubernetes 中的持久卷(PVs)是提取集群中的 pod 所使用的块卷的方法。pod 请求 PVs 的方式是通过创建永久卷声明(PVC ),它的工作方式类似于资源需求文档。存储类负责如何实际处理 PV 的实际实现，它可以由 ceph volumes 提供，或者在云提供商的情况下，由 AWS 中的 EBS volumes、Azure 中的托管磁盘和 GCP 中的磁盘提供。

这篇文章是我写给自己的，当时我正在检查 PV resize 在托管 kuberentes 服务中是如何工作的。随意复制这些命令，看看会发生什么。

## 亚马逊弹性 Kubernetes 服务

让我们先用`eksctl`快速创建集群。请注意，这需要很长时间。

`eksctl create cluster -n darek -r eu-central-1`

过一会儿，您应该会看到一些节点正在运行。

```
➜  ~ kubectl get nodes 
NAME                                              STATUS   ROLES    AGE   VERSION
ip-192-168-26-190.eu-central-1.compute.internal   Ready    <none>   67s   v1.13.7-eks-c57ff8
ip-192-168-55-249.eu-central-1.compute.internal   Ready    <none>   67s   v1.13.7-eks-c57ff8 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看存储类。

```
➜  ~ kubectl get sc gp2 -o yaml 
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"storage.k8s.io/v1","kind":"StorageClass","metadata":{"annotations":{"storageclass.kubernetes.io/is-default-class":"true"},"name":"gp2"},"parameters":{"fsType":"ext4","type":"gp2"},"provisioner":"kubernetes.io/aws-ebs"}
    storageclass.kubernetes.io/is-default-class: "true"
  creationTimestamp: "2019-08-24T09:23:35Z"
  name: gp2
  resourceVersion: "281"
  selfLink: /apis/storage.k8s.io/v1/storageclasses/gp2
  uid: d903b89e-c650-11e9-968c-0273bdd9611a
parameters:
  fsType: ext4
  type: gp2
provisioner: kubernetes.io/aws-ebs
reclaimPolicy: Delete
volumeBindingMode: Immediate 
```

Enter fullscreen mode Exit fullscreen mode

要调整卷的大小，存储类需要将`allowVolumeExpansion`设置为 true。幸运的是，它可以被修补。

`k patch sc gp2 -p '{"allowVolumeExpansion": true}'`

现在，创建一个大小为 10Gi 的基本 PVC，并将 storageclass 设置为`gp2`。我们以后也会为 AK 和 GKE 使用相同的 yaml，保存为`pvc.yml`。

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: test-volume-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: gp2 
```

Enter fullscreen mode Exit fullscreen mode

创建 PVC。

`kubectl apply -f pvc.yml`

PVC 最终应该创建大小为 10 的 PV，这反映在 AWS 中创建的实际 EBS 卷中。EBS 的名称编码在`Source/VolumeID`中。做描述操作就可以得到。

```
➜  kubectl describe pv/pvc-0262b546-c65a-11e9-8f74-06e2726fa54c
Name:              pvc-0262b546-c65a-11e9-8f74-06e2726fa54c
Labels:            failure-domain.beta.kubernetes.io/region=eu-central-1
                   failure-domain.beta.kubernetes.io/zone=eu-central-1b
Annotations:       kubernetes.io/createdby: aws-ebs-dynamic-provisioner
                   pv.kubernetes.io/bound-by-controller: yes
                   pv.kubernetes.io/provisioned-by: kubernetes.io/aws-ebs
Finalizers:        [kubernetes.io/pv-protection]
StorageClass:      gp2
Status:            Bound
Claim:             default/test-volume-pvc
Reclaim Policy:    Delete
Access Modes:      RWO
VolumeMode:        Filesystem
Capacity:          10Gi
Node Affinity:
  Required Terms:
    Term 0:        failure-domain.beta.kubernetes.io/zone in [eu-central-1b]
                   failure-domain.beta.kubernetes.io/region in [eu-central-1]
Message:
Source:
    Type:       AWSElasticBlockStore (a Persistent Disk resource in AWS)
    VolumeID:   aws://eu-central-1b/vol-056a0bc0115292add
    FSType:     ext4
    Partition:  0
    ReadOnly:   false
Events:         <none> 
```

Enter fullscreen mode Exit fullscreen mode

上面的命令显示 EBS 卷名为`vol-056a0bc0115292add`。
现在让我们调整 PVC 的大小。将`pvc.yml`中的存储大小更改为 12Gi，并再次应用 kubectl。

```
➜  kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                     STORAGECLASS   REASON   AGE
pvc-0262b546-c65a-11e9-8f74-06e2726fa54c   12Gi       RWO            Delete           Bound    default/test-volume-pvc   gp2                     19h 
```

Enter fullscreen mode Exit fullscreen mode

好的，PV 显示 12Gi。让我们用 aws cli 检查实际 EBS 卷的大小。

`aws ec2 describe-volumes --volume-ids vol-056a0bc0115292add --query 'Volumes[0].Size'`

它现在应该显示 12。

> 请记住，在 Amazon 中，您只能每隔 6 小时在 EBS 上进行一次调整大小操作。所以下一次调整大小不会起作用，除非你等待它。

您可以通过`eksctl delete cluster -n darek`删除 clutser。

## 天蓝色 AKS

现在让我们检查一下 AK。在 AKS 中创建一个集群。下面的代码片段创建了一个资源组 darek 和一个名为`darekEKS`的集群。

```
az group create --name darek --location westeurope
az aks create --resource-group darek --name darekEKS --node-count 2 --enable-addons monitoring --generate-ssh-keys
az aks get-credentials --resource-group darek --name darekEKS 
```

Enter fullscreen mode Exit fullscreen mode

AKS 中的存储类称为默认，它将提供 azure 管理的磁盘。

```
➜  kubectl get sc
NAME                PROVISIONER                AGE
default (default)   kubernetes.io/azure-disk   13m
managed-premium     kubernetes.io/azure-disk   13m 
```

Enter fullscreen mode Exit fullscreen mode

让我们看看整个类，类似地，在 EKS，调整大小是不 enbled。

```
➜  kubectl get storageclasses.storage.k8s.io default -o yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{"storageclass.beta.kubernetes.io/is-default-class":"true"},"labels":{"kubernetes.io/cluster-service":"true"},"name":"default","namespace":""},"parameters":{"cachingmode":"ReadOnly","kind":"Managed","storageaccounttype":"Standard_LRS"},"provisioner":"kubernetes.io/azure-disk"}
    storageclass.beta.kubernetes.io/is-default-class: "true"
  creationTimestamp: "2019-08-24T09:31:20Z"
  labels:
    kubernetes.io/cluster-service: "true"
  name: default
  resourceVersion: "459"
  selfLink: /apis/storage.k8s.io/v1/storageclasses/default
  uid: ee21d5ae-c651-11e9-9f0b-aac03b918570
parameters:
  cachingmode: ReadOnly
  kind: Managed
  storageaccounttype: Standard_LRS
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Delete
volumeBindingMode: Immediate 
```

Enter fullscreen mode Exit fullscreen mode

让我们修补它:`kubectl patch sc default -p '{"allowVolumeExpansion": true}'`
并创建 PVC，记住将`storageClassName`改为默认值。

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: test-volume-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: default 
```

Enter fullscreen mode Exit fullscreen mode

PVC 和 PV 是创造出来的吗？

```
➜  kubectl get pvc
NAME              STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
test-volume-pvc   Bound    pvc-eb332309-c654-11e9-a0fd-36761452f1dd   10Gi       RWO            default        26s

➜  kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                     STORAGECLASS   REASON   AGE
pvc-eb332309-c654-11e9-a0fd-36761452f1dd   10Gi       RWO            Delete           Bound    default/test-volume-pvc   default                 41s 
```

Enter fullscreen mode Exit fullscreen mode

Azure 盘的名字会在`Source/DiskURI`里看到。

```
➜ ~ kubectl describe pv
Name:            pvc-eb332309-c654-11e9-a0fd-36761452f1dd
Labels:          <none>
Annotations:     pv.kubernetes.io/bound-by-controller: yes
                 pv.kubernetes.io/provisioned-by: kubernetes.io/azure-disk
                 volumehelper.VolumeDynamicallyCreatedByKey: azure-disk-dynamic-provisioner
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    default
Status:          Bound
Claim:           default/test-volume-pvc
Reclaim Policy:  Delete
Access Modes:    RWO
VolumeMode:      Filesystem
Capacity:        12Gi
Node Affinity:   <none>
Message:
Source:
    Type:         AzureDisk (an Azure Data Disk mount on the host and bind mount to the pod)
    DiskName:     kubernetes-dynamic-pvc-eb332309-c654-11e9-a0fd-36761452f1dd
    DiskURI:      /subscriptions/6690b014-bdbd-4496-98ee-f2f255699f70/resourceGroups/MC_darek_darekEKS_westeurope/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-eb332309-c654-11e9-a0fd-36761452f1dd
    Kind:         Managed
    FSType:
    CachingMode:  ReadOnly
    ReadOnly:     false
Events:           <none> 
```

Enter fullscreen mode Exit fullscreen mode

现在将磁盘大小更改为 12 并查看。我们可以使用以下命令检查磁盘大小。

```
> az disk  show --ids /subscriptions/6690b014-bdbd-4496-98ee-f2f255699f70/resourceGroups/MC_darek_darekEKS_westeurope/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-eb332309-c654-11e9-a0fd-36761452f1dd --query 'diskSizeGb'
12 
```

Enter fullscreen mode Exit fullscreen mode

Ok 在 AKS 也管用。非常奇怪的是，当你做`kubectl get pv`时，它显示新的尺寸，但 PVC 仍然显示 10Gi。

清理您创建的资源:`az group delete -n darek -y`

## 谷歌库柏发动机

我在名为`turnkey-cooler-31343`的项目中使用 CLI 创建了一个[区域集群](https://cloud.google.com/kubernetes-engine/docs/concepts/regional-clusters)。

```
gcloud config set project turnkey-cooler-31343
gcloud container clusters create darek --zone europe-north1-a --cluster-version "1.13.7-gke.19" --machine-type n1-standard-1 --num-nodes 2 
```

Enter fullscreen mode Exit fullscreen mode

并生成一个 kubeconfig:

```
gcloud container clusters get-credentials darek --zone europe-north1-a --project turnkey-cooler-31343 
```

Enter fullscreen mode Exit fullscreen mode

在 GKE，默认的存储类称为`standard`。

```
NAME                 PROVISIONER            AGE
standard (default)   kubernetes.io/gce-pd   3m44s 
```

Enter fullscreen mode Exit fullscreen mode

正如在 AKS 和 EKS 中未启用自动扩展一样，让我们修补一下存储类。

`kubectl patch sc standard -p '{"allowVolumeExpansion": true}'`

并使用我们的 pvc yaml 创建一个 PVC。记得把`storageClassName`改成`standard`。

`kubectl apply -f pvc.yml`

并查看磁盘:

```
kubectl get pv 
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                     STORAGECLASS   REASON   AGE
pvc-f79ca9c5-c750-11e9-911b-42010aa601a8   10Gi       RWO            Delete           Bound    default/test-volume-pvc   standard                8m30s 
```

Enter fullscreen mode Exit fullscreen mode

现在检查 GCP 磁盘的名称:

```
kubectl get pv/pvc-f79ca9c5-c750-11e9-911b-42010aa601a8 -o jsonpath='{.spec.gcePersistentDisk.pdName}'
gke-darek-a2e51c42-dyn-pvc-f79ca9c5-c750-11e9-911b-42010aa601a8 
```

Enter fullscreen mode Exit fullscreen mode

和磁盘的大小。

```
➜  gcloud compute disks describe gke-darek-a2e51c42-dyn-pvc-f79ca9c5-c750-11e9-911b-42010aa601a8 --zone europe-north1-a | grep sizeGb
sizeGb: '10' 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们调整磁盘大小，更改 pvc.yml 或修补 pvc。

```
➜  gcloud compute disks describe gke-darek-a2e51c42-dyn-pvc-f79ca9c5-c750-11e9-911b-42010aa601a8 --zone europe-north1-a | grep sizeGb
sizeGb: '12' 
```

Enter fullscreen mode Exit fullscreen mode

好的，一切正常，但是在 AKS 和 EKS，PVC 仍然显示 10Gi。

清洁下面:`gcloud container clusters delete darek --zone europe-north1-a`。