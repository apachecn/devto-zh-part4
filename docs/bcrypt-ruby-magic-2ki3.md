# BCrypt == Ruby 'magic'??

> 原文：<https://dev.to/caffiendkitten/bcrypt-ruby-magic-2ki3>

密码每天都被用来访问我们的银行，检查电子邮件，使用社交媒体，玩游戏，甚至只是通过视频如 Zoom 或 Skype 进行交流。在这些平台上，他们使用(或者应该使用)一个函数来“散列”密码，以确保它是安全的，并且只有用户能够访问他们自己的数据。BCrypt 是 Ruby on Rails 应用程序中最常用的方法之一，它对密码进行“加盐”和“哈希”处理，这样当密码被保存时，如果不道德地访问，就不容易以纯文本的形式读取密码，并且有助于防止 rainbow 攻击。
[![](img/b2003c76042b22fb90f839286b0fc3c1.png)](https://i.giphy.com/media/iH4GrBoiLY6zu/giphy-downsized-large.gif)

## BCrypt 从何而来？

BCrypt 创建于 20 世纪 90 年代，由 OpenBSD 项目设计，基于 Blowfish 密码(一种分组密码)。BCrypt 是一种复杂而安全的哈希算法，用于哈希 C、C++、C#、Go、Java、JavaScript、Elixir、Perl、PHP、Python、Ruby 和 Node.js 等语言中的密码。(1)
特别是在 Ruby 中，“bcrypt Ruby gem 为安全处理密码提供了一个简单的包装器。”②
[![](img/1cf7b8fdc163b3bcd1a9eee1dfb26b19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ffoCjZ4C--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/e375fxb6f7nhucry17wf.png)

## 什么是盐？

数据加盐是在哈希算法的输入中添加一些额外的随机数据的过程，以保证唯一的输出，“在不增加用户要求的情况下增加其复杂性，并减轻彩虹表等密码攻击。”③
[![](img/d2d8494cad0eea3663051ae8ace51798.png)](https://i.giphy.com/media/7f2iTqiznYKvm/giphy.gif)

#### 你说彩虹表攻击？？？

彩虹表攻击是一种攻击方法，黑客试图使用彩虹表来破解存储在数据库系统中的密码，彩虹表是一种预先计算的查找表，用于存储密码散列。④
[![](img/5ec06f4a497642087275171eab2e5b85.png)](https://i.giphy.com/media/BSx6mzbW1ew7K/giphy.gif)

| 名字 | 带冲突的简单哈希 |
| --- | --- |
| 爱丽丝 | **4420d 1918 bbcf 7686 defdf 9560 bb 5087d 20076 de 5f 77 b 7 CB 4c 3b 40 BF 46 EC 428 b** |
| 詹森 | 695 ddccd 984217 fe8d 79858 DC 485 b 67d 66489145 AFA 78 E8 b 27 c 1451 b 27 cc 7a 2 b |
| 马里奥 | CD 5 CB 49 b 8 b 62 FB 8 DCA 38 ff 2503798 aee 71 bfb 87 b 0 ce 3210 cf 0 acac 43 a3 f 2883 c |
| 女子名 | 73 FB 51 a 0c 9 be 7d 988355706 b 18374 e 775 b 18707 A8 a 03 f 7 a 61198 eefc 64 b 409 e 8 |
| 鲍勃（人名） | **4420d 1918 bbcf 7686 defdf 9560 bb 5087d 20076 de 5f 77 b 7 CB 4c 3b 40 BF 46 EC 428 b** |
| 麦克风 | 77b 177 de 23 f 81d 37 b5 b 4495046 b 227 befa 4546 db 63 cfe 6 Fe 541 fc 4c 3c d 216 EB 9 |

## Bcrypt 如何帮助防止彩虹攻击？

Bcrypt 有助于防止这种类型的攻击，方法是
1)降低散列数据的计算速度，从而降低或完全阻止攻击者执行这种类型攻击的能力。
2)允许每个散列拥有自己的随机 salt 与数据进行散列，从而创建真正唯一的输出。
3)允许开发者控制 Bcrypt 的成本或工作。这可以让他们足够快以至于用户不会注意到，但又足够慢以至于攻击者可以猜测散列的所有可能输出。

## 什么是‘哈希’函数？

哈希函数只是一个函数，它接受输入值并创建一个确定输入值的输出值。它被设计成充当“单向函数”。单向函数是一种数学运算，很容易执行，但很难，而且通常不可能逆转。
[![](img/11a4a2a51fc423fff8c1764bad937049.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--sCLZzKb_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://learncryptography.com/assets/content/latex/SGFzaChwYXNzd29yZCArIHNhbHQp.png)

* * *

对我来说，真正有趣的部分是可以散列的次数和方式...下面是一些有趣的散列数据的方法/组合。

| 通用哈希类型: |
| --- |
| MD5/MD5(＄通过。$salt) /md5($salt。$pass) / md5(utf16le($pass)。$salt) / md5($salt.utf16le($pass)) |
| HMAC-MD5(密钥= $pass) / HMAC-MD5(密钥= $salt) |
| SHA1/sha1(＄通过。$盐)/sha1($盐。$pass) / sha1(utf16le($pass)。$ salt)/sha1($ salt . ut F16 le($ pass)) |
| HMAC-SHA1(key = $ pass)/HMAC-SHA1(key = $ salt) |
| MySQL323 / MySQL4.1/MySQL5 |
| phpass，WordPress (MD5)，Joomla (MD5) |
| phpass，phpBB3 (MD5) |
| MD5 加密，MD5 (Unix)，Cisco-IOS $1$ (MD5) 2 |
| 杜松 IVE |
| 黑色 2b-512 |
| MD4 |
| NTLM |
| 域缓存凭据(DCC)，MS 缓存 |
| 沙-224 |
| SHA-256/sha 256(＄pass。$盐)/sha 256($盐。$pass) / sha256(utf16le($pass)。$ salt)/sha 256($ salt . ut F16 le($ pass)) |
| HMAC-sha 256(key = $ pass)/HMAC-sha 256(key = $ salt) |
| 解密、DES (Unix)、传统 DES |
| Apache $apr1$ MD5，md5apr1，MD5 (APR) 2 |
| SHA-512/sha 512(＄pass。$盐)/sha 512($盐。$pass) / sha512(utf16le($pass)。$ salt)/sha 512($ salt . ut F16 le($ pass)) |
| HMAC-sha 512(key = $ pass)/HMAC-sha 512(key = $ salt) |
| SHA512 加密$6$，SHA512 (Unix) 2 |
| 域缓存凭据 2 (DCC2)，MS 缓存 2 |
| Cisco-PIX MD5 / Cisco-ASA MD5 |
| wpa/wpa 2.1 |
| WPA/WPA2 PMK 14 |
| md5(md5($pass)) |
| 长韵律 |
| Oracle H: Type (Oracle 7+)，DES(Oracle) |
| **bcrypt $2*$，Blowfish (Unix)** |
| MD5($ salt . MD5($ pass))/MD5($ salt。$通过。$ salt)/MD5(MD5($ pass). MD5($ salt))/MD5($ salt . MD5($ salt)。$ pass))/MD5(strto upper(MD5($ pass))) |
| md5(sha1($pass)) |
| sha1(sha1($pass)) |
| sha1(＄salt . sha1(＄pass)) |
| sha1(md5($pass)) |

## BCrypt 哈希什么？

与任何 salted hash 一样，BCrypt 生成一个 salt(一些随机的固定字节值),并在 hash 函数为每个输入创建唯一的 hash 之前将其与密码相结合。例如，如果两个用户有相同的密码，他们就不会有相同的密码散列，因为随机盐会使每个输入不同。

具体来说，BCrypt 算法是对文本至少加密 64 次的结果。在 BCrypt 中，通常的 Blowfish 键设置函数被替换为一个昂贵的键设置(EksBlowfishSetup)函数。(BCrypt 算法在很大程度上依赖于它的“Eksblowfish”密钥设置算法。)

```
Function bcrypt
   Input:
      cost:     Number (4..31)                      num of iterations. e.g. 12 ==> 212 = 4,096 iterations
      salt:     array of Bytes (16 bytes)           random salt
      password: array of Bytes (1..72 bytes)        UTF-8 encoded password

   Output: 
      hash:     array of Bytes (24 bytes)           This will be a fixed length output 
```

Enter fullscreen mode Exit fullscreen mode

“从 3.0.0 bcrypt 开始，kdf 现在提供了一个 KDF 函数，它执行 bcrypt_pbkdf。这个 KDF 用于 OpenSSH 更新的加密私钥格式。

```
>>> import bcrypt
>>> key = bcrypt.kdf(
...     password=b'password',
...     salt=b'salt',
...     desired_key_bytes=32,
...     rounds=100) 
```

Enter fullscreen mode Exit fullscreen mode

可调工作因子
bcrypt 的特性之一是可调对数工作因子。要调整工作系数，只需将所需的轮数传递给 bcrypt.gensalt(轮数=12)，默认为 12):

```
>>> import bcrypt
>>> password = b"super secret password"
>>> # Hash a password for the first time, with a certain number of rounds
>>> hashed = bcrypt.hashpw(password, bcrypt.gensalt(14))
>>> # Check that a unhashed password matches one that has previously been
>>> #   hashed
>>> if bcrypt.checkpw(password, hashed):
...     print("It Matches!")
... else:
...     print("It Does not Match :(") 
```

Enter fullscreen mode Exit fullscreen mode

" (6)

## BCrypt 安全吗？

虽然攻击者可以知道明文、成本和散列中的盐，但 Blowfish 是一个现代加密系统，它是专门为防止“已知明文攻击”或“彩虹表攻击”而设计的。这意味着任何想要攻击的人都无法从明文及其对应的密文中导出密钥。他们唯一的机会是尝试加密每一个可能的密码以获得相同的结果，或者进行暴力攻击(尝试任何方法)，这两种方法都是“计算上不可行的”。

##### 计算可行性:

给定一个计算问题，上下文需求对什么构成可接受的解决方案和可接受的计算成本施加某些限制(8)

## 外卖...

作为一名开发人员，如果不需要，就不要存储密码(或其他敏感数据),如果确实需要存储，对数据进行适当的加盐和哈希处理，而不仅仅是以纯文本形式存储，这是一个很好的做法。

作为用户，最好的做法是在每个网站使用不同的密码，使用密码管理器，使用安全的密码，和/或不要注册这么多可能不安全的网站，这可能会暴露您的密码或个人信息。

看看你的密码是否安全，或者是时候在这里申请一个新的了。

以下是我在 2007 年使用的一个旧密码的例子...哦，大约 2010 年。很明显，这件事已经传开了，要么是我，要么是其他也有这种想法的人。为了避免这种情况，最好设置安全的密码，并经常更改密码，以确保它们不会在互联网上被人使用。
[![](img/9ae26d944925f69c8c6cae1731661102.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EZrbc8Vr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/527afgsi6o8375qsro38.png)

[![](img/89931867f0348071d73f279ed471e541.png)](https://i.giphy.com/media/oNFOEInPqJvbYPj8MQ/giphy.gif)

### 资源

1.  [https://en.wikipedia.org/wiki/Bcrypt](https://en.wikipedia.org/wiki/Bcrypt)
2.  [https://rubygems.org/gems/bcrypt/versions/3.1.12](https://rubygems.org/gems/bcrypt/versions/3.1.12)
3.  [https://auth 0 . com/blog/add-salt-to-hashing-a-better-way-to-store-passwords/](https://auth0.com/blog/adding-salt-to-hashing-a-better-way-to-store-passwords/)
4.  [https://www . techopedia . com/definition/30617/rainbow-table-attack-cryptography](https://www.techopedia.com/definition/30617/rainbow-table-attack-cryptography)
5.  [https://learn cryptography . com/hash-functions/password-salting](https://learncryptography.com/hash-functions/password-salting)
6.  [https://haveibeenpwned.com/Passwords](https://haveibeenpwned.com/Passwords)
7.  [https://github.com/pyca/bcrypt/](https://github.com/pyca/bcrypt/)
8.  [http://phenomenologica . com/index . PHP/blog/40-posts/37-什么是可行的-计算](http://phenomenologica.com/index.php/blog/40-posts/37-what-is-feasible-computation)
9.  [https://auth 0 . com/blog/hashing-in-action-understanding-bcrypt/# What-is-code-bcrypt-code-](https://auth0.com/blog/hashing-in-action-understanding-bcrypt/#What-is--code-bcrypt--code--)

###### 请注意，我仍在学习中，如果我说的不正确，请告诉我。我很想了解更多我可能不完全了解的东西。