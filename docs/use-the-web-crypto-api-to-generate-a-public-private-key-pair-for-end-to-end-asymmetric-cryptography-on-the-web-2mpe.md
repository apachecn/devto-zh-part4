# 使用 Web Crypto API 为 Web 上的端到端非对称加密生成公钥/私钥对

> 原文：<https://dev.to/jrgould/use-the-web-crypto-api-to-generate-a-public-private-key-pair-for-end-to-end-asymmetric-cryptography-on-the-web-2mpe>

我先声明我不是密码学专家——我只是一个发现自己对一般的密码学和更具体的非对称密码学感兴趣的开发者。这些概念非常深奥，我几乎不会触及其表面，但让我们尝试一下，好吗？

## 公钥现在是什么？

公钥加密(或者不对称加密，如果你讨厌的话)是密码学中的一个概念，大约一年前引起了我的兴趣，因为乍一看它没有任何意义，但似乎非常有用。它的想法是，你可以创建一组加密密钥，这些密钥被设计成协同工作，其中一个密钥可以加密数据，而这些数据只能被另一个密钥解密。

这听起来可能令人困惑，但这是我们日常使用的相当一部分加密技术(ssh、ssl、其他以`s`开头的首字母缩写词)的症结所在，而且理由很充分:我们需要能够在不受信任的环境中共享秘密。

我想知道代码，所以如果你需要更多关于公钥加密的解释，我推荐你看这个来自 Computerphile 的[视频](https://www.youtube.com/watch?v=GSIDS_lvRv4)

## 输入`window.crypto.subtle`

现在我们已经解决了*什么*的问题，让我们来看看*如何*使用[网络加密 API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API) 。

使用`crypto.subtle.generateKey`生成密钥对实际上非常简单——难的是知道使用什么设置。我想使用 RSA-OAEP，推荐的模长为`4096`，推荐的公开指数为`65437`(需要用 UInt8Array 表示)，我们将使用`SHA-256`算法散列算法。

既然我们已经弄清楚了这些变量，我们只需要将它们插入到`generateKey`方法中:

```
 const keyPair = await crypto.subtle.generateKey(
      {
        name: "RSA-OAEP",
        modulusLength: 4096,
        publicExponent: new Uint8Array([1, 0, 1]),
        hash: "SHA-256"
      },
      true,
      ["encrypt", "decrypt"]
    ); 
```

上面的代码将返回一个承诺，该承诺将与一个看起来像`{publicKey: CryptoKey, privateKey: CryptoKey`的 [`CryptoKeyPair`](https://developer.mozilla.org/en-US/docs/Web/API/CryptoKeyPair) 对象一起解析。很简单。

## 现在怎么办？

确实是这样，但也没什么用。首先，`CryptoKeyPair`对象中包含的`CryptoKey`将只在你当前所在的页面上起作用。所以，如果你问我，他们基本上只适合在他们目前的状态下进行演示。

我们需要的下一件事是能够导出它们，以便它们可以被重用，在我看来，最方便的方法是将它们导出为 JSON Web 密钥，因为 Web Crypto API 支持开箱即用:

```
 const publicKey = await crypto.subtle.exportKey("jwk", keyPair.publicKey);
  const privateKey = await crypto.subtle.exportKey("jwk", keyPair.privateKey); 
```

现在我们已经将我们的密钥对导出为 JWKs，我们可以将我们的私钥保存在某个安全的地方——例如——发布我们的公钥，以允许只有我们可以解密的加密消息通过不可信的方式发送给我们。但是我们会留到下一天。