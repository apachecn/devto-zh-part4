# 设置我的个人邮件服务器

> 原文：<https://dev.to/icyphox/setting-up-my-personal-mailserver-3o9k>

邮件服务器是很久以后才出现的。大约 4 年前，我曾试图建立一个域名系统，IIRC，当它涉及到域名系统时，我退出了。这次我也差点成功了。 <sup id="fnref-1">[1](#fn-1)</sup>

对于这次尝试，我想要一个更简单的方法。我记得 Dovecot & Postfix 的配置是多么令人困惑，因此我决定寻找一个容器化的解决方案，最重要的是，它可以在我便宜的 5 美元的 Digital Ocean VPS 上运行——1 个 vCPU 和 1 GB 内存。其中实际上只有大约 500 MB 可用。所以是的，*相当的*紧。

### 有什么

事实证明，有很多这样的 OOTB，准备提供解决方案。这些是我遇到的:

*   [poste.io](https://poste.io) :基于“开放核心”模式。基本安装是开源和免费的(就像 beer 一样)，但是你必须为额外的东西付费。

*   [mailu.io](https://mailu.io) :自由软件。从 poste.io 汲取灵感，但附带了一个我不需要的 web UI。

*   mailcow.email :这些花哨的域名越来越可笑了。但更重要的是，他们需要 2g 内存*加上*交换空间？！没有。

*   [邮箱](https://mailinabox.email):与上面的不同，它不是基于 Docker 的解决方案，但绝对值得一提。然而，它需要一个新的盒子来工作。一个什么都没有的盒子。我不能那样做。

*   [docker-mailserver](https://github.com/tomav/docker-mailserver/) : **胜者**。

### 所以……`docker-mailserver`

自述文件中首先引起我注意的是:

> 推荐:
> 
> *   1 个 CPU
> *   1GB 内存
> 
> 最低:
> 
> *   1 个 CPU
> *   512MB RAM

太棒了，我可以设法把这个压缩到我现有的 VPS 中。设置相当简单&文档非常好。它采用单个`.env`文件进行配置，这很棒。然而，我确实遇到了一些小问题。

一个特别糟糕的例子是`docker` / `docker-compose`内存不足。

```
Error response from daemon: cannot stop container: 2377e5c0b456: Cannot kill container 2377e5c0b456226ecaa66a5ac18071fc5885b8a9912feeefb07593638b9a40d1: OCI runtime state failed: runc did not terminate sucessfully: fatal error: runtime: out of memory 
```

但经过几次尝试后，它终于成功了。

接下来我要纠结的是——DNS。具体来说，就是生成 DKIM 密钥的步骤 <sup id="fnref-2">[2](#fn-2)</sup> 。

`config/opendkim/keys/domain.tld/mail.txt`

下的输出并不完全是 CloudFlare 友好的；它们不能直接复制粘贴到`TXT`记录中。

这是它看起来的样子。

```
mail._domainkey IN TXT ( "v=DKIM1; h=sha256; k=rsa; "
      "p=<key>"
      "<more key>" ) ; ----- DKIM key mail for icyphox.sh 
```

但是在配置记录时，您将“类型”设置为`TXT`，将“名称”设置为`mail._domainkey`，将“值”设置为括号内的内容`( )`，将*去掉*引号`""`。也删除部分似乎是一个评论`; ----- ...`。

为了简化以后对 DNS 问题的调试，使用类似于`mail.domain.tld`的子域和`A`记录指向您的邮件服务器可能是个好主意。然后，您必须设置一个`MX`记录，其“名称”为`@`(或者您的 DNS 提供商用来表示根域的任何名称)，其“值”为`mail.domain.tld`。最后是`PTR`(我想是指针记录)，它与`A`记录相反——“Name”是服务器 IP，“Value”是`mail.domain.tld`。当我发出的电子邮件不断被 Tutanota 的服务器拒绝时，我才知道这一点。

另一个障碍是 SSL/TLS 证书。这没有被很好地记录下来，除非你通读了维基并看了一个例子。简而言之，安装`certbot`，释放端口 80，运行

```
$ certbot certonly --standalone -d mail.domain.tld 
```

一旦完成，编辑`docker-compose.yml`文件以在容器中挂载`/etc/letsencrypt`，类似于

```
...

volumes:
    - maildata:/var/mail
    - mailstate:/var/mail-state
    - ./config/:/tmp/docker-mailserver/
    - /etc/letsencrypt:/etc/letsencrypt

... 
```

这样做后，您就不会让邮件客户端抱怨证书不可靠，您必须手动添加一个例外。

### 你为什么会…？

这有几个很好的理由:

#### 隐私

不，真的，这是真正私人电子邮件的最佳选择。不是 ProtonMail，不是 Tutanota。当然，他们是这么说的，我对此没有异议。引用德鲁·德沃<sup id="fnref-3">T3T3</sup>，

> 真正安全的系统不需要您信任服务提供商。

但是你必须*信任* ProtonMail。他们运行开源软件，但是你怎么能确定这不是它的后门版本呢？

当你拥有自己的邮件服务器时，你就真正拥有了自己的邮件，而不必依赖任何第三方。这不是传播 FUD 教的企图。最终，这完全取决于您的威胁模型。

#### 放权

如今的电子邮件基本上是由谷歌运营的。Gmail 拥有超过 12 亿活跃用户。那是淫秽的。电子邮件的设计是去中心化的，但是大公司突然介入，把它变成了一种产品。他们现在控制了你的数据，谷歌阅读你的邮件也不是不知道。这又回到了我之前的观点，隐私。去中心化保证隐私。当你控制了你的邮件，你就控制了谁会阅读它。

#### 个性化

不能忽略这一条。拥有一个自定义的电子邮件地址很酷。

`x@icyphox.sh`对`gabe.newell4321@gmail.com`

呸，这不是比赛。

* * *

我沮丧的推文。↩

[链接](https://github.com/tomav/docker-mailserver#generate-dkim-keys)进入文档。↩

从他的[文章](https://drewdevault.com/2018/08/08/Signal.html)上他为什么不信任 Signal。↩