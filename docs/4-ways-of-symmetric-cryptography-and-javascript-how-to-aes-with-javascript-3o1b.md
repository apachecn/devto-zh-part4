# JavaScript 中对称加密的 4 种方式/如何使用 JavaScript 实现 AES

> 原文：<https://dev.to/halan/4-ways-of-symmetric-cryptography-and-javascript-how-to-aes-with-javascript-3o1b>

在很大程度上，互联网的安全系统只是使用 HTTPS 作为一种协议(HTTP over SSL/TLS)，所以从浏览器发送到服务器的所有数据，包括路径，都将在服务器端加密，然后在服务器端解密。此外，所有来自服务器端的数据都将在浏览器上加密和解密。这些类型的系统保护我们免受连接之间的拦截，这就足够了。

但是，假设您不能在数据库中存储纯文本。您希望在发送前从浏览器加密，因为您不想接触或负责纯文本。或者，只是在发送前的上传过程中解密文件，甚至在浏览器上加密或解密电子邮件。这种加密的案例数不胜数。

在这里，我想介绍如何在浏览器上甚至在服务器端使用 Node.js 使用 JavaScript 来使用最常见的对称加密算法。我可以稍后再写非对称加密，但这篇文章已经足够大了。

# 4 个平台

目前至少有 4 个重要的平台可以使用 JavaScript 构建加密系统。

*   2015 年左右从 0.10.x 版本开始的一个原生节点实现，更新到最新版本(查看最新文档:[https://nodejs.org/api/crypto.html#crypto_crypto](https://nodejs.org/api/crypto.html#crypto_crypto))；

*   一个名为 Web Cryptography API 的 API 的原生实现，W3C 从 2012 年推荐至今(查看 2017 年的最新推荐:[https://www.w3.org/TR/WebCryptoAPI/](https://www.w3.org/TR/WebCryptoAPI/))并且已经被所有浏览器【https://caniuse.com/#feat=cryptography】T2 支持(你也可以在你的浏览器这里[https://diafygi.github.io/webcrypto-examples/](https://diafygi.github.io/webcrypto-examples/)查看实现细节)。这是目前推荐的解决方案，它解决了使用 JavaScript 处理密码系统时的一些经典问题；

*   2009 年首次发布的一个非常好的完整的纯 JavaScript 实现！它于 2013 年被废弃，4 个月后被重新采用。它被称为 CryptoJS，现在仍被 GitHub 上的~92k 项目使用；

*   在纯 JavaScript 中也有一个非常健壮和现代的实现，叫做 Forge。它于 2013 年首次发布，今天仍在更新，GitHub 上的 1965k 个项目都在使用它！

*   此外，在 JavaScript 中有一个很大的加密库列表是基于这个主旨维护的:[https://gist.github.com/jo/8619441](https://gist.github.com/jo/8619441)；还有那个页面:【http://cryptojs.altervista.org/】T2。

总的来说，就版本而言，密码学和 JavaScript 是一个非常新的主题，主要是与其他语言及其标准库中通常构建的 openSSL 包装器相比。我发现了一个关于过去 10 年来所有与 JavaScript 密码学相关的主要讨论的总结，值得一看:[http://blog.kotowicz.net/2014/07/js-crypto-goto-fail.html](http://blog.kotowicz.net/2014/07/js-crypto-goto-fail.html)。

在处理密码系统时，确切地知道自己在做什么以及了解最新的漏洞和建议是极其重要的。密码系统就像一条链条，系统的强度总是最弱部分的强度。

在本文中，我将演示如何比较使用这些工具，解释 JavaScript 对称加密的一些概念。

# 处理数据，而不是文本

在处理加密技术时，我们处理的是数据，而不是文本。最终，数据必须通过纯文本字段传输，因此也需要用文本表示。一个 UTF-8 字符由 1 到 4 个字节组成，还有一大堆字节在 UTF-8 上没有表示(像控制字符)，所以 UTF-8 表示数据效率不高。十六进制是最易读的处理数据的方式，但它便于共享，因为它每个字节使用 2 个字符！Base64 是迄今为止将数据作为字符共享的最佳方式。

让我们看看如何通过 Node.js 工具、浏览器 Forge 和 CryptoJS 使用 JavaScript 导航数据表示。

Node.js 有一个很好的接口来处理这些格式，它叫做 Buffer:

```
Buffer.from('hello world')
// <Buffer 68 65 6c 6c 6f 20 77 6f 72 6c 64>

Buffer.from('hello world').toString('hex')
// '68656c6c6f20776f726c64'

Buffer.from('hello world').toString('base64')
// 'aGVsbG8gd29ybGQ='

Buffer.from('aGVsbG8gd29ybGQ=', 'base64').toString()
// 'hello world'

Buffer.from('68656c6c6f20776f726c64', 'hex').toString()
// 'hello world'

[...Buffer.from('hello world')]
// [ 104, 101, 108, 108, 111, 32, 119, 111, 114, 108, 100 ] 
```

Enter fullscreen mode Exit fullscreen mode

在浏览器端，我们有 TextEncoder 在文本格式之间来回转换，我们有函数 atob 和 btoa 在 Base64 之间来回转换。不幸的是，为了处理十六进制，我们不得不借助 toString 和 parseInt 来映射它

```
new TextEncoder().encode('hello world')
// Uint8Array(11) [104, 101, 108, 108, 111, 32, 119, 111, 114, 108, 100]

new TextDecoder().decode(new Uint8Array([104, 101, 108, 108, 111, 32, 119, 111, 114, 108, 100]))
// "hello world"

[...(new TextEncoder().encode('hello world'))]
  .map(b => b.toString(16).padStart(2, "0")).join('')
// "68656c6c6f20776f726c64"

"68656c6c6f20776f726c64".match(/.{1,2}/g)
  .map(e => String.fromCharCode(parseInt(e, 16))).join('')
// 'hello world'

btoa('hello world')
// "aGVsbG8gd29ybGQ="

atob('aGVsbG8gd29ybGQ=')
// "hello world" 
```

Enter fullscreen mode Exit fullscreen mode

CryptoJS 使用的接口与 Node.js 的 Buffer 非常相似。在所有表现形式之间转换非常容易。最后，CryptoJS 使用一个内部表示来处理一组字(32 位):

```
var CryptoJS = require('crypto-js')

CryptoJS.enc.Utf8.parse('hello world')
// { words: [ 1751477356, 1864398703, 1919706112 ], sigBytes: 11 }

CryptoJS.enc.Utf8.parse('hello world').toString()
// '68656c6c6f20776f726c64'

CryptoJS.enc.Utf8.parse('hello world').toString(CryptoJS.enc.Base64)
// 'aGVsbG8gd29ybGQ='

CryptoJS.enc.Base64.parse('aGVsbG8gd29ybGQ=').toString(CryptoJS.enc.Utf8)
// 'hello world'

CryptoJS.enc.Hex.parse('68656c6c6f20776f726c64').toString(CryptoJS.enc.Utf8)
// 'hello world' 
```

Enter fullscreen mode Exit fullscreen mode

Forge 使用原生的 Uint8Array 来表示数据，在格式之间转换也非常简单:

```
var forge = require('node-forge')

forge.util.text.utf8.encode('hello world')
// Uint8Array [ 104, 101, 108, 108, 111, 32, 119, 111, 114, 108, 100 ]

forge.util.binary.hex.encode('hello world')
// '68656c6c6f20776f726c64'

forge.util.binary.base64.encode(new Uint8Array([ 104, 101, 108, 108, 111, 32, 119, 111, 114, 108, 100 ]))
// aGVsbG8gd29ybGQ=

forge.util.binary.base64.decode('aGVsbG8gd29ybGQ=')
// Uint8Array [ 104, 101, 108, 108, 111, 32, 119, 111, 114, 108, 100 ]

forge.util.binary.hex.decode('68656c6c6f20776f726c64')
// Uint8Array [ 104, 101, 108, 108, 111, 32, 119, 111, 114, 108, 100 ] 
```

Enter fullscreen mode Exit fullscreen mode

正如我们所看到的，在没有任何工具帮助的浏览器上，这并不是那么简单，主要是当我们想要在十六进制之间进行转换时。顺便说一下，在处理数据时，记住如何容易地转换碱基并理解每一步需要哪种格式是很重要的。我们将在本文中使用这些概念。

# 密码不是钥匙

浏览 https://github.com/brix/crypto-js 的公开问题，我发现几个人对对称加密和如何处理加密元素有非常相似的问题。事实上，这些问题强烈地激发了我写这篇文章的灵感。我想先解释一下这些加密元素是什么，我们应该如何对待它们，以及如何在我们的系统中使用它们。特别是关于密钥和密码的混淆。

所有加密系统都至少有一个密钥。对称加密使用相同的密钥进行加密和解密，而非对称加密使用两个密钥，一个用于加密，另一个用于解密。也有基于密钥的认证系统，其中使用密钥我们可以确保数据块的真实性。哈希算法是加密系统中非常重要的部分，它们不使用密钥(尽管它们被用于组成使用密钥的系统，请参见下一节)。

密钥的长度与字符数无关，总是与位数有关。所有密钥都有一系列不一定与字符对应的位，同时密码长度约为字符，通常密码由字符组成。密码系统使用非常严格的密钥长度，因为密钥的长度直接与算法的实现相互作用，增加或减少轮次、步骤甚至改变块的长度。密码通常有最小和最大长度，这仅与存储字段或暴力破解有关，因为密码通常用于提供哈希算法，其作用与密钥完全不同。

# 散列数据

哈希算法是将数据块转换为预先确定大小的不可预测数据块的功能。一旦经过哈希处理，内容就永远无法恢复到原始状态。此外，哈希算法必须具有抗冲突性，在某种程度上，找到两个匹配的内容是不切实际的。

第一个广泛使用的哈希算法是 MD(消息摘要)，它被 MD2、MD3、MD4 和最后的 MD5 所取代，MD5 在本世纪初首次被破解(这里有一个弱点的演示:[https://www.mscs.dal.ca/~selinger/md5collision/](https://www.mscs.dal.ca/%7Eselinger/md5collision/))。然后基于 MD4 创建了 SHA1(安全哈希算法)，也被破解了(这里可以查看一些漏洞:【https://shattered.io/】[)。目前我们使用 SHA2，这是一个能够产生 224、256、384 或 512 位哈希的算法家族。今天所有最重要的加密系统都使用 SHA2 的安全性！](https://shattered.io/)

几乎所有的加密系统都使用散列函数。还有一些与加密无关的用途，例如:git 在一个提交的参数和主体上使用 SHA1 作为一种提交引用。比特币在 256 模式下使用 SHA2 对整个交易块进行两次哈希运算，附加一个随机数(一种任意数据)，以确保工作证明。在数据库中存储密码时，必须将密码散列存储，而不是以纯文本形式存储。

对哈希表最常见的攻击是彩虹表。它们是预先计算的值表和相应的散列结果。例如，尝试在这个哈希表中键入这个哈希值`8BB0CF6EB9B17D0F7D22B456F121257DC1254E1F01665370476383EA776DF414`:[https://md5decrypt.net/Sha256](https://md5decrypt.net/Sha256)。我们在 0.1 秒内得到答案！防御措施包括在内容的末尾附加一个随机数据块，并将其散列在一起。

防止彩虹桌有两个主要的技巧:盐和胡椒。虽然 salt 是附加到原始内容的非秘密随机数据，但是 pepper 也是附加到原始内容的随机数据，但是在这种情况下，数据是秘密的。Salt 对于每个散列必须是唯一的，并且通常与内容存储在一起，因为它不是秘密，而 pepper 可以在相同的应用程序中重用，但是它需要存储在存放 salt 和散列结果的数据库之外。通过添加胡椒，暴力将是不可行的，因为胡椒的数据是未知的。

本文提到的所有 4 个平台都实现了最相关的散列函数:所有可能长度的 SHA1 和 SHA2。由于 MD5 的弱点，它从来不被 web crypto 支持。

# 从密码到密钥！

通常我们使用密码来生成密钥，这种操作被称为 KDF(密钥派生函数)。基本上，一个密码反复通过一些散列算法或一些对称加密。

在我谈论 KDF 函数之前，让我介绍另一个概念:MAC(消息认证码)。基本上，它是附加在内容上的代码，作为内容真实性的证明。HMAC 是基于哈希的消息认证码。它在内部使用一个主要的哈希函数，通常是 SHA1，在这个过程中，它会以一种非常特定的方式分别和一起哈希密码和密钥。这样，知道了密钥，我们就可以计算消息的 HMAC，并与给定的 MAC 进行比较，这就足以证明内容的完整性和真实性。我们将很快使用 HMAC，但不是出于最初的目的，我们将使用它来从给定的密码和 salt 生成一些字节。

PBKDF2(基于密码的密钥派生函数 2，由 RFC-8018:[https://tools.ietf.org/html/rfc8018#section-5.2](https://tools.ietf.org/html/rfc8018#section-5.2)描述和规定)是当今最常用和最安全的 KDF 算法之一，它可以通过增加散列的迭代次数来显著增加它们的强度。通常它使用 HMAC 散列，使用密码作为内容，盐作为密钥。迭代次数是每个块在输出和开始散列链中的下一个块之前将通过散列(HMAC)的次数，并且再次散列几次迭代，直到我们得到足够的块。通过这种方式，PBKDF2 可以生成任意数量的数据，这些数据显然是随机的，但是一旦知道了密码和 salt，就可以再现。

让我们使用 Node.js:
生成一个长度为 256 的密钥

```
var crypto = require('crypto');
derivedKey = crypto.pbkdf2Sync('my password', 'a salt', 1000, 256/8, 'sha1');
console.log(derivedKey.toString('hex'));
// 8925b9320d0fd85e75b6aa2b2f4e8ecab3c6301e0e2b7bd850a700523749fbe4 
```

Enter fullscreen mode Exit fullscreen mode

还有 CryptoJS:

```
var CryptoJS = require('crypto-js');
CryptoJS.PBKDF2('my password', 'a salt', { keySize: 256/32, iterations: 1000 }).toString();
// 8925b9320d0fd85e75b6aa2b2f4e8ecab3c6301e0e2b7bd850a700523749fbe4 
```

Enter fullscreen mode Exit fullscreen mode

带伪造:

```
var forge = require('node-forge');

forge.util.binary.hex.encode(forge.pkcs5.pbkdf2('my password', 'a salt', 1000, 256/8))
// '8925b9320d0fd85e75b6aa2b2f4e8ecab3c6301e0e2b7bd850a700523749fbe4' 
```

Enter fullscreen mode Exit fullscreen mode

让我们在浏览器上使用 webcrypto 试试:

```
// firstly we need to importKey
window.crypto.subtle.importKey(
    //the format that we are input
    "raw",
    //the input in the properly format
    new TextEncoder().encode("my password"),
    //the kind of key (in that case it's a password to derive a key!)
    {name: "PBKDF2"},
    //if I permit that this material could be exported
    false,
    //what I permit to be processed against that (password to derive a) key
    ["deriveBits", "deriveKey"]
  // the derive key process
  ).then(keyMaterial => window.crypto.subtle.deriveKey(
    {
      "name": "PBKDF2",
      salt: new TextEncoder().encode("a salt"),
      "iterations": 1000,
      "hash": "SHA-1"
    },
    // it should be an object of CryptoKey type
    keyMaterial,
    // which kind of algorithm I permit to be used with that key
    { "name": "AES-CBC", "length": 256},
    // is that exportable?
    true,
    // what is allowed to do with that key
    [ "encrypt", "decrypt" ]
  )
// exporting...
).then(key => crypto.subtle.exportKey("raw", key)
).then(key => console.log(
// finally we have a ArrayBuffer representing that key!
  [...(new Uint8Array(key))]
    .map(b => b.toString(16).padStart(2, "0"))
    .join("")
));
//8925b9320d0fd85e75b6aa2b2f4e8ecab3c6301e0e2b7bd850a700523749fbe4 
```

Enter fullscreen mode Exit fullscreen mode

正如你所看到的，当在浏览器上直接使用 webcrypto 时，会有一堆关于密钥和它能做什么的问题和权限。保护密钥很重要，但这对用户来说并不友好。

共享这些信息是安全的:

*   盐
*   相互作用
*   密钥长度
*   哈希算法

增加交互将增加算法必须做的基本散列的数量，考虑到 HMAC，每个交互将散列至少 2 个 SHA1(或者你设置的任何东西)。这可能会使进程变慢，它必须足够慢，可以运行一两次，但很难蛮力，尽量不要冻结您的浏览器哈哈！

必须随机选择好的盐，我们也可以在 4 个平台上选择:

Node.js:

```
const crypto = require('crypto');
crypto.randomBytes(8); 
```

Enter fullscreen mode Exit fullscreen mode

隐孢子虫:

```
const CryptoJS = require('crypto-js');
CryptoJS.lib.WordArray.random(8); 
```

Enter fullscreen mode Exit fullscreen mode

伪造:

```
const forge = require('node-forge');
forge.random.getBytesSync(8); 
```

Enter fullscreen mode Exit fullscreen mode

WebCrypto(浏览器):

```
window.crypto.getRandomValues(new Uint8Array(8)); 
```

Enter fullscreen mode Exit fullscreen mode

# 什么是运营模式？

当今最常用的对称加密算法是 AES(高级加密标准)。AES 是一种能够使用 128、192 和 256 个密钥长度密码分组系统，其中密钥对 128 比特的明文块进行操作，以生成 128 比特的加密文本。

AES 几乎无处不在。为了保护在亚马逊上购买的电子书，通过 SSL 加密连接，保护存储在浏览器中的会话 cookies，加密手机上的数据…无处不在！

当使用密码块系统作为 AES 时，我们应该填充纯文本，使得在解密时可以从纯文本中移除填充。最常见的填充物是 PKSC 5 号/PKSC 7 号(也以 RFC-8018[https://tools.ietf.org/html/rfc8018](https://tools.ietf.org/html/rfc8018)公布)。
给定一个 11 字节的十六进制数，填充 16 字节:

```
 h  e  l  l  o     w  o  r  l  d  —  11 bytes
68 65 6c 6c 6f 20 77 6f 72 6c 64
68 65 6c 6c 6f 20 77 6f 72 6c 64 05 05 05 05 05  —  16 bytes
                                |___padding____| 
```

Enter fullscreen mode Exit fullscreen mode

我们只是通过打印应该重复连接的字节数来填充它。(检查我做的实现:[https://github.com/halan/aes.js/blob/master/src/padding.js](https://github.com/halan/aes.js/blob/master/src/padding.js))

顺便说一下，当使用基于块的密码时，我们需要将明文分割成相同大小的块(AES 为 128 位),并选择一种操作模式来处理这些块，并使用密钥对其进行加密。正因为如此，有时最后一个块没有合适的大小通过。

在这篇文章中，我将向你展示一种叫做 CBC 的操作模式。

CBC 开始在第一个纯文本块和一个称为 IV(初始化向量)的特殊块之间进行 XOR(特殊 OR ),然后根据密钥对其进行加密，以生成第一个加密块。因此，第一个加密块用于与第二个纯文本块进行 XOR 运算，然后根据密钥对其进行加密以生成第二个加密块，依此类推……更改一个块将导致下一个块发生雪崩，因此，当确保随机且不可预测的 IV 时，即使使用相同的密钥和纯文本，结果也会完全不同。

为了解密，它会做相反的过程。首先解密第一个块，然后与 IV 进行 XOR 运算，得到第一个纯文本块。第二个纯文本块是由第二个加密块的解密与第一个加密块的异或构成的…
注意，IV 必须是不可预测的，它可以是随机的，并且不需要保密。通常情况下，它会与加密数据预先连接或存储在一起。并且 IV 的大小总是与块的长度相同。(检查我做的那个实现:[https://github . com/halan/AES . js/blob/master/src/op modes . js # L12-L24](https://github.com/halan/aes.js/blob/master/src/opModes.js#L12-L24))

# 让我们加密一些东西

最后，我们可以将这些概念结合在一起，加密/解密来自浏览器或 Node.js 的任何数据。

我们的加密系统将使用以下方案:

*   使用 CBC 模式和 256 密钥的加密 AES
*   由 PBKDF2 使用 HMAC-SHA512 哈希生成的密钥，使用 100k 个交互和 16 字节的随机 salt
*   IV 随机生成
*   最终格式:base64(salt + IV + data)
*   我刚刚从我在这里找到的 Enpass 的实际实现中复制了该模式的一部分:[https://www . en pass . io/docs/security-white paper-en pass/enpasssecuritywhitepaper . pdf](https://www.enpass.io/docs/security-whitepaper-enpass/EnpassSecurityWhitepaper.pdf)

注意，这个模式与 openssl enc 命令行不兼容，除非您直接传递从 PBKDF2 派生的原始密钥。正如我们上面讨论的，openssl enc 使用 EVP_BytesToKey 从加密数据前面的 salt 中导出密钥和 IV。

Node.js

```
const crypto = require('crypto');

salt = crypto.randomBytes(16);
iv = crypto.randomBytes(16);
key = crypto.pbkdf2Sync('my password', salt, 100000, 256/8, 'sha256');

cipher = crypto.createCipheriv('aes-256-cbc', key, iv);

cipher.write("That is our super secret text");
cipher.end()

encrypted = cipher.read();
console.log({
  iv: iv.toString('base64'),
  salt: salt.toString('base64'),
  encrypted: encrypted.toString('base64'),
  concatenned: Buffer.concat([salt, iv, encrypted]).toString('base64')
});

/*

{ iv: 'JaTFWNAEiWIPOANqW/j9kg==',
  salt: '4DkmerTT+FXzsr55zydobA==',
  encrypted: 'jE+QWbdsqYWYXRIKaUuS1q9FaGMPNJko9wOkL9pIYac=',
  concatenned:
   '4DkmerTT+FXzsr55zydobCWkxVjQBIliDzgDalv4/ZKMT5BZt2yphZhdEgppS5LWr0VoYw80mSj3A6Qv2khhpw==' }

*/ 
```

Enter fullscreen mode Exit fullscreen mode

简单易行，我们从`4DkmerTT+FXzsr55zydobCWkxVjQBIliDzgDalv4/ZKMT5BZt2yphZhdEgppS5LWr0VoYw80mSj3A6Qv2khhpw==`开始解密吧。知道这个数据是 salt + IV +加密数据:

```
const crypto = require('crypto');

encrypted = Buffer.from('4DkmerTT+FXzsr55zydobCWkxVjQBIliDzgDalv4/ZKMT5BZt2yphZhdEgppS5LWr0VoYw80mSj3A6Qv2khhpw==', 'base64');
const salt_len = iv_len = 16;

salt = encrypted.slice(0, salt_len);
iv = encrypted.slice(0+salt_len, salt_len+iv_len);
key = crypto.pbkdf2Sync('my password', salt, 100000, 256/8, 'sha256');

decipher = crypto.createDecipheriv('aes-256-cbc', key, iv);

decipher.write(encrypted.slice(salt_len+iv_len));
decipher.end();

decrypted = decipher.read();
console.log(decrypted.toString());
// That is our super secret text 
```

Enter fullscreen mode Exit fullscreen mode

这个 API 有一些问题:

*   所有数据都可以表示为缓冲区、字符串、类型化数组或数据视图。write()函数的第二个参数用于定义输入格式:utf8、hex、base64。read()的第一个参数也将用于定义输出格式。
*   end()将添加填充并加密密码的最后一个块，在此之前调用 read()将输出除最后一个块之外的所有块。final()的行为类似于 end()，但它也输出最后一个块。如果在 final()之前或之后运行 read()，它将输出除最后一个块之外的所有块。final()的第一个参数将用于定义输出格式，正如我们在 read()中看到的那样。
*   有一个 update()函数，它的作用是添加输入并返回输出。它不输出任何以前使用 write()加密的数据。但是，如果通过 update 插入的数据少于一个块，它将输出一个空缓冲区，并将该数据与下一个 update()或 final()连接。update()的第二个和第三个参数是关于输入和输出格式的。
*   加密和解密也支持通过 on()的事件。我们可以监听‘可读’和‘结束’事件。
*   所有步骤都有一个等效的异步函数(除了 write()/read()、final()/end()和 update())，查看文档以了解更多详细信息。

伪造

```
const forge = require('node-forge');

const salt = forge.random.getBytesSync(16);
const iv = forge.random.getBytesSync(16);

const key = forge.pkcs5.pbkdf2('my password', salt, 100000, 256/8, 'SHA256');

const cipher = forge.cipher.createCipher('AES-CBC', key);

cipher.start({iv: iv});
cipher.update(forge.util.createBuffer('That is our super secret text'));
cipher.finish();

const encrypted = cipher.output.bytes();

console.log({
  iv: forge.util.encode64(iv),
  salt: forge.util.encode64(salt),
  encrypted: forge.util.encode64(encrypted),
  concatenned: forge.util.encode64(salt + iv + encrypted)
});

/*

{ iv: '2f0PCR5w/8a4y/5G4SGiLA==',
  salt: 'sYoCiGLJ9xuH3qBLoBzNlA==',
  encrypted: '9LYfj1wUrkro8+a+6f6rglHlVX9qj8N4EMC8ijMjp7Q=',
  concatenned:
   'sYoCiGLJ9xuH3qBLoBzNlNn9DwkecP/GuMv+RuEhoiz0th+PXBSuSujz5r7p/quCUeVVf2qPw3gQwLyKMyOntA==' }

*/ 
```

Enter fullscreen mode Exit fullscreen mode

然后:

```
const forge = require('node-forge');

const encrypted = forge.util.binary.base64.decode('sYoCiGLJ9xuH3qBLoBzNlNn9DwkecP/GuMv+RuEhoiz0th+PXBSuSujz5r7p/quCUeVVf2qPw3gQwLyKMyOntA=='
);

const salt_len = iv_len = 16;

const salt = forge.util.createBuffer(encrypted.slice(0, salt_len));
const iv = forge.util.createBuffer(encrypted.slice(0+salt_len, salt_len+iv_len));

const key = forge.pkcs5.pbkdf2('my password', salt.bytes(), 100000, 256/8, 'SHA256');
const decipher = forge.cipher.createDecipher('AES-CBC', key);

decipher.start({iv: iv});
decipher.update(
  forge.util.createBuffer(encrypted.slice(salt_len + iv_len))
);
decipher.finish();

console.log(decipher.output.toString());
// That is our super secret text 
```

Enter fullscreen mode Exit fullscreen mode

重要注意事项:

*   pbkdf2()要求字符串作为密码和 salt。所以，如果你有一个 forge 缓冲区，你必须先调用 bytes()。
*   cipher . update()/deciper . update()需要缓冲区。

隐孢子虫〔t0〕

```
const CryptoJS = require('crypto-js');

const salt = CryptoJS.lib.WordArray.random(16);
const iv = CryptoJS.lib.WordArray.random(16);

const key = CryptoJS.PBKDF2('my password', salt, { keySize: 256/32, iterations: 10000, hasher: CryptoJS.algo.SHA256});

const encrypted = CryptoJS.AES.encrypt('That is our super secret text', key, {iv: iv}).ciphertext;

const concatenned =  CryptoJS.lib.WordArray.create().concat(salt).concat(iv).concat(encrypted)

console.log({
  iv: iv.toString(CryptoJS.enc.Base64),
  salt: salt.toString(CryptoJS.enc.Base64),
  encrypted: encrypted.toString(CryptoJS.enc.Base64),
  concatenned: concatenned.toString(CryptoJS.enc.Base64)
});

/*

{ iv: 'oMHnSEQGrr04p8vmrKU7lg==',
  salt: 'OkEt2koR5ChtmYCZ0dXmHQ==',
  encrypted: 'jAOb0LwpmaX51pv8SnTyTcWm2R14GQj0BN7tFjENliU=',
  concatenned:
   'OkEt2koR5ChtmYCZ0dXmHaDB50hEBq69OKfL5qylO5aMA5vQvCmZpfnWm/xKdPJNxabZHXgZCPQE3u0WMQ2WJQ==' }

*/ 
```

Enter fullscreen mode Exit fullscreen mode

解密:

```
const CryptoJS = require('crypto-js');

const encrypted =  CryptoJS.enc.Base64.parse('OkEt2koR5ChtmYCZ0dXmHaDB50hEBq69OKfL5qylO5aMA5vQvCmZpfnWm/xKdPJNxabZHXgZCPQE3u0WMQ2WJQ==');

const salt_len = iv_len = 16;

const salt = CryptoJS.lib.WordArray.create(
  encrypted.words.slice(0, salt_len / 4 )
);
const iv = CryptoJS.lib.WordArray.create(
  encrypted.words.slice(0 + salt_len / 4, (salt_len+iv_len) / 4 )
);

const key = CryptoJS.PBKDF2(
  'my password',
  salt,
  { keySize: 256/32, iterations: 10000, hasher: CryptoJS.algo.SHA256}
);

const decrypted = CryptoJS.AES.decrypt(
  {
    ciphertext: CryptoJS.lib.WordArray.create(
      encrypted.words.slice((salt_len + iv_len) / 4)
    )
  },
  key,
  {iv: iv}
);

console.log(decrypted.toString(CryptoJS.enc.Utf8));
// That is our super secret text 
```

Enter fullscreen mode Exit fullscreen mode

重要注意事项:

*   如果在 encrypt()上传递一个字符串作为密钥，它将进入与 OpenSSL 兼容的基于密码的模式(假设前 8 个字节是字符串“Salted__ ”,接下来的 8 个字节将是一个 salt，用于导出 IV 和密钥。该派生与 PBKDF 不兼容，并且使用 MD5 作为核心哈希函数，因此不安全！).给定一个字符串形式的密钥，encrypt()将忽略 iv sent as 选项。
*   这个界面太混乱了，我发现 Github 上的几个问题都与这个魔术有关。
*   为了解密，我们需要发送一个包含 word array(cryptojs . lib 提供的一种类型)属性密文的对象。
*   WordArray 正好是 4 字节的数字数组。我们可以通过“单词”直接访问该数组。所以，切片总是除以 4，因为每个单词的长度。

网络加密 API

```
const encoder = new TextEncoder();

const toBase64 = buffer =>
  btoa(String.fromCharCode(...new Uint8Array(buffer)));

const PBKDF2 = async (
  password, salt, iterations,
  length, hash, algorithm =  'AES-CBC') => {

  keyMaterial = await window.crypto.subtle.importKey(
    'raw',
    encoder.encode(password),
    {name: 'PBKDF2'},
    false,
    ['deriveKey']
  );

  return await window.crypto.subtle.deriveKey(
      {
        name: 'PBKDF2',
        salt: encoder.encode(salt),
        iterations,
        hash
      },
      keyMaterial,
      { name: algorithm, length },
      false, // we don't need to export our key!!!
      ['encrypt', 'decrypt']
    );
}

const salt = window.crypto.getRandomValues(new Uint8Array(16));
const iv = window.crypto.getRandomValues(new Uint8Array(16));
const plain_text = encoder.encode("That is our super secret text");
const key = await PBKDF2('my password', salt, 100000, 256, 'SHA-256');

const encrypted = await window.crypto.subtle.encrypt(
  {name: "AES-CBC", iv },
  key,
  plain_text
);

console.log({
  salt: toBase64(salt),
  iv: toBase64(iv),
  encrypted: toBase64(encrypted),
  concatennated: toBase64([
    ...salt,
    ...iv,
    ...new Uint8Array(encrypted)
  ])
});

/*

{ salt: "g9cGh/FKtMV1LhnGvii6lA==",
  iv: "Gi+RmKEzDwKoeDBHuHrjPQ==",
  encrypted: "uRl6jYcwHazrVI+omj18UEz/aWsdbKMs8GxQKAkD9Qk=",
  concatennated:

"g9cGh/FKtMV1LhnGvii6lBovkZihMw8CqHgwR7h64z25GXqNhzAdrOtUj6iaPXxQTP9pax1soyzwbFAoCQP1CQ=="}

*/ 
```

Enter fullscreen mode Exit fullscreen mode

这么脏，但是很管用。来解密一下:

```
const encoder = new TextEncoder();
const decoder = new TextDecoder();

const fromBase64 = buffer =>
  Uint8Array.from(atob(buffer), c => c.charCodeAt(0));

const PBKDF2 = async (
  password, salt, iterations,
  length, hash, algorithm =  'AES-CBC') => {

  const keyMaterial = await window.crypto.subtle.importKey(
    'raw',
    encoder.encode(password),
    {name: 'PBKDF2'},
    false,
    ['deriveKey']
  );
  return await window.crypto.subtle.deriveKey(
    {
      name: 'PBKDF2',
      salt: encoder.encode(salt),
      iterations,
      hash
    },
    keyMaterial,
    { name: algorithm, length },
    false, // we don't need to export our key!!!
    ['encrypt', 'decrypt']
  );
};

const salt_len = iv_len = 16;

const encrypted = fromBase64('g9cGh/FKtMV1LhnGvii6lBovkZihMw8CqHgwR7h64z25GXqNhzAdrOtUj6iaPXxQTP9pax1soyzwbFAoCQP1CQ==');

const salt = encrypted.slice(0, salt_len);
const iv = encrypted.slice(0+salt_len, salt_len+iv_len);
const key = await PBKDF2('my password', salt, 100000, 256, 'SHA-256');

const decrypted = await window.crypto.subtle.decrypt(
  { name: "AES-CBC", iv },
  key,
  encrypted.slice(salt_len + iv_len)
);
console.log(decoder.decode(decrypted)); 
```

Enter fullscreen mode Exit fullscreen mode

有一些注意事项:

*   importKey()、deriveKey()和 encrypt()/decrypt()是异步函数。importKey()既用于从字节中导入密钥，也用于导入与 deriveKey()一起使用的密码。
*   deriveBits()也可以用来导出一个密钥。如果你想同时导出一个 IV 和一个 key，通常会用到它，实际上你要求导出几个字节，然后从中取出一部分，在 mode raw 中植入一个 importKey，以便能够用来加密或解密某些东西。
*   deriveKey()或 importKey()的最后一个参数是允许链接的函数列表。

暂时就这样了。我希望已经介绍了足够的概念来支持如何使用 JavaScript 加密纯文本或字符串化的 JSON 对象。

# 更新

*   几乎所有的加密系统中都使用散列函数。还有一些与加密无关的用途”。第一版我写了*密码术*而不是*加密*。*密码学*是科学，*加密*是一门学科内部的密码学。当我们创建签名和哈希来确保内容的完整性时，这不是加密，但肯定是一个密码学主题。

# 荣誉称号

*   [Luan gon alves](https://github.com/LuanGB)感谢您在我写这篇文章的时候与我进行了很好的交谈，并对这篇文章进行了积极的评论。
*   [Elias Rodrigues](https://github.com/elias19r) 对代码示例进行了重要的修正

# 参考资料和有用链接

*   《密码学与网络安全:原理与实践》，威廉·斯塔林斯-[http://williamstallings.com/Cryptography/](http://williamstallings.com/Cryptography/)
*   [https://www.w3.org/TR/WebCryptoAPI/](https://www.w3.org/TR/WebCryptoAPI/)
*   [https://nodejs . org/API/crypto . html # crypt _ crypto](https://nodejs.org/api/crypto.html#crypto_crypto)
*   [https://en.wikipedia.org/wiki/PBKDF2](https://en.wikipedia.org/wiki/PBKDF2)
*   [https://github.com/halan/aes.js](https://github.com/halan/aes.js)——我的教学目的 AES 的实施
*   [https://tonyarcieri.com/whats-wrong-with-webcrypto](https://tonyarcieri.com/whats-wrong-with-webcrypto)
*   [https://www . NCC group . trust/us/about-us/news room-and-events/blog/2011/August/JavaScript-cryptography-considered-habital/](https://www.nccgroup.trust/us/about-us/newsroom-and-events/blog/2011/august/javascript-cryptography-considered-harmful/)
*   [https://tankredhase . com/2014/04/13/heart bleed-and-JavaScript-crypto/](https://tankredhase.com/2014/04/13/heartbleed-and-javascript-crypto/)
*   [https://VN hacker . blogspot . com/2014/06/why-JavaScript-crypto-is-useful . html](https://vnhacker.blogspot.com/2014/06/why-javascript-crypto-is-useful.html)
*   [http://blog.kotowicz.net/2014/07/js-crypto-goto-fail.html?m=1](http://blog.kotowicz.net/2014/07/js-crypto-goto-fail.html?m=1)
*   [https://Hal . inria . fr/Hal-01426852/document](https://hal.inria.fr/hal-01426852/document)
*   [https://www . slide share . net/Channy/the-history-and-status-of-web-crypto-API](https://www.slideshare.net/Channy/the-history-and-status-of-web-crypto-api)
*   [https://www.w3.org/wiki/NetflixWebCryptoUseCase](https://www.w3.org/wiki/NetflixWebCryptoUseCase)