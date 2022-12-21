# 加密煮错了，或者为什么不是所有的顶级搜索结果宝石同样有帮助

> 原文：<https://dev.to/arturmartsinkovskyi/encryption-cooked-wrong-or-why-not-all-top-search-result-gems-are-equally-aiding-18b1>

# 免责声明

我不怪 aes gem creator 有意识的做错什么。这是 8 年前写的，它从来没有获得足够的吸引力，以至于没有人知道这样的宝石应该是什么样子，只是碰巧他的宝石在谷歌的“aes gem”搜索中排名第一。虽然，这种宝石有一些痛点，在现代根本不应该用于生产。这就是原因。

# TL；速度三角形定位法(dead reckoning)

不要在谷歌搜索中使用 aes gem 作为您的首选，它不能与 ruby >= 2.4 一起正常工作，也不能验证您的密钥。另外要记住，Ruby 2.4 forward openssl gem 并不剪切 key 和 iv 值，所以任何大于 16/32/64/whatever_size 字节的 key/iv 都会以`ArgumentError(#{iv or key} must be #{n} bytes)`失败。在将它传递给 openssl 或使用它的库之前，用适当的大小剪切/生成它。

# 说来话长

我们是卢布主义者。我们都知道并热爱宝石。Gems 是我们社区的基础，它们很容易被整合到我们闪亮的新项目中，这是 Ruby 受欢迎程度和原型开发速度急剧增长的主要原因之一，这使得我们的语言成为创业之王。宝石是与每个人分享我们共同的知识和经验，让别人比以前更快乐的方法。唉，并不是所有的宝石都同样成熟和有用。他们中的一些人可能会背信弃义。

# 加密是一个有趣的混淆词

不久前，我在我的一个项目中发现了一个有趣的依赖项。Gem [aes](https://github.com/chicks/aes) 被用于对称加密，它看起来很好，直到我意识到(在[一些媒体文章](https://blog.elpassion.com/simple-and-terrifying-encryption-story-c1f1d6707c07)的帮助下)对于一个依赖 Gem 创造者智慧的开发者来说，这有点疯狂。我不骗你，宝石允许你这样做:

```
encrypted = AES.encrypt("Sic transit gloria mundi.", "password", iv: SecureRandom.hex(22))
#  => "1c3c084a8f2384689aa2c440b8d24a3129104fb2dfef$HQVAxvfq1405HV0poYwC3zdEebWC05qhUlMkPbG+kLw="
decrypted = AES.decrypt(encrypted, "password")
# => "Sic transit gloria mundi."
# Seems fine.
2.3.3 :009 > decrypted = AES.decrypt(encrypted, "gassword")
# => "Sic transit gloria mundi."
# Not fine.
2.3.3 :010 > decrypted = AES.decrypt(encrypted, "gassworp")
# => "Sic transit gloria mundi."
# WTF, does it even encrypt? 
```

魔鬼就在宝石的这个细节:

```
 # Create a new cipher using the cipher type specified
      def _setup(action)
        @cipher ||= OpenSSL::Cipher::Cipher.new(@options[:cipher])
        # Toggles encryption mode
        @cipher.send(action)
        @cipher.padding = @options[:padding]
        @cipher.key = @key.unpack('a2'*32).map{|x| x.hex}.pack('c'*32)
      end 
```

此方法的最后一行[将任何字符串作为十六进制字符串解包](https://apidock.com/ruby/String/unpack),即使它没有声明它是否实际上是一个。它生产什么？任何没有有效的两个字母的十六进制序列的字符串都会以同样的方式结束，所以我们锁定的加密数据将会以一个相当大的可能的密钥集结束，这些密钥匹配并解密该字符串。

我知道，我知道你不应该使用非十六进制值作为 AES 的密钥，但是库会告诉我我错了。它当然不应该这样做，没有眨眼，只是让我失败我的加密技术，就像我用 rot13，而不是美国政府批准的 AES。这与我们对加密技术的追求完全相反——透明和快速失效策略，所以我们最终陷入了难以察觉的不安全的混乱中。记得检查你的密钥是否是有效的十六进制，因为你的加密可能比你想象的要糟糕。

# 更新-修复-重复

其次，这个库不能在 Ruby 2.4 和更新的版本中正常工作。我说的不能正常工作是指你的旧代码在从 2.3 升级到 Ruby 后会失败而没有任何改变，你需要打补丁才能让它工作。为什么？因为它依赖于 OpenSSL 并且 OpenSSL 在 2016 年做了[这个](https://github.com/ruby/ruby/commit/ce635262f53b760284d56bb1027baebaaec175d1)(他们那年 aes gem master 分支最后一次更新)。它不再截断太长的 iv/key 值，所以在 Ruby 更新之前工作正常的代码开始失败，因为它没有有效的 key/iv 长度，而且没有人费心去修复它，直到发生了重大变化。

你可能会说，你只需将截断的 key/iv 传递给库，就可以使用 gem 了...除非你想正确地解密那些你过去用错误的长度 iv 加密的讨厌的字符串。aes 的 gem 并没有给你一个普通的加密字符串，它使用$符号作为分隔符，在它的右边加上 iv。我不会说这完全是一件坏事，毕竟 iv 应该是公共的，但它会影响您未来的选择，将您绑定到加密数据的 gem 格式，这将需要对您所有的加密字符串进行转换。

要修复由于加密时 iv 长度不匹配而导致的故障，您还需要在将 iv 提供给库之前对其进行预处理、分割和截断。它工作得很好，直到下一个突破性的变化，你已经把自己与你的加密数据绑定到遗留 gem，有几个漏洞，3 年没有更新。恭喜你，你演了你自己。

# 怎么办？

如果 gem 基本上只有 160 行那么长，为什么要在更新 Ruby 之后打补丁让它工作呢？还不如直接看 [OpenSSL::Cipher](https://ruby-doc.org/stdlib-2.0.0/libdoc/openssl/rdoc/OpenSSL/Cipher.html) 自己用 AES。文档是有益的，并警告你可能的警告，整个加密过程都用代码解释，所以不要使用搜索中最先出现的 gem，保持冷静，与标准库保持更远的距离，与 gem 不同，标准库将在维护者和更新中停留更长的时间。如果您还没有将 aes gem 集成到您的应用程序中，这是可行的。如果你做了，那么，好吧，保护风险和计划迁移，伙计。

# 防患于未然

宝石是有用的，它们极大地加快了我们的发展速度。然而，它们只是由其他开发人员编写的代码片段。代码是开源的这一事实并不会使它比你的更好(除非它是一个拥有来自全世界的 1000 名明星和贡献者的大库，在这种情况下，hivemind 将代码打磨得相当好)。你不应该盲目地相信你在 Rubygems 上找到的每一段代码，尤其是当它与安全相关的时候。阅读 gem 代码和文档，检查其受欢迎程度、常见问题和 PRs。这样，您就可以确保应用程序中的宝石是真正的宝石，而不是一块恶意的玻璃。aes gem 并没有那么糟糕，但它仍然可能会带来麻烦，即使它只是 OpenSSL 上的一个薄层，您可以在不到一个小时的时间内自己编写。明智地选择。祝你好运，和露比玩得开心，朋友。