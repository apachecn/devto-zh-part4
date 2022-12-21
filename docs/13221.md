# 开始我们的区块链编程之旅

> 原文：<https://dev.to/danvoyce/starting-our-blockchain-programming-journey-53f>

我们在 2018 年 6 月初开始了这段旅程。我们是一个由 IT 专家组成的小型、有才华且充满激情的团队。我们从他们开始进化和成长，但是我们仍然很紧密。我们即将涉足加密领域，开始打造一款名为 LOCALLY Reward 的全新产品。

LOCALLY Reward 是一个建立在 NEM 区块链之上的参与平台。它根据人们在我们系统上共享的数据，奖励他们 NEM 加密货币。我们在幕后有几层机器学习、人工智能和数据处理算法来帮助我们建立高质量的数据。

我们是移动团队，我们在这个项目上的主要目标是为 iOS 和 Android 建立一个框架，允许人们分享数据和监控他们的奖励。

## NEM 的记述

当您第一次使用 LOCALLY Reward 时，我们会为您创建一个 NEM 帐户，以便您继续使用和提取您的资金。NEM 账户就像加密世界中的银行账户，它有一个地址和一个公钥和私钥。地址和公钥被用作您在区块链上接收和发送资金的标识符。私钥就像是您访问帐户的密码。

NEM 有一个名为 NIS 的公共 API，在我们开始这个项目时，我们认为大多数与帐户管理相关的功能将通过 API 调用和网络服务来处理。事实并非如此，我们最终不得不自己实现与 NEM 账户相关的所有特性(稍后会详细介绍)。

NEM 客户是 LOCALLY Reward 的核心，是基础设施的重要组成部分，也是我们在这个项目中面临的最大项目之一。这背后的主要原因是，为了创建一个 NEM 帐户，我们还必须处理一些加密库、算法、哈希和数据结构，这些必须以非常特定的顺序实现，以符合 NEM 规范。

除此之外，当我们开始这个项目的时候，缺乏文档和可靠的实现。幸运的是，NEM 有一个非常活跃和支持的社区，当困难时刻到来时，他们伸出了援助之手。如果没有他们，我们将无法解决这个问题，但即使有了所有的支持，我们仍然需要花费大量时间在不同的时区进行协调、沟通等。当我们有机会用 C#实现一段像样的代码时，我们才彻底了解了这个问题(感谢林锴)。

我们正在将这个实现移植到移动平台上(Swift 和 Kotlin)。我们从 Swift 开始，因为我们认为由于语言的易变性和当时缺乏参考资料，这将更具挑战性。

NEM 帐户的生成完全基于密钥对(私钥和公钥)的概念，其中私钥是其他创建元素的中枢。

*这里有一个私钥生成的快速提示:*

```
var privateKeyBytes = [UInt8](repeating: 0, count: keySize)
createPrivateKey(&privateKeyBytes) 
```

正如你所看到的，私钥是由一个 32 字节的数组随机产生的，并被转换成十六进制。

在 Swift 中，我们使用私钥生成公钥，然后创建一个密钥对。

```
static func generatePublicKey(fromPrivateKey privateKey: String) -> String {

    var publicKeyBytes = ['UInt8'](repeating: 0, count: keySize)
    var privateKeyBytesEndian = privateKey.toEndianArray(privateKey.bytes.count)
    createPublicKey(&publicKeyBytes, &privateKeyBytesEndian)

    return Data(bytes: publicKeyBytes).toHexString()
} 
```

选择公钥和网络后，我们使用 Sha3 和 RipeMd160 生成帐户地址。

```
static func address(withPublicKey publicKey: String, network: Network = .mainnet) -> String {

        // First Step
        let sha3 = publicKey.toByteArray().sha3(.keccak256)

        // Second Step
        let sha3data = NSData(bytes: sha3, length: sha3.count)
        let RIPEMD160 = RIPEMD.digest(sha3data) as NSData

        // Third Step
        let networkRIPEMD160Bytes = [network.rawValue] + (RIPEMD160 as Data).bytes

        // Fourth Step
        let networkRIPEMD160BytesSha256 = networkRIPEMD160Bytes.sha3(.keccak256)

        let checksumArray = [networkRIPEMD160BytesSha256[0],
                             networkRIPEMD160BytesSha256[1],
                             networkRIPEMD160BytesSha256[2],
                             networkRIPEMD160BytesSha256[3]]

        let checksumBuffer = networkRIPEMD160Bytes + checksumArray
        let checksumData = Data(bytes: checksumBuffer, count: checksumBuffer.count)

        return Base32Encode(checksumData)
} 
```

有了公钥、私钥和地址，我们现在可以创建一个 NEM 帐户。

在 Swift 中，我们只用一行代码就做到了:

```
Account(network: network) 
```

## NEM 钱包

NEM 钱包基本上是我们存放 NEM 账户的地方。我们使用公共帐户、选定的网络和密码，通过 AES256 和 Keccak 等加密算法来生成这些数字钱包。这个钱包的伟大之处在于它完全兼容任何其他 NEM 应用程序。例如，一旦您在 LOCALLY Reward 上拥有了一个帐户，您就可以导出您的钱包，并在任何其他桌面客户端(Mac、Linux 和 Windows)[[https://nem.io/downloads/](https://nem.io/downloads/)]中下载和访问您的帐户详情。

*下面是钱包一代的一个片段:*

```
static func generate(withAccount account: PublicAccount, password: String, network: Int = Network.mainnet.intValue) -> Wallet? {
        guard let encryptedKey = Crypto.encryptPrivateKey(privateKey: account.privateKey, withPassword: password) else { return nil }
        return Wallet(address: account.address, encrypted: encryptedKey.encrypted, iv: encryptedKey.encryptedIV, network: network)
} 
```

这里的想法是创建一个加密的私钥，它将安全地存储在数字钱包中。让我们看看 encryptPrivateKey 方法的内部。

首先，我们给密码 20 轮派生的 Sha3，然后我们将派生的 Sha3 转换为十六进制，并将其与密码一起发送到 AES256Encrypt。

```
static func encryptPrivateKey(privateKey: String, withPassword password: String) -> EncryptedKey? {

    guard privateKey.isHexadecimal else { return nil }

    let derivedSha3 = derivedPassSha(string: password, count: 20)

    return AES256Encrypt(inputString: privateKey, hashKey: derivedSha3.toHexString())
} 
```

AES256Encrypt 接收私钥、使用密码和随机 IV(初始化向量)生成的哈希。

```
static func AES256Encrypt(inputString: String, hashKey: String, iv: [UInt8] = AES.randomIV(16)) -> EncryptedKey? {

    let inputStringBytes = inputString.toByteArray()

    let inputStringData = Data(bytes: inputStringBytes, count: inputStringBytes.count)

    guard let encryptedData = inputStringData.aesEncrypt(key: hashKey.toByteArray(), iv: iv) else { return nil }

    return EncryptedKey(encrypted: encryptedData.toHexString(), encryptedIV: iv.toHexString())
} 
```

该算法用私钥生成一个字节数组，用传递的哈希生成另一个字节数组。然后，它将随机 IV 发送到另一个 AES 加密算法，以创建一个字节数组。当字节数组返回时，我们生成两个十六进制字符串，一个包含返回的字节数组，另一个包含随机的 IV。

## 结论

在本章中，我们讲述了在 iOS 平台上构建 NEM 账户和数字钱包时所面临的一些挑战。在下一章，我们将讨论签名事务(马赛克、签名事务、消息等)以及我们如何构建一个能够与 NEM 区块链通信的网络层。

| ![](img/defb095227491e8def10705c7a5766b6.png) | **Eduardo Dias -首席移动平台开发人员**我已经在软件开发行业工作了近二十年。凭借对所有软件开发生命周期的深刻理解，我参与了大量项目，这些项目提供了出色的用户体验，并超越了客户的期望。 |