# 安全 Docker:启用 Docker 内容信任

> 原文：<https://dev.to/idevkamboj/care-about-security-enable-docker-content-trust-4djh>

我喜欢 Docker 和安全性，所以我想为什么不写 Docker 内容信任。所以给你...

[最初发布于](https://devkamboj.in/ContentTrustInDocker/)

Docker 内容信任允许你有一个图像签名，这样你可以确保你不是在运行任意代码，你是在运行你首先希望 T3 运行的代码。

当启用 Docker 信任并且发布者将图像推送到远程注册表时，Docker 引擎使用发布者的私钥在本地对图像进行签名。当有人提取此图像时，Docker 使用发布者的公钥来验证图像的完整性，并检查图像是否完全是发布者创建的、未被篡改并且是最新的。

## 启用内容信任

*   通过环境变量启用

```
 $> export DOCKER_CONTENT_TRUST=1 
```

*   或 CLI 标志:

```
 $> docker pull --disable-content-trust=false mongo:tag 
```

一旦使用环境变量
启用 docker 内容信任

```
 $> export DOCKER_CONTENT_TRUST=1 
```

每个 docker 单个 docker 操作都是安全的

*   码头推送
*   码头运行
*   码头工人拉动

现在每一个操作都要对签名进行操作。如果你试图运行未签名的图像 docker 不会允许你。

如果您正在创建应用程序的 docker 映像，并将其推送到存储库，然后通过网络将其拉出。那么您应该启用内容信任。

#### 签署 Docker 图像的步骤。

*   启用 docker 内容信任

```
 $> export DOCKER_CONTENT_TRUST=1 
```

*   推送图像

```
 $> docker push  [yourImage:tag] 
```

如果你是第一次这样做，它会生成**根密钥**、**回购密钥**和其他密钥。*(默认存储在~/。docker/trust/)* 。您需要输入**根密钥**和**回购密钥**的密码。给出密码短语后，它将推送签名的图像。

您应该将根密钥备份到安全的地方。假设只需要创建新的存储库，那么将它离线存储在硬件中是一个好主意。
[为 DCT 管理密钥。](https://docs.docker.com/engine/security/trust/trust_key_mng/)

从现在开始，docker 推送操作将提示您输入回购的密码。如果启用了内容信任。但是这是可以自动化的。

## 自动化与内容信任

大多数时候，我们使用自动化系统来构建图像。为了在我们的自动化系统上启用内容信任，我们可以使用环境变量。

```
$> export DOCKER_CONTENT_TRUST_REPOSITORY_PASSPHRASE="strongpassword" 
```

如果使用此变量，则不需要每次都输入密码。

Docker 内容信任在 Docker CS 引擎 1.9.0 和 Docker EE 中可用。将来可能会默认启用 Docker 内容信任。