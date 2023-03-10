# 通过访问者处理保管库租赁

> 原文：<https://dev.to/digitalonus/dealing-with-vault-leases-via-accessors-53a7>

通常，在部署 Vault Enterprise 后管理它时，您需要对其进行配置。

设计和规划之后的大部分工作是通过其 API 或 API 的包装来配置 Vault Enterprise。

但是，有时，尤其是在排除故障或自动执行某些操作时，Vault Enterprise 的管理员必须管理 Vault 本身的状态，而不仅仅是配置。

如果出现问题，比如潜在的不希望的访问或应用程序行为不当，这种情况尤其如此。

作为调查的一部分，如果您想查看所有当前有效的访问令牌、Azure 凭据或在特定日期创建的数据库凭据，该怎么办？

我将向您展示一个脚本，它将为您做到这一点。不过，在此之前，我们将下载一个二进制文件来设置一个“虚拟”保险库进行测试。您不需要安装任何东西，或者编写任何配置文件。

为了了解一些背景知识，我建议在我们深入之前阅读一下 Vault 租赁& Vault 使用的租赁“层次结构”:

[https://www.vaultproject.io/guides/identity/lease/](https://www.vaultproject.io/guides/identity/lease/)

文档可能有点混乱，但是希望在本指南中使用它会使事情变得更清楚。

## 下载金库

我们将下载 HashiCorp Vault 的二进制文件，因为我们可以用它来运行一个 easy Vault 服务器:

[https://www.vaultproject.io/downloads.html](https://www.vaultproject.io/downloads.html)

下载并解压缩二进制文件后，打开一个终端窗口。

### 如果你在 Mac 上，打开一个 Bash 终端

打开“应用程序”文件夹，然后“实用工具”并双击“终端”，或者按 Command +空格键启动 Spotlight，然后键入“终端”，并双击搜索结果。你会看到一个白色背景的小窗口在你的桌面上打开。

### 如果你在 Windows 操作系统上，打开一个 Shell 终端

按住`Windows`键，按一次 R 键，打开运行对话框。然后，输入`cmd`。当你按下`Enter`键，进入`cmd`后，你会看到一个黑底白字的窗口。

在您刚刚打开的 shell 终端中，使用`cd`命令以及`dir`或`ls`命令，导航到您下载 Vault 的位置。

## 运行 Vault 服务器

将终端导航到下载并解压缩 Vault 的文件夹后，在终端中输入以下内容:

`./vault server -dev -dev-root-token-id=root`

您应该可以从 Vault 中看到一些输出，但是您将无法在此终端中输入更多的命令。

若要继续输入更多命令，请打开另一个终端，并导航到下载和提取 Vault 的同一文件夹。

## 连接到保险库

Mac: `export VAULT_TOKEN=root`
`export VAULT_ADDR=http://127.0.0.1:8200`
`./vault status`

最后一个命令是带有`status`的命令，它应该显示您的存储库的状态。这表明你可以连接到它。

## 下载脚本

`git clone https://github.com/v6/delete_old_tokens`
T1】

## 运行脚本

*(这部分可能在 Windows 上不能用，但是请不要让我阻止你把代码转换成 PowerShell 之类的。)*

`./list_accessor_issue_time.sh | grep 2019-09-13`

如果您想要搜索不同的日期，比如 2019 年 9 月 20 日，您应该改为运行以下内容:

`./list_accessor_issue_time.sh | grep 2019-09-20`