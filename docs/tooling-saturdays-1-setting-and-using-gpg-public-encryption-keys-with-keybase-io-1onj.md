# 工具星期六#1:用 keybase.io 设置和使用 GPG 公共加密密钥

> 原文：<https://dev.to/shermix/tooling-saturdays-1-setting-and-using-gpg-public-encryption-keys-with-keybase-io-1onj>

*Cross 在[shermisaurus . XYZ](https://shermisaurus.xyz/)T3 从我的博客的 RSS 订阅源发布*

**简介**

首先，你可能对 GitHub a VCS 很熟悉，但是 keybase 到底是什么？直接引自 keybase.io 的文档:

> Keybase 的核心功能是以标准化的格式为我们的用户存储公共签名

同样来自维基百科:

> Keybase 是一个密钥目录，它以可公开审计的方式将社交媒体身份映射到加密密钥。Keybase 提供了一个端到端的加密聊天和云存储系统，分别叫做 Keybase Chat 和 Keybase filesystem。

在这篇非常**第一个工具周六**的文章中，我们将学习如何使用 keybase 来制作我们自己的公共加密密钥，它将保存我们所有的社交媒体身份，如 GitHub、Reddit、Twitter 等。Keybase 制作的密钥称为 PGP 密钥。*在本文页脚了解更多关于 PGP 和 GPG 的关系。*

维基百科上对 PGP 密钥的定义是:

> 相当好的隐私是一个加密程序，为数据通信提供加密隐私和认证。PGP 用于对文本、电子邮件、文件、目录和整个磁盘分区进行签名、加密和解密，并提高电子邮件通信的安全性

在当前场景中，PGP 以及由此衍生的 GPG 密钥的使用不仅局限于电子邮件通信，还广泛用于跨诸如 GitHub 之类的源代码控制平台来签署提交，从而向存储库所有者验证提交来自可信来源并且被验证。

* * *

**用途**

确实存在在命令行中手工制作 GPG 键的方法，但是在本文中，我们将在由 [keybase.io](https://keybase.io/) 提供的一个易于查看的 GUI 界面中制作我们的键。

我们将使用从 Keybase 生成的 PGP 密钥添加到本地工作站上的 GitHub 提交和验证的 Git 签名中。我们还可以将这些键用于许多其他目的，但是这些将会在以后的工具文章中使用，当我发现与这些键的集成足够酷的时候。我所有的目光都集中在制作一个基于 U2F 和 FIDO2 的安全钥匙，用物理笔驱动器解锁我们的笔记本电脑，而无需输入密码。我听说它还能改善启动时间。

* * *

**配方**

_ **从密钥库中获取您的 PGP 密钥:** _

*   注册一个 keybase.io 帐户[在这里](https://keybase.io/)
*   您可以从页面为您选择的平台安装应用程序。该程序还会动态安装 keybase CLI 实用程序。
*   按照屏幕上的说明在各种媒体平台上设置身份，你们都可以做到。
*   验证完同一列中的所有身份后，最后添加您的 PGP 指纹。

_ **将 GPG 密钥添加到 GitHub 和 git:** _

*   假设您已经在工作站上安装并配置了 keybase 应用程序，启动您的终端应用程序。

## ——首先获得公钥`keybase pgp export | gpg --import`

导入私钥`keybase pgp export -s | gpg --allow-secret-key-import --import`在此命令中，keybase 可能会要求您验证并为密钥创建一个密码短语。

验证`gpg --list-secret-keys`

这样的事情出现了

```
/home/shermisaurus/.gnupg/pubring.kbx
------------------------------------------
sec rsa4086 2019-07-05 [SC] [expires: 2035-07-01]
      63CBCDE92
uid [unknown] Ankesh Bharti <ankeshbharti8@gmail.com>
ssb rsa4086 2019-07-05 [E] [expires: 2035-07-01] 
```

Enter fullscreen mode Exit fullscreen mode

电子邮件地址应该与您的 Github 电子邮件地址相同。

`63CBCDE92`部分是你接下来需要的。不，我们将使这个密钥“可信”。

*   编辑信任

```
$ gpg --edit-key C9D8E1A1
gpg> trust
Please decide how far you trust this user to correctly verify other users' keys
(by looking at passports, checking fingerprints from different sources, etc.)

  1 = I don't know or won't say
  2 = I do NOT trust
  3 = I trust marginally
  4 = I trust fully
  5 = I trust ultimately
  m = back to the main menu

Your decision? 5
Do you really want to set this key to ultimate trust? (y/N) y 
```

Enter fullscreen mode Exit fullscreen mode

根据您的意愿选择信任级别，在我的情况下，我更喜欢并推荐使用`5 = I trust ultimately`。

现在将它添加到您的 GitHub 个人资料:`gpg --armor --export C9D8E1A1`

在[https://github.com/settings/keys](https://github.com/settings/keys)复制并粘贴输出

现在，对于本地工作站上的 git，使用下面的命令。

```
shermisaurus@pop-os:~$ gpg --list-secret-keys --keyid-format LONG
gpg: checking the trustdb
gpg: marginals needed: 3 completes needed: 1 trust model: pgp
gpg: depth: 0 valid: 1 signed: 0 trust: 0-, 0q, 0n, 0m, 0f, 1u
gpg: next trustdb check due at 2035-07-01
/home/shermisaurus/.gnupg/pubring.kbx
------------------------------------------
sec rsa4086/7E8134F35 2019-07-05 [SC] [expires: 2035-07-01]
      63CBCDE92A8B19FB3D44FA137E8134F3588DFA01
uid [ultimate] Ankesh Bharti <ankeshbharti8@gmail.com>
ssb rsa4086/0C40D2B88CD11958 2019-07-05 [E] [expires: 2035-07-01]

shermisaurus@pop-os:~$ git config --global user.signingkey 7E8134F35
shermisaurus@pop-os:~$ git config --global commit.gpgsign true 
```

Enter fullscreen mode Exit fullscreen mode

感谢您阅读至此:)

* * *

注意:`“GPG” stands for “Gnu Privacy Guard.” GPG is a re-write or upgrade of PGP. It does not use the IDEA encryption algorithm. This is to make it completely free. It uses the NIST AES, Advanced Encryption Standard .`