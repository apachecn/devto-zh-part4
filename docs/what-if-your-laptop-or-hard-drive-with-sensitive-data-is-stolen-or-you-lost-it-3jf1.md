# 如果装有敏感数据的笔记本电脑或硬盘被盗或丢失，该怎么办？

> 原文：<https://dev.to/nikhilponnuru/what-if-your-laptop-or-hard-drive-with-sensitive-data-is-stolen-or-you-lost-it-3jf1>

网络隐私和数据安全是一个一直备受关注的领域，但是本地系统上的数据呢？

加密是实现磁盘数据保护的方法。如果您在一家企业工作，并且存储着敏感的业务信息，这尤其需要。

我们公司想要加密我们的笔记本电脑，因此我做了以下所有可用方法的调查，并简要概述了它们在内部的工作方式。

### 需要进行系统级加密:

*   在磁盘/设备意外丢失或被盗的情况下保护数据。
*   保护数据不被未经授权的人物理访问。
*   磁盘/设备在寿命结束后被丢弃。

加密确保文件总是以加密的形式存储在磁盘上。只有当系统正在运行并被可信用户解锁时，文件才以可读的形式对操作系统和应用程序可用。未经授权的人直接查看磁盘内容只会发现看起来杂乱无章的数据，而不是真正的文件。

### 加密的级别:

**用户空间**:

1.  应用层加密

**内核空间**:

1.  文件系统加密
2.  全磁盘/硬盘加密
3.  卷加密
4.  分区加密

AES 是几乎所有类型的对称分组密码加密中使用的默认加密算法。因此，以下所有加密都是基于密码短语/口令(单个密钥)进行的。

由于它使用用户输入的密码短语/口令来生成加密密钥，如果用户忘记了，事情就完了！

[![image](img/0caa541a5c5cd0e8065b0c40a39417d2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VBrKO5q5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.stack.imgur.com/KuoSQ.png)

**加密(尤其是全磁盘加密)是否会缩短硬盘或固态硬盘的寿命**？

从上图可以看出，不需要执行额外的读取或写入，因为加密模块会在数据写入盘片之前对数据进行加密，并在数据发送到执行读取的进程之前进行解密。这是因为大多数加密算法使用分组密码(基本上是使用数据块而不是一个字符来加密/解密)。

### 文件系统加密:

文件系统级加密或文件/文件夹加密是一种磁盘加密形式，其中单个文件或目录由文件系统本身加密。

实现这一点的常见方法是使用堆叠文件系统加密解决方案。这些被实现为堆叠在现有文件系统之上的层，使得写入启用加密的文件夹的所有文件在底层文件系统将它们写入磁盘之前被动态加密，并且每当文件系统从磁盘读取它们时被解密。这样，文件以加密的形式存储在主机文件系统中(这意味着它们的内容，通常还有它们的文件/文件夹名，被长度大致相同的随机数据所替换)，但除此之外，它们仍然存在于该文件系统中，就像没有加密时一样，就像普通的文件/符号链接/硬链接等。

**Home folder encryption** 是最常见的文件加密，其中 Home folder 中的所有内容都会在系统登录时(引导时)进行加密和解密。从 Ubuntu 18.04 开始，对此的支持已经停止，只有全磁盘加密才起作用。

可用的解决方案有 eCryptfs(由 Ubuntu(直到 16.04)和 chrome OS 使用)和 EncFS。

**缺点**:

*   无法加密交换分区、/tmp、/var 等。可能存储敏感用户数据的系统目录。
*   文件元数据(文件数量、目录结构、文件大小、权限等。)未加密，但文件和文件夹的名称是加密的。

**阅读更多:**

其实现方式是，为了解锁在主机文件系统(“下层目录”)中存储原始加密文件的文件夹，将其安装(使用特殊的堆叠伪文件系统)到其自身上，或者可选地安装到不同的位置(“上层目录”)，其中相同的文件以可读的形式出现，直到其再次被卸载，或者系统被关闭。

### 全磁盘加密:

全磁盘加密是对磁盘上的每一位数据进行加密的过程。磁盘上的所有东西，包括操作系统，都是加密的。MBR(负责加载操作系统的主引导记录)没有加密。一些基于硬件的全磁盘加密系统可以真正加密整个引导磁盘，包括 MBR。

在文件系统层之下运行。不关心加密块设备的内容是文件系统、分区表、LVM(用于管理分区的逻辑卷管理器)设置，还是其他任何东西。

可用的解决方案有:Loop-AES、dm-crypt +/- LUKS、VeraCrypt。Ubuntu(事实上大多数 Linux 系统)默认使用 dm-crypt+LUKS。所有这些都是块设备加密。

**优点**:

*   可以在操作系统安装期间完成。
*   包括交换空间和临时文件在内的所有内容都被加密，确保没有机密数据因疏忽而未受保护。
*   对哪些文件进行加密的决定不由用户决定。
*   安装键盘记录器或其他特洛伊木马变得很困难，与全磁盘加密，甚至与全系统物理访问。
*   直接的数据销毁，例如简单地销毁密钥(加密粉碎)，会使包含的数据变得无用。尽管物理毁灭是一个随时可用的选择。

**缺点**:

*   攻击者可以在我们解锁并安装磁盘的加密部分后，在磁盘运行时，通过互联网或其他方式闯入系统。(冷启动攻击也是可能的)
*   政府实体可以简单地强迫个人放弃密钥/密码短语。
*   安装操作系统后无法完成。仅在操作系统安装过程中需要启用/设置。
*   没有合适的图形用户界面(除了使用 Veracrypt，这是一个用于全磁盘加密的第三方软件。)

针对上述缺点的一个解决方案可以是使用**基于硬件的全磁盘加密**(尤其是针对上述第一个缺点)，它消除了作为潜在攻击媒介的计算机内存。

#### 加密的情况下如何在一个系统中容纳多个用户？

应该共享密码/密钥。

#### 如果操作系统崩溃或其他东西被破坏，如何检索硬盘上的加密数据？

对重要数据进行定期备份是简单且值得推荐的方法。

#### 用户忘记通行短语/密码怎么办？

故事结束。尽管我们可以使用恢复机制，但这并不能真正达到加密的目的。

#### 如何更改通行短语/密码？

对于全磁盘加密:
使用名为 [cryptsetup](https://gitlab.com/cryptsetup/cryptsetup) 的实用程序

对于文件/文件夹加密:
ecryptfs-mount-private 命令(如果使用 ecryptfs 加密，这是 until 16.04 之前的常用方式)

#### 如何选择安全的通行短语/密码？

看看这些文章吧，它们很好地揭示了如何选择一个。

1.  [https://www . schneier . com/blog/archives/2014/03/choosing _ secure _ 1 . html](https://www.schneier.com/blog/archives/2014/03/choosing_secure_1.html)
2.  [https://www.iusmentis.com/security/passphrasefaq/](https://www.iusmentis.com/security/passphrasefaq/)
3.  [https://en.wikipedia.org/wiki/Password_strength](https://en.wikipedia.org/wiki/Password_strength)

#### 在哪里存储密钥(密码短语/口令)？

1.  密钥管理软件。
2.  密码管理员。
3.  肌肉记忆。
4.  HSM 或其他硬件密钥管理设备，提供最高级别的物理安全性。

#### 我们输入的 passphrase/ password 是加密整个数据的密钥吗？

不。首先，由于这是对称加密，有一个主密钥可以解密所有内容。主密钥在加密期间生成，并以加密形式与加密数据一起存储在磁盘上(是的，与数据并排)。

密钥的熵(随机性)对于加密的安全性至关重要。随机生成的 32 字节(256 位)的字节串具有期望的属性。但是你能记住 32 个字母的密码短语吗？这就是为什么密码短语不是主密钥。

输入密码后会发生以下情况:

步骤 1:用户在装载时输入的密码

步骤 2:密钥派生函数被应用于这个挂载密码短语(例如 PBKDF2 也被 Django 和 salt 一起使用),它生成一个挂载密钥。

步骤 3:上述安装密钥用于解密在文件系统/全磁盘加密过程中生成的加密主密钥。

步骤 4:这个解密的主密钥现在解密所有内容。

这是一个缓慢的过程，但因为它只发生在挂载时，所以不会有太大影响。

**关于使用 eCryptfs 或 encFs 进行文件系统加密的有趣事实**:

每个文件都用一个唯一的、随机生成的名为“fek”的密钥进行加密(该密钥存储在文件头中，并用挂载密码进行包装)。这意味着二进制等价的两个明文文件加密成两个完全不同的密文。