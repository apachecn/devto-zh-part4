# Linux 和 Mac OS 中的环境变量

> 原文：<https://dev.to/gajesh/environment-variables-in-linux-and-mac-os-4j30>

如果您从事的项目包含 API 或任何涉及令牌与服务器交互的内容，那么您可能会使用环境变量。本文解释了如何在 MAC 和 Linux 操作系统中创建环境变量。他们也可以阻止灾难的发生。

什么是环境变量，为什么要使用它们？
环境变量是具有特定含义的系统变量，必要时可在整个操作系统中引用。在 bash 中，它们通常以$符号开始。还必须注意的是，当存储 API 密钥或生产的秘密数据时，[环境变量并不是最好的选择](https://softwareengineering.stackexchange.com/questions/352653/how-to-manage-environment-variables-in-local-development)。它们便于测试和开发，因为大多数语言都有内置的支持。

## 创建变量。

如果是 Linux，请按照以下格式将您的密钥放在`~/.bashrc`中；如果是 Mac OS，请按照以下格式放在`~/.bash_profile`中。

`export SECRETKEYNAME="Some-Key-here"`

**注意:**变量名只能是大写字母。

将密钥复制到文件后，下一步是刷新文件，使变量可以在整个系统中访问。你可以使用命令 source `~/.bashrc` Linux 或者 source`~/.bash_profile`Mac OS 来完成。你可以使用`printenv`查看 Mac 和 Linux 操作系统上的所有变量。您也可以使用终端访问现有变量，如`$HOME`或`$USER`。

**示例:** echo `$HOME`显示你的主目录名。

要跨会话和重启设置环境变量，您可以将它们存储在`~/.bashrc`、`~/.bash_profile`或`~/.profile`中。

使用环境变量的实践是避免敏感信息泄漏和密钥的一个很好的方法，尤其是在处理开源项目和公共存储库的时候。

### 参考文献及进一步阅读:

1.  [AWS Hack 上的 Quora 线程和 50K 账单](https://www.quora.com/My-AWS-account-was-hacked-and-I-have-a-50-000-bill-how-can-I-reduce-the-amount-I-need-to-pay)。
2.  [维基百科上的环境变量](https://en.wikipedia.org/wiki/Environment_variable)
3.  [如何管理本地开发中的环境变量](https://softwareengineering.stackexchange.com/questions/352653/how-to-manage-environment-variables-in-local-development)
4.  [数字海洋的泛读](https://www.digitalocean.com/community/tutorials/how-to-read-and-set-environmental-and-shell-variables-on-a-linux-vps)