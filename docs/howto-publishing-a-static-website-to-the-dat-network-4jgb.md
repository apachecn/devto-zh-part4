# 如何:将静态网站发布到 dat:// network

> 原文：<https://dev.to/kovah/howto-publishing-a-static-website-to-the-dat-network-4jgb>

有许多新的协议来建立一个新的互联网。意图很明显:当前的互联网依赖于单个服务器提供网站的文件。或者简而言之:是基于集权。这些新协议， [dat://(或 Dat)](https://dat.foundation/) 就是其中之一，它们将网络重新思考为一个基于 P2P 方法的共享网站文件的节点网络。Dat 是除了 [IPFS](https://ipfs.io/) 和 [Solid](https://solid.mit.edu/) 之外最受欢迎的系统之一。我最近重建了我的博客，并尝试用 Dat 网络来试验新的 P2P 网络。此博客可通过 [dat://fd1 获得...e8c/](https://dev.todat://fd1c022637e708328f44427cb04b1ebda3c41ff2044403fcfc188885a499ce8c/) 。

## 我为什么选择 Dat

我没有做很多关于这个话题的研究。也许 IPFS 更好，但我在 Reddit 和 Twitter 上看到一些帖子，说 Dat 是多么容易使用，与 IPFS 相比，它的性能相当好。也许我以后会尝试其他方法，但是现在我只是用 Dat 来发布我的博客。

## 基本要求

Dat 基本上是一个 P2P 文件共享网络。如果你发布了你的站点，你就和其他人共享了 HTML，CSS 和 JS 文件，这样他们就可以浏览你的站点了。因此，你不能发布依赖后端的网站，比如 PHP，Ruby 或者你正在使用的任何东西。如果你的网站需要动态内容，你必须从网络外的中央服务器获取。

总结一下:

*   让你的网站成为一个静态网站(HTML，CSS 和 JS)。
*   请确保您不要使用像`https://blog.kovah.de/`这样的固定网址，因为这将打破你的 Dat 网站的所有链接。
*   一个终端。
*   有效的互联网连接。

## 入门:Dat CLI

如果您从 Dat 开始，您可以随时选择使用 [Beaker 浏览器](https://beakerbrowser.com/)或 CLI 工具来管理您的网站。因为我在终端中做了很多工作，所以我选择了 CLI 工具。

CLI 工具可作为独立版本或通过 npm 获得，后者需要 Node.js。要安装该工具，请在终端中运行以下命令之一:

```
# Install the standalone version
curl -o- https://raw.githubusercontent.com/datproject/dat/master/download.sh | bash

# Install the npm version
npm install -g dat 
```

就是这样。现在，您可以使用`dat`命令与 Dat 网络进行交互。

### Dat CLI 的主要特性

这些是您需要发布到网络的 CLI 工具的主要功能:

*   `dat create` -基于您的本地副本创建新站点。
*   将你的站点同步到网络，运行直到你退出。
*   管理你网站的密钥。

## 开始发布你的站点到 Dat 网络

### 第一步:创建 Dat 站点

对于下面的步骤，我假设你现在在你站点的目录中，包含 index.html 和所有其他需要的文件，比如 CSS 和 JS。

首先，我们将根据您的文件创建一个 Dat 网站。为此，运行`dat create`命令。它将生成一个 dat.json 和一个. dat 文件夹，其中包含有关网站和所有内容的元信息。如果您就这样离开，您将只发布您的文件，但是添加标题可能是一个好主意。

编辑标题，打开`dat.json`文件，设置标题和简短描述，如下:

```
{  "title":  "Kovah.de Blog",  "description":  "Kovah.de Blog - My personal blog about topics like Web Development, Programming, Gaming and the Internet",  "url":  "dat://fd1c022637e708328f44427cb04b1ebda3c41ff2044403fcfc188885a499ce8c"  } 
```

### 第二步:将新站点同步到网络

现在您已经创建了站点，可以将它推送到网络上了。在开始同步之前，您应该确保一旦停止同步，您的站点仍然可以访问。因为正如我前面提到的:这是一个 P2P 协议，一旦你停止分享你的网站，它将不会被其他人使用，直到另一个主机开始再次分享它。这对于一个低流量的网站尤其重要。

对于你的第一次实验，我建议注册 [Hashbase.io](https://hashbase.io/) 。该服务会保管您的文件，并不断与网络共享。最大 100MB 的网站是免费的。或者，如果你有一个更大的网站，你可以通过使用 [Homebase](https://github.com/beakerbrowser/homebase) 建立你自己的共享服务器，这是一个由 Beaker 浏览器的开发者建立的共享服务器。随着我发布博客和网站，我很快就达到了 Hashbase 的 100MB 极限。我使用 [telyn/homebase Docker 镜像](https://hub.docker.com/r/telyn/homebase)设置 Homebase，效果非常好。

创建您的网站后，您将获得唯一的 URL，该 URL 可用于设置您的 Homebase 服务器的 Hashbase。准备好服务后，开始分享您的网站:

```
dat share 
```

### 第三步:导出您的密钥

在做任何其他事情之前，运行下面的命令:

```
dat keys export 
```

您将会看到您的密钥。把它保存在绝对安全的地方，像密码一样对待它。这绝对**至关重要**！秘钥允许你**编辑**这个非常 Dat 的网站。如果你丢失了它并删除了网站的本地副本，你将**再也不能**更新它了。

如果您删除了您的本地副本并想要更新您的站点，请运行以下命令:

```
# Make a local copy of the site
dat clone dat://YOUR-DAT-URL-HERE

# Import the secret key
dat keys import 
```

### 稍后更新网站

一旦你的网站上传，你可以打开烧杯浏览器，看看你的网站。如果你想更新它，只需编辑文件，然后运行`dat sync`命令。它将自动扫描所有文件，并相应地更新参考。如果你删除了你的本地拷贝，运行`dat clone ...`和`dat keys import`来获得一个新的可编辑的拷贝。

* * *

现在，您的静态网站在 Dat 网络中运行。恭喜你！请通过[推特](https://twitter.com/kovah_kvh)或乳齿象(`@Kovah@mastodon.social`)与我分享你的 Dat 网址。如果你有任何问题，请随时写信给我。

**我在 Dat 网络上的站点**

*   我的博客:dat://fd1c 022637 e 708328 f 44427 CB 04 B1 ebda 3c 41 ff 2044403 fcfc 188885 a 499 ce 8c/
*   我的作品集:dat://02 dcfb 85 D1 BFF 68d 5027 b 9969739 C3 eecf 171 c13 a 29 CDE 554 c 9 c 29 c 3271 a 515 a/

* * *

*本文最初发表于[blog . kovah . de](https://blog.kovah.de/en/2019/publishing-to-dat-network/)T3*