# 密钥库的 PGP 子密钥

> 原文：<https://dev.to/kasparsd/pgp-subkeys-for-keybase-4n0b>

存储在 Yubikey 上的 PGP 密钥(包括子密钥)的私有部分不能导出，因此您必须始终使用实际的 Yubikey 来加密、解密、签名和验证消息。[存储在硬件密钥之外的子密钥](https://wiki.debian.org/Subkeys)可以简化日常的加密和签名操作，并且可以独立于主密钥被撤销。

所以我使用 Yubikey 上的主密钥创建了一个具有加密和签名功能的新子密钥，并将它们留在了计算机上，而不是保存到 Yubikey:

```
gpg --expert --edit-key FINGERPRINTOFYOURMASTERKEY 
```

需要`--expert`标志来启用具有加密和签名能力的子密钥。使用`gpg --edit-card`查看插有 Yubikey 的万能钥匙的指纹。

现在创建一个新的子项:

```
addkey 
```

选择`(8) RSA (set your own capabilities)`选择`(S) Toggle the sign capability`和`(E) Toggle the encrypt capability`，设置密钥大小为 4096 位。

请务必将到期日设置为比已经存储在 Yubikey(如果有)上的加密子密钥的到期日更晚的日期[，否则当用户使用](https://github.com/keybase/keybase-issues/issues/1853)[在线加密表单](https://keybase.io/encrypt#kaspars)加密信息时，Keybase 将无法使用新的加密子密钥。大多数 PGP 软件会根据子密钥的创建时间和到期时间来选择子密钥。

创建子密钥后，输入`save`保存对组合公钥的更改。

现在将更新的公钥发送到您首选的 PGP 密钥服务器:

```
$ gpg --send-keys FINGERPRINTOFYOURMASTERKEY 
```

并使用[命令行工具](https://keybase.io/docs/command_line) :
访问 Keybase

```
$ keybase pgp update 
```

除非您有另一个与帐户关联的私钥，否则无法使用他们的 web UI 上传更新的公钥。在创建这个新子键之前，我所有的私钥都存储在 Yubikey 上，所以我必须使用他们的 CLI 工具。

现在，您可以导出新子密钥的私有部分。找到新子键的指纹:

```
$ gpg --list-secret-keys --with-subkey-fingerprints
---------------------------------
sec> rsa4096 2015-01-01 [SC] [expires: 2021-01-01]
      A134BA0260D43F8EACC889D994F13532A319EA5D
      Card serial no. = 0001 011111111
uid [ultimate] Kaspars Dambis <hi@kaspars.net>
uid [ultimate] [jpeg image of size 1790]
ssb> rsa4096 2015-01-01 [E] [expires: 2021-01-01]
      D2A5D7D1B4D5A75BB161AC94FD4869EA5538D9E8
      Card serial no. = 0001 011111111
ssb> rsa4096 2015-01-01 [A] [expires: 2021-01-01]
      0A153C055A881B4524C04B3F03142B71D9CDD878
      Card serial no. = 0001 011111111
ssb rsa4096 2019-01-01 [SE] [expires: 2021-01-01]
      A58B88D1220599B82097CBA30C8C9DD50841A889 
```

主密钥列在顶部，所有子密钥都在它下面。注意，存储在 Yubikey 上的所有子密钥在它们的`ssb`(秘密子密钥)标识符后面都有一个`>`字符，而新的没有这个字符。新子密钥的指纹是`A58B88D1220599B82097CBA30C8C9DD50841A889`，所以我使用下面的命令导出新子密钥的私钥:

```
gpg --armor --export-secret-subkeys FINGERPRINTOFSUBKEY! 
```

请注意指纹末尾的感叹号`!`——只需要导出特定子密钥的私钥。

将输出通过管道传输到 macOS 上的`pbcopy`，以将其复制到剪贴板:

```
gpg --armor --export-secret-subkeys FINGERPRINTOFSUBKEY! | pbcopy 
```

转到您的 Keybase 个人资料，点击您的公钥指纹旁边的`edit`,选择“托管我的私钥的加密副本”,粘贴密钥并输入您的 Keybase 密码以加密存储的密钥。

现在你可以使用 Keybase web UI 或命令行工具加密和解密信息，而不必使用 Yubikey。