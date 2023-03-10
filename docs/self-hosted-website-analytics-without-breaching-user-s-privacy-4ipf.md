# 不侵犯用户隐私的自托管网站分析

> 原文：<https://dev.to/darthknoppix/self-hosted-website-analytics-without-breaching-user-s-privacy-4ipf>

我们都爱免费，但生活中没有什么是真正免费的。几乎所有的东西都有成本，如果你不用钱支付，那么你就用别的东西支付。我过去常用的分析平台是谷歌分析，它是免费的，功能齐全，但有一个问题。对我来说是免费的，但是那些想访问我博客的用户怎么办？他们用自己的隐私来支付。我比几年前更加关注隐私问题，我想尽自己的一份力量确保访问我网站的用户不会被不必要的跟踪。这是通向更加注重隐私的分析平台之旅的起点。

## TL；速度三角形定位法(dead reckoning)

试试 Fathom Analytics，它是开源的，易于自托管。把用户的隐私放在你的网络分析的最前面。

#### 我们在努力解决什么？

不像大企业试图确定谁在访问他们的网站，他们符合什么样的人口统计，他们有多大可能购买一些东西，我只是想要一个大概的数字，有多少人在阅读我的文章，这样我就可以迎合我的读者。我只需要在保护用户隐私的同时，为慈善字节收集简单的统计数据。

[![Self-hosted Website Analytics without breaching user's privacy](img/ef36097c61efff30da49a87c085309c4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7sTRi0sF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.sethcorker.com/conteimg/2019/07/fathom-1.jpg)

#### 我们如何解决以隐私为中心的分析？

经过一番研究，我发现了 Fathom Analytics。他们网站上的标语引起了我的注意。

> Fathom Analytics 提供简单、有用的网站统计数据，而不会跟踪或存储用户的个人数据。

Fathom 给了我足够的信息来做出明智的决定，希望不会给我的网站增加不必要的麻烦。他们提供托管服务，或者你可以自己托管，这是我选择的，因为我开始在数字海洋上使用 Ghost 托管我的博客。

#### Fathom vs Google Analytics？

Fathom 为您提供了数量非常有限的跟踪指标，但足以完成工作。

*   顶级页面
*   热门推荐人
*   跳出率
*   人们在你的网站上花费的平均时间

与谷歌分析相比，这是一个最低限度的产品，但这就是我所寻找的。我不需要知道用户使用的是哪种浏览器或设备。我只想知道什么做得好，我不关心他们从哪里阅读或者他们点击了什么。这不会适合每个人，但 Fathom 适合我，如果你想要简单轻便的东西，它可能适合你。

## 我在！我如何设置它？

设置非常简单，我在 DigitalOcean 上托管所有东西，因为这是我迄今为止用过的最好的托管平台。它有透明的定价，一个伟大的用户界面和廉价的主机选项是物有所值。如果您不喜欢，您可以在任何运行 linux 的主机上尝试一下。(我一直在使用 Ubuntu 18.04，因为它最容易安装)。

如果你想试试数字海洋，我用它来托管仁慈的字节和理解分析，它很棒！如果你还没有账户，那么当你使用我的推荐链接时，从 [$50 开始。通过与 DigitalOcean 签约，你可以帮助支持我的网站，并尝试一个我认为最好的托管平台。](https://m.do.co/c/427f9779439a)

### 设置一个水滴

如果你没有使用 DigitalOcean，那么你可以跳过这一步，在你选择的主机上安装 Ubuntu。

登录仪表板后，第一步是创建一个新的 droplet。

<figure>[![Self-hosted Website Analytics without breaching user's privacy](img/1cee289bf7b949cb7173c92162e09969.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--O2c_RTJ5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.sethcorker.com/conteimg/2019/07/Screenshot-2019-07-12-at-11.26.54-am-2.png) 

<figcaption>登录数字海洋</figcaption>

</figure>

时的仪表盘

我们将使用 Ubuntu 18.04 配置我们的 droplet，你可以尝试一下 FreeBSD，因为 Fathom 也有一个[预编译版本可以下载](https://github.com/usefathom/fathom/releases)。我目前使用的是每月 5 美元的标准计划。对于这项任务来说，这是令人惊讶的体面，到目前为止我还没有遇到任何问题。

<figure>[![Self-hosted Website Analytics without breaching user's privacy](img/d0b30637d7b4eb045d0258bea4aa6f7f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ostlkk6v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.sethcorker.com/conteimg/2019/07/Screenshot-2019-07-12-at-11.27.18-am-3.png) 

<figcaption>创建液滴，分配，计划和机器</figcaption>

</figure>

选择一个数据中心区域，您可以选择一个更接近您的目标受众的区域，默认情况下会选择新加坡。设置一些身份验证也很重要。我更喜欢 SSH 密钥，因为它很方便。你可以将密钥添加到你的帐户，然后选择允许哪些密钥在此登录。或者，如果这太麻烦，那么一次性密码就足够了。您可以设置它，而不必担心它。

<figure>[![Self-hosted Website Analytics without breaching user's privacy](img/dba49954584d3597a193ca259ab4b3c0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Mwrm23nd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.sethcorker.com/conteimg/2019/07/Screenshot-2019-07-12-at-11.27.49-am-2.png) 

<figcaption>创建一个数据中心，并认证</figcaption>

</figure>

最后，我们可以完成设置，我选择了一个 SSH 密钥，这将允许下一步轻松登录。继续创建水滴。添加一些标签，如果你想再次找到它(取决于你有多少滴)，并选择一个难忘的主机名，默认的是描述性的，但不可识别。

<figure>[![Self-hosted Website Analytics without breaching user's privacy](img/fe5cf8a0c3adfee3a20b1f31fa485545.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--321LQ1LW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.sethcorker.com/conteimg/2019/07/Screenshot-2019-07-12-at-11.28.03-am-2.png) 

<figcaption>创建一个水滴，最终屏幕</figcaption>

</figure>

我们的液滴已创建，可能需要几秒钟才能完成。现在它已经启动并运行了，我们需要通过 SSH 连接到它，安装 Fathom 并配置它。

<figure>[![Self-hosted Website Analytics without breaching user's privacy](img/d00e58da1c623636d63d34ef561b4b6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cJVyziZX--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.sethcorker.com/conteimg/2019/07/Screenshot-2019-07-12-at-11.29.51-am-2.png) 

<figcaption>水滴被创建！</figcaption>

</figure>

### 在我们的 Ubuntu 水滴上设置 Fathom

设置 Fathom 非常简单，我们可以在一分钟内完成并运行它。第一步是通过 SSH 进入我们的机器，复制我们的 droplet 的 IP 地址，然后像这样运行下面的命令:

```
ssh root@157.230.249.142 
```

如果一切都设置正确，你应该登录。如果你没有使用 SSH 密钥，那么你需要输入密码(通过电子邮件发送)。

<figure>[![Self-hosted Website Analytics without breaching user's privacy](img/2fd41b4e42a227095de40762b35949d0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ZJlCMdnm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.sethcorker.com/conteimg/2019/07/Screenshot-2019-07-12-at-11.31.04-am-1.png) 

<figcaption>宋承宪成新滴</figcaption>

</figure>

我们下载 Fathom 吧，我用的是 Fathom 1.2.1 for Linux。

<figure>

```
wget https://github.com/usefathom/fathom/releases/download/v1.2.1/fathom_1.2.1_linux_amd64.tar.gz 
```

<figcaption>The [releases can be found here](https://github.com/usefathom/fathom/releases)</figcaption>

</figure>

一旦完成，我们需要将它解压到 out bin 目录并使其可执行:

```
tar -C /usr/local/bin -xzf fathom_1.2.1_linux_amd64.tar.gz
chmod +x /usr/local/bin/fathom 
```

我们可以用`fathom --version`测试它的工作情况，如果你得到一个类似下面截图的版本字符串，那么你就一切就绪了。我们现在可以运行服务器，但我们应该先配置它。

<figure>[![Self-hosted Website Analytics without breaching user's privacy](img/51f16f6a0ff3a10f782015ad6957ecc2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--388FWmR0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.sethcorker.com/conteimg/2019/07/Screenshot-2019-07-12-at-11.34.42-am-1.png) 

<figcaption>下载理解并安装</figcaption>

</figure>

让我们制作一个目录来存放我们的 Fathom 数据:

```
mkdir ~/fathom-analytics
cd ~/fathom-analytics/
nano .env 
```

的。env 文件将是我们的配置，在这里我们可以设置我们想要使用的数据库类型和服务器将被服务的端口。

```
FATHOM_SERVER_ADDR=9000
FATHOM_GZIP=true
FATHOM_DEBUG=true
FATHOM_DATABASE_DRIVER="sqlite3"
FATHOM_DATABASE_NAME="fathom.db"
FATHOM_SECRET="random-secret-string" 
```

我将使用 SQLite 数据库，因为它很简单，而且我不想设置外部数据库。也可以使用不同的数据库，如 MySQL 或 Postgres。如果你需要更多的定制，看看[配置选项](https://github.com/usefathom/fathom/blob/master/docs/Configuration.md)。请确保将 FATHOM_SECRET 设置为唯一的值，这用于签署会话 cookies。

<figure>[![Self-hosted Website Analytics without breaching user's privacy](img/c6b195eb35f439bf7c9e43ac091eabc9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yd3oO_Gr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.sethcorker.com/conteimg/2019/07/Screenshot-2019-07-12-at-11.37.24-am-1.png) 

<figcaption>配置揣</figcaption>

</figure>

现在我们已经配置好了，让我们看看 Fathom 是否在工作！

运行`fathom server`启动服务器并导航到您的服务器。在我的例子中，我的服务器的 IP 是`157.230.249.142`，我将我的`FATHOM_SERVER_ADDR`设置为 *9000，*，所以[http://157.230.249.142:9000](http://157.230.249.142:9000)将显示下面的页面。

<figure>[![Self-hosted Website Analytics without breaching user's privacy](img/2d99a0342601cf8af42618c3446b0f6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--i7PJChVf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.sethcorker.com/conteimg/2019/07/Screenshot-2019-07-12-at-11.38.25-am-1.png) 

<figcaption>揣摸起来跑</figcaption>

</figure>

你差不多可以走了，但是你注意到什么了吗？没有不安全的身份验证，所以让我们添加一个用户。停止您的服务器，使用您选择的电子邮件和密码运行以下命令。

<figure>

```
fathom user add --email="hello@mydomain.com" --password="look-at-my-strong-password" 
```

<figcaption>Setup a user on your Fathom instance</figcaption>

</figure>

现在完成了，用`fathom server`启动你的服务器，你会看到你的新登录页面。

<figure>[![Self-hosted Website Analytics without breaching user's privacy](img/151685e003e1ac61502dddd97d262c6b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jSg_VkgV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.sethcorker.com/conteimg/2019/07/Screenshot-2019-07-12-at-11.40.54-am-1.png) 

<figcaption>揣摸用我们的用户凭证担保</figcaption>

</figure>

现在，您应该可以在几分钟内让 Fathom Analytics 在您自己的服务器上运行。我推荐从 Fathom 安装指南中获取一些步骤，比如使用 [NGINX 让 SSL 运行](https://github.com/usefathom/fathom/blob/master/docs/Installation%20instructions.md#using-nginx-with-fathom)并设置 [Fathom 在引导时启动](https://github.com/usefathom/fathom/blob/master/docs/Installation%20instructions.md#using-nginx-with-fathom)，以备您需要重启服务器并希望确保不会错过重要步骤时使用。

有用的链接

*   [关于 Fathom 的一切](https://usefathom.com/)看看官方网站，看看你为什么会想使用 Fathom，社区版(我目前正在使用的)和他们的托管版之间有一个比较，这增加了一些额外的安心。最终，他们计划推进他们的平台，但是 1.0 版本是非常好的自托管或其他方式。
*   [Fathom 安装说明](https://github.com/usefathom/fathom/blob/master/docs/Installation%20instructions.md)这是我的文章所基于的指南，安装和配置很简单。如果您想要最基本的东西来启动并运行它，请跳过我的文章，看看这里。
*   [Fathom Releases](https://github.com/usefathom/fathom/releases) 你会想为你选择的主机操作系统下载一个版本，除非你想从源代码中构建它。我使用的是 1.2.1，这是接线时的最新版本。
*   [$50 DigitalOcean credit](https://m.do.co/c/427f9779439a) 想继续玩下去，在 DigitalOcean 中获得一些点数。我把它用于我的个人项目，尽管我可以免费使用其他一些云托管提供商，我还是为 DigitalOcean 付费，因为它很容易使用，而且功能强大。

我希望你同意用户隐私是互联网上一项重要的保护权利。作为创作者，我们想知道我们正在产生影响，我们想衡量我们的产品和内容做得如何。我们仍然可以在不放弃用户数据的情况下获得有价值的信息。看看你想要跟踪什么指标，看看像 Fathom 这样的平台或另一个注重隐私的分析平台是否符合你的愿景。

* * *

如果你喜欢建立自己的东西，为什么不看看为什么我和幽灵一起主持我的博客。如果你还没有意识到，你在仁慈的字节上浏览这个博客，它是由自托管的 Ghost 提供支持的，我也使用自托管的 Fathom Analytics。