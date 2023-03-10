# 双重加密

> 原文：<https://dev.to/wagslane/dual-encryption-3mf9>

Qvault 的双重加密允许用户要求需要两把钥匙来解锁他们的保险库。密码和门卡。

你可能听说过双因素认证。根据 [Authy](https://authy.com/what-is-2fa/) :

> 2FA 是一个额外的安全层，用于确保试图访问在线帐户的人是他们所说的人。

通常第二个因素是你拥有的东西，而不是你知道的东西。例如，你拥有的东西可以是一个可以通过给定的电话号码接收短信的设备。

<figure>[![](img/001d0754448ba7b2b42d5c1a3d05e9bd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3v75c6hZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://qvault.io/wp-content/uploads/2019/06/pcdnyt1-1024x683-1024x683.jpg) 

<figcaption>Qvault 钥匙卡</figcaption>

</figure>

### 用 Qvault 卡双重加密

2FA 适用于 web 应用程序和网站。但它不能用于 Qvault，因为** Qvault 在您的计算机上本地**进行所有加密。本地加密更安全，因为这意味着除了 Qvault 软件(它是开源的，由我们的社区审查)之外，您不需要信任任何东西

Qvault 制造和发行具有独特 QR 码的物理塑料卡，该 QR 码包含随机和独特的 256 位加密密钥。在锁定和解锁保险库时，Q Vault 首先使用用户的密码加密机密，然后使用加密密钥(通过扫描卡的二维码获得)再次加密。通过使用双重加密，保险库可以变得更加安全。

### q vault 因为知道二维码所以可以访问保险库吗？

不。原因有二:

1.  **Qvault 无法访问保险库的密码**，这是第一级加密。这只有用户知道。
2.  在每张卡的制造过程完成后，Qvault 删除每把钥匙的所有记录。

#### 在撰写本文时，Qvault 正处于公测阶段，我们邀请所有人下载它，并告诉我们如何改进！

跟着我们走！[https://medium.com/qvault](https://medium.com/qvault)

莱恩·瓦格纳