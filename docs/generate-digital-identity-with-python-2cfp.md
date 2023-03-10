# 使用 Python 生成数字身份

> 原文：<https://dev.to/codesharedot/generate-digital-identity-with-python-2cfp>

您可以生成一个数字身份，这在区块链技术或 ssh 中很常见。这是针对 [Python](https://pythonprogramminglanguage.com) 语言的。

像在区块链技术中一样，“用户名”将是公钥。下面的代码生成一个公钥和私钥。

[![keys](img/5226f9de18b06aba58df063e8ae79c21.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--c2DupClF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttp%253A%252F%252Fladyjaderains.files.wordpress.com%252F2012%252F06%252Fkeys.gif%253Fw%253D584%26f%3D1%26nofb%3D1)

## 密钥生成

我们将导入模块 Crypto 并使用 RSA 算法。(不推荐 RSA，但这是个例子)。

```
#!/usr/bin/python3
from os import chmod                                                            
from Crypto.PublicKey import RSA 
```

然后，我们使用该算法生成一个公钥/私钥对:

```
key = RSA.generate(2048)                                                        
pubkey = key.publickey() 
```

最后你可以输出它们。实际上，你永远不会输出你的私钥。你最后一次向终端输出密码是什么时候？

```
print(key.exportKey('PEM'))                                                     
print(pubkey.exportKey('OpenSSH')) 
```

在 key.exportKey('PEM ')中，参数是输出格式。您可以选择这些格式:

*   DER' -二进制编码
*   PEM' -纹理编码
*   OpenSSH' -根据 OpenSSH 规范的纹理编码。

运行程序，你现在有一个数字身份。您可以与任何人共享您的公钥。

相关链接:

*   [学习 Python](https://pythonprogramminglanguage.com)
*   [加密模块](https://pythonhosted.org/pycrypto/Crypto-module.html)