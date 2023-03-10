# 外部，Istio 和 AWS 号公路

> 原文：<https://dev.to/samcre/externaldns-istio-y-aws-route-53-3ah3>

在之前的文章中，您将 istio 和 cert-manager 设置为通过 HTTPS 充当 mesh 服务的 pods 协同工作。经常开玩笑，∞吗？但是，除非您在供应商中设置 DNS 记录，否则显然无法访问。手工做事，今年是吗？□什么？

在本文中，我们将在群集上保留“[external DNS](https://github.com/kubernetes-incubator/external-dns)”实用程序的安装和配置，该实用程序将根据在 Instituto 网关上配置的主机自动更新我们的 DNS 提供程序。

ExternalDNS 支持 15 个以上的 DNS 供应商，但仅有两个稳定(GCloud 和 AWS Route 53)，而且在我们的群集上安装和配置相对容易。在本部分中，我们将继续上一篇文章中的示例，并将其与 AWS Route 53 集成，以便在 Route 53 中自动创建我们在 istio 网关上激活的主机。

## 预配置

与 cert-manager 一样，但在 AWS 中，我们需要创建 IAM 的“t0”策略，以授予运行 ExternalDNS 的群集或 pods 使用 Route 53 所需的权限。在外部 DNS 本身的文档中，我们可以找到定义它的“T2”【JSON】，但基本上是这样的:

```
{  "Version":  "2012-10-17",  "Statement":  [  {  "Effect":  "Allow",  "Action":  ["route53:ChangeResourceRecordSets"],  "Resource":  ["arn:aws:route53:::hostedzone/*"]  },  {  "Effect":  "Allow",  "Action":  [  "route53:ListHostedZones",  "route53:ListResourceRecordSets"  ],  "Resource":  ["*"]  }  ]  } 
```

我认为重要的是要指出，`"Resource": ["arn:aws:route53:::hostedzone/*"]`线给予太开放的许可，我们应该对其进行修改，只指出我们希望 ExternalDNS 为我们管理的地区中的`ZONE_ID`。

显然，我们必须已经在 AWS 中创建了 DNS 区域，以便由 ExternalDNS 管理，因为此实用程序不会创建 DNS 区域。

## 安装并配置外部

为了安装外部 DNS，我们有一个来自头盔的*图表，但它的文件[【部署 YAML】](https://github.com/kubernetes-incubator/external-dns/blob/master/docs/tutorials/aws.md#manifest-for-clusters-with-rbac-enabled)非常简单，我们将仅限于复制它，并修改将外部 DNS 与路由 53 集成的参数。如果与*图表一起安装，您可以在注释中共享以下设置。**

 *我们将上一个 YAML 保存到本地文件，然后继续编辑部署部分。具体来说，`args`的值为:

```
[...]
        args:
        - --source=service
        - --source=ingress
        - --source=istio-gateway
        - --istio-ingress-gateway=custom-istio-namespace/custom-istio-ingressgateway # load balancer service to be used; can be specified multiple times. Omit to use the default (istio-system/istio-ingressgateway)
        - --domain-filter=external-dns-test.my-org.com # will make ExternalDNS see only the hosted zones matching provided domain, omit to process all available hosted zones
        - --provider=aws
        - --policy=upsert-only # would prevent ExternalDNS from deleting any records, omit to enable full synchronization
        - --aws-zone-type=public # only look at public hosted zones (valid values are public, private or no value for both)
        - --registry=txt
        - --txt-owner-id=my-identifier 
```

*   **`--source`** :我们指示外部 DNS 在群集中要分析哪些资源才能更新 DNS。如果我们只使用`istio-gateway`，我们只能留下这个。
*   **`--istio-ingress-gateway`** :这*标志只有在我们创建或使用了不同于 Instituto 缺省创建的*输入网关时才是强制性的。如果是，我们删除或评论这一行。**
***   **`--domain-filter`** :我们将外部 DNS 的更新限制在一个或多个域。如果我们去掉这个*标志*，它会更新它发现的任何域。这与创建具有必要权限的 IAM 策略密切相关。*   **`--provider`** :在这种情况下，`aws`，因为我们想将其纳入 53 号线路。*   **`--policy`** :默认情况下使用值`sync`，在任何配置的`source`中找不到 DNS 记录时，将删除 DNS 记录。`upsert-only`值只是将新记录添加到 DNS 中。*   **`--aws-zone-type`** :我们指出，拟管理的地区在 53 号公路上属于私人或公共性质。**

 **根据需要修改这些选项后，将它们应用于群集。你可以把它安装在自己的命名空间:

```
kubectl apply -n externaldns -f externaldns.yaml 
```

pod 处于`Running`状态时，如果我们配置了任何本能网关，ExternalDNS 将开始在 Route 53 中创建 DNS 记录。如果您还一无所有，我们将应用我们的`Gateway`和`VirtualService`，几分钟后，我们将在 pod 日志中看到您如何开始添加日志:

```
time="2019-06-10T15:51:33Z" level=info msg="Desired change: CREATE nginx.example.com A"
time="2019-06-10T15:51:33Z" level=info msg="Desired change: CREATE nginx.example.com TXT"
time="2019-06-10T15:51:36Z" level=info msg="2 record(s) in zone example.com. were successfully updated" 
```

使用这些选项，我们可以消除 DNS 设置，DNS 设置将由 ExternalDNS 为我们管理。这些设置对于通过持续集成在时间点呈现我们 web 的一个新功能，以及与 cert-manager 结合在一个完善的 HTTPS 上非常有用。***