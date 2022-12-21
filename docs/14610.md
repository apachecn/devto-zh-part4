# 如何安全地存储密码

> 原文：<https://dev.to/luispa/how-to-safely-store-a-password-5fao>

原贴[此处](https://codahale.com/how-to-safely-store-a-password/)

# TL；博士；医生

考虑使用 bcrypt

Use [bcrypt](https://www.npmjs.com/package/bcrypt) . Use [bcrypt](http://www.usenix.org/events/usenix99/provos.html) . Use [bcrypt](https://github.com/codahale/bcrypt-ruby) . Use [bcrypt](http://pypi.python.org/pypi/py-bcrypt/) . Use [bcrypt](http://derekslager.com/blog/posts/2007/10/bcrypt-dotnet-strong-password-hashing-for-dotnet-and-mono.ashx) . Use [bcrypt](http://p3rl.org/Authen::Passphrase::BlowfishCrypt) . Use [bcrypt](http://www.mindrot.org/projects/jBCrypt/) . Use [bcrypt](http://www.openwall.com/crypt/) . Use [bcrypt](http://www.openwall.com/phpass/) . Use [bcrypt](https://github.com/smarkets/erlang-bcrypt) .

## 为什么不是{MD5，SHA1，SHA2，SHA3 等}？

这些都是通用哈希函数，旨在尽可能短的时间内计算大量数据的摘要。这意味着它们在确保数据完整性方面非常出色，而在存储密码方面则完全是垃圾。

一台现代服务器每秒可以计算出大约 [330MB 的 MD5 哈希](http://www.cryptopp.com/benchmarks-amd64.html)。如果您的用户有小写字母、字母数字和 6 个字符长的密码，您可以在大约 40 秒内尝试所有可能的密码。

这还不包括任何投资。

如果你愿意花大约 2000 美元和一两周的时间来学习 CUDA，你可以组装自己的小型超级计算机集群，这将让你[每秒尝试大约 7 亿个密码](http://www.win.tue.nl/cccc/sha-1-challenge.html)。以这样的速度，你将会以超过每秒一个 T4 的速度破解那些密码。

# 盐对你没有帮助

需要注意的是**salt 对于防止字典攻击或者暴力攻击**是没有用的。你可以使用巨大的盐或多种盐，或者手工收获的、荫蔽生长的、有机的[喜马拉雅粉盐](http://en.wikipedia.org/wiki/Himalayan_salt)。给定数据库中的散列和 salt，它不会影响攻击者尝试候选密码的速度。

不管含不含盐，如果你使用的是为速度而设计的通用散列函数，那你就真的很好受了。

# bcrypt 解决了这些问题

怎么会？基本上是慢的要命。它使用了 Blowfish 加密算法的键控时间表的变体，并引入了一个工作因子，允许您确定哈希函数的开销。正因为如此，bcrypt 才能跟上摩尔定律。随着计算机越来越快，你可以增加工作因子，散列将变得更慢。

bcrypt 比 MD5 慢多少？取决于工作因素。使用工作因子 12，bcrypt 在我的笔记本电脑上大约 0.3 秒内散列密码`yaaa`。另一方面，MD5 需要不到一微秒的时间。

所以我们说的是 **5 个数量级左右**。我不再每 40 秒破解一次密码，而是每 12 年左右**破解一次。您的密码可能不需要这种安全性，您可能需要更快的比较算法，但是 bcrypt 允许您选择速度和安全性之间的平衡。使用它。**

# 吞并

`Isn’t bcrypt just Blowfish? Where do you store the password?`

你可以阅读普罗沃斯& Mazières 的论文。bcrypt 是一种自适应密码哈希算法，它使用 Blowfish 密钥计划，而不是对称加密算法。

`You said salts aren’t helpful, but what about rainbow tables? Why would you suggest people not use salts?`

正如 Provos & Mazières 的论文所描述的，bcrypt 内置了盐来防止彩虹表攻击。所以我不是说盐是没有目的的，我是说它们不能防止字典或蛮力攻击(它们不能)。

尽管彩虹表最近作为博客帖子的主题而广受欢迎，但它并没有优雅地老化。密码破解程序的 CUDA/OpenCL 实现可以利用 GPU 中可用的大量并行性，峰值为每秒[十亿个候选密码](http://www.golubev.com/hashgpu.htm)。您可以在不到 2 秒的时间内测试所有≤7 个字符的小写字母密码。你现在可以租用硬件，这使得这成为可能，以不到 3 美元/小时的价格[。大约每小时 300 美元，你可以在一秒钟内破解大约 500，000，000，000 个候选密码。](http://aws.amazon.com/ec2/#pricing)

鉴于加密攻击经济学的这种巨大转变，任何人都没有理由浪费太多的磁盘空间来希望他们的受害者没有使用 salt。破解密码要容易得多。即使是 **SHA2 256 (SALT || PASSWORD)** 的“好”哈希方案也仍然完全容易受到这些廉价而有效的攻击，由此可见 bcrypt 这样的自适应哈希算法的重要性。