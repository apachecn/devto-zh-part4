# 使用 GitHub 作为内容交付网络

> 原文：<https://dev.to/amit_merchant/using-github-as-a-content-delivery-network-50pe>

这是一篇关于我如何开始利用 [GitHub](https://github.com) 作为[内容交付网络](https://en.wikipedia.org/wiki/Content_delivery_network)(某种程度上)以及你如何做同样事情的快速文章。

通常，cdn 用于在您的网站上快速发布内容，并从图像、文档等资源中节省一些服务器空间。出于类似的原因，我曾尝试将 GitHub 用作 CDN。我在 GitHub 页面上运行这个博客，你可能知道 GitHub 只允许每个存储库的内容小于 1 GB。我不想利用我的 GitHub 页面的存储空间来存储我最终会在博客上使用的图片。所以，我想出了一个办法。

## 存储库为 CDN (RaaC) [？]

我想到了创建和使用另一个 GitHub 库，在那里我可以存储所有的图片资源，并且可以在需要的时候在我的博客上发布内容。我创建了一个名为 [cdn](https://github.com/amitmerchant1990/cdn) 的存储库，它实际上是一个基本的存储库。我会在这里添加图像和资源。

现在，我已经将我的 GtHub pages 站点配置为使用我的自定义域，我可以使用 URL `https://www.amitmerchant.com/cdn/<resource name in the repos>`轻松访问这个站点上的任何内容。例如，存储库中有一个名为`bulb-min.jpg`的图像，我可以使用以下 URL 访问它:【https://www.amitmerchant.com/cdn/bulb-min.jpg 的 T2】

现在你知道了！只需点击几下鼠标，即可使用您的免费 CDN。很简单！

这样，我可以节省我原来的 GitHub pages 站点上的空间，我不想在 1 GB 的限制下耗尽这些空间，如果“cdn”存储库也耗尽了，我可以随时创建另一个名为“cdn2”或类似名称的存储库。；)

## 在关闭

我相信这里的许多人会觉得这很有趣，但相信我，它的功能非常强大，人们肯定可以用它作为 CDN 的替代品，他们可以在几分钟内完成。我希望这对正在寻找快速 CDN 服务的人有用。:)

下次见！

> 最初发布于[https://www . Amit merchant . com/using-github-as-content-delivery-network/](https://www.amitmerchant.com/using-github-as-content-delivery-network/)