# 和 IPFS 一起分散我的网站

> 原文：<https://dev.to/hacdias/decentralizing-my-website-with-ipfs-2073>

大约两年前，也就是 2017 年，我通过[大卫·迪亚斯](http://daviddias.me)认识了 IPFS。这不是我第一次在我的网站上谈论 IPFS，但这里有一个简短的描述:IPFS 代表星际文件系统，它是一个点对点，内容寻址和分散的协议。

通过分散我的网站，我希望你能从最近的来源获取它。如果你通过 HTTP 访问这个，那可能不是真正发生的事情(*有点*，稍后会解释)。但是如果你已经知道 IPFS，并且正在使用 [IPFS 同伴](https://github.com/ipfs-shipyard/ipfs-companion#install)，变化可能是你正在通过一个分散的网络加载这个。

首先，我使用了一个名为 Hugo 的静态网站生成器，它做得非常好，我对它的创建者和维护者表示支持。然而，我们不是在这里谈论静态网站生成器，而是关于我的网站如何工作，以及你如何对你的网站做同样的事情。

将我的网站更新到星际网络只需要两个简单的命令。实际上，我只需要将它推送到 git 存储库，因为我已经用所需的命令设置了[circle ci](https://github.com/hacdias/hacdias.com/blob/master/.circleci/config.yml)。无论如何，这里是命令:

```
hugo --gc --minify
npx ipfs-deploy public -p pinata -d cloudflare 
```

简单吧？让我们稍微分析一下，看看这里发生了什么。第一个命令是网站生成器，它输出到一个名为`public`的目录。对此没什么好说的。只知道它包含了我网站的所有文件。然后，我们运行`ipfs-deploy`，神奇的事情发生了！

[`ipfs-deploy`](https://github.com/ipfs-shipyard/ipfs-deploy) 是由 [@agentofuser](https://github.com/agentofuser) 构建的一个工具，它执行以下操作:获取某个目录——在本例中是`public`——并将其添加到我们想要的任意多个锁定服务中，并更新我们定义的 DNS 提供商上的 DNSLink。

等等，什么？钉住服务？

因此，锁定服务是一种在线服务，它运行一个 IPFS 守护进程，通常也是一个网关，确保你的内容不会离开网络。现在你告诉我:这难道不会让去中心化和不需要电脑就能上网的观点失效吗？

嗯，你可以运行一个本地的 IPFS 守护进程- [就像你应该做的那样！](https://github.com/ipfs-shipyard/ipfs-desktop)——在自己的电脑上通过自己的节点添加内容。但是，如果没有其他人有你网站的内容，那么它就不会工作，除非你有你的电脑 24 小时连接到互联网。

【DNSLink 怎么样？

DNSLink 是您的域上的一个 TXT 记录，它指向某个 IPFS 哈希。假设您的网站在`example.com`可用，并且您的网站内容的哈希是`QmHash`，那么，您应该在`_dnslink.example.com`有一个值为
的 TXT 记录

```
dnslink=/ipfs/QmHash 
```

DNSLink 允许 IPFS 通过查看域名就能知道你网站的内容。当然，你不会让人们访问你的网站。

现在让我们来谈谈每项服务吧！

## 皮纳塔

Pinata 是一项免费增值服务，允许您免费锁定 1 GB 的数据。你需要在他们的网站上注册，然后你就能得到你的 api 密匙和秘密 api 密匙。你需要用这些值设置两个环境变量来让`ipfs-deploy`工作。

## 两只橘子的故事

我将 Cloudflare 作为首选 DNS 提供商。今年以来，Cloudflare 已经让[自己的 IPFS 网关](https://www.cloudflare.com/distributed-web-gateway/)开始运行。你能做的是:如果你的域名是`example.com`，在根设置一个 CNAME 记录为`cloudflare-ipfs.com`。这意味着您的网站将由 Cloudflare 的网关提供服务。

Pinata 和 Cloudflare 保持持久的连接，因此内容发现肯定会很快，因此在更新网站时不会有任何问题。

嗯，我已经使用了 Cloudflare，但现在“永远在 HTTPS”不再工作了...我做错了什么？

没什么，你没做错什么。现在，由于您的网站指向另一个 Cloudflare 网站(他们的 IPFS 网关)，在`cloudflare-ipfs.com`应用的规则将决定您的网站如何运行。而且，由于许多不同的原因，IPFS 网关已经禁用了“总是使用 HTTPS”。

但是这个会解决的！Cloudflare 正在开发一个名为 [Orange to Orange，或 O2O](https://blog.cloudflare.com/continuing-to-improve-our-ipfs-gateway/) 的解决方案，它将允许您覆盖设置，即使您的网站 DNS 指向他们服务的另一个网站！

至于`ipfs-deploy`，你还需要设置[一些环境变量](https://github.com/ipfs-shipyard/ipfs-deploy#cloudflare)来配置它，以便能够在 Cloudflare 自动更新你的 DNSLink。

然后，就跑

 ``ipfs-deploy -p pinata -d cloudflare`` 

 ``您的网站将被固定到 Pinata，您在 Cloudflare 的 DNSLink 将被更新。

但是这对于一个分散的网站来说太多了...

这是一个一次性的过程:你设置它，让它运行。如前所述，我使用 CircleCI 来建立我的网站，并在每次提交主分支时更新 DNSLink。可以看一下[设置](https://github.com/hacdias/hacdias.com/blob/master/.circleci/config.yml)。

**真的是去中心化了吗？**

是啊！DNS 是去中心化的，Cloudflare 是分布式 CDN。您的网站指向 Cloudflare IPFS 网关，这是一个由分布式 Cloudflare 网络提供服务的缓存 IPFS 网关。而且 Cloudflare 还需要从 Pinata 获得内容——或者如果你不想把它固定到外部服务的话，从你的电脑上获得内容！

你能做些什么来改善？

从安装 [IPFS 桌面](https://github.com/ipfs-shipyard/ipfs-desktop#install)开始，它会给你安装一个 IPFS 守护进程，并让它一直运行，给你的电脑带来超能力。然后，安装 [IPFS 伴侣](https://github.com/ipfs-shipyard/ipfs-companion)，浏览器扩展将启动你的浏览器！如果你这样做了，下次你打开我的网站(还有很多其他的！)，您将被重定向到您自己的网关，由您自己的 IPFS 节点提供，它将从找到内容的任何地方获取网站！

快乐放权！``