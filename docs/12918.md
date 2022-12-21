# 使用 Cloudfront、S3 和 53 号公路进行托管

> 原文：<https://dev.to/jillesvangurp/using-cloudfront-s3-and-route-53-for-hosting-395o>

*   [基本设置](#basic-setup)
*   [更新内容](#updating-content)
*   [云锋失效](#Cloudfront-invalidations)
*   [使用 S3 重写 Url](#url-rewrites-using-s3)
*   [结论](#conclusion)

在过去的几个月里，我已经退役了我们的 nginx 服务器，用 Cloudfront、s3、Route 53 取而代之，亚马逊颁发了证书来简化托管 [www.inbot.io](https://www.inbot.io) 。这比我喜欢的要痛苦一点，而且出现了一些问题。这主要是因为 AWS 文档很糟糕，而且分散在不同的产品上，通常代表了一个误导的迷宫。

任何从亚马逊读到这篇文章的人，对于那些想把你的产品组合成一个基本用例的人来说，这是一个很有帮助的 guid，比如“给定一个完全 bog 标准的单页 javascript 网站，这就是你如何在 AWS 上托管它”会非常有帮助。取而代之的是，你对你的每一个产品都有一点点的文档，把所有其他的一点点文档留给读者作为练习。我不得不参考 Stackoverflow，因为你登陆的每个文档页面似乎都缺少一些关键的细节。我浪费了大量的时间来弄清楚这些产品最基本的用途。

好消息是，一旦你在他们的每个产品中找出所有的变通方法，它就能工作。好处是它为一个简单的单页应用程序简化了您的基础架构。我们没有自我托管的零碎东西。此外，CDN 确保您的用户从快速 CDN 下载您的网站时有良好的体验，而不是在地球的另一端访问您的糟糕的 web 服务器。坏消息是 AWS UI 在可用性和灵活性方面有点欠缺，做一些你在 nginx 等中会做的常见事情并不那么容易。

所以，为了避免我不得不再次搜索这个，我记录了我必须做的事情。此外，其他人可能会发现这很有用。

## 基本设置

首先，感谢 Tiberiu Oprea】提供了这个非常有帮助的概述。这会让你走得很远。我不会重复他说的每一句话，相反，我会把重点放在一些我必须单独解决的额外问题上。AWS 的人读这篇文章，使用这个网站作为如何正确记录你的产品的参考。

假设您完全按照他的指示做了，您将最终得到一个 Cloudfront + s3 设置(只需用您自己的域替换`inbot.io`):

*   AWS 证书管理器中`inbot.io`和`www.inbot.io`的 https 证书。这里的一个问题是，Cloudfront 需要很长时间才能真正看到这些。给它几个小时的时间。如果你做错了，删除并再试一次。我在这上面浪费了半天时间，我认为我做错了，事实上 Cloudfront 在实际请求当前证书列表时有点愚蠢。最终他们出现在相关的下拉菜单中。给它一些时间。
*   带有域`www.inbot.io.s3-website-eu-west-1.amazonaws.com`的 s3 存储桶。这是您部署静态内容的地方。为此，我建议使用 AWS cli。
*   具有域`inbot.io.s3-website-eu-west-1.amazonaws.com`的另一个 s3 存储桶。这个桶被配置为重定向到`www.inbot.io`。它没有内容。
*   两个桶的两个匹配的 Cloudfront 设置。确保在 Cloudfront 中将 http 重定向到 https。
*   Route 53 中的两个 A 记录指向两个 Cloudfront 设置中有和没有`www`的域。

请注意，**桶名对于让 53 号公路做正确的事情很重要。所以在桶名中匹配域名，不要在这里发挥创意。**

您可以使用几个简单的 curl 命令来验证所有的重定向:

```
# Cloudfront redirects to https
curl -v http://inbot.io 
# Cloudfront hits the S3 bucket and S3 redirects to https://www.inbot.io
curl -v https://inbot.io 
```

Enter fullscreen mode Exit fullscreen mode

如果一切正常，你的浏览器会通过两个永久重定向将`http://inbot.io/whatever/path`重定向到`https://www.inbot.io/whatever/path`。因此，用户在`inbot.io`中输入的内容最终会出现在你的网站上，而不是一个空白页，或者更糟，一个 S3 403 XML 页面。

这似乎是 AWS 产品的人需要得到一点打击。这实际上是他们产品的用户可能拥有的最基本的用例之一，那就是托管一个简单的网站，这太糟糕了。这个世界上几乎没有一个网站不希望它开箱即用，不需要 https 重定向和处理带/不带 [www](http://www) 的域名。这应该不需要两个独立的 buckets 和 Cloudfront 设置。这太疯狂了。但至少有用。

## 更新内容

Cloudfront 的一个问题是 TTL 非常长。这有助于在全球分布的 CDN 中缓存内容，但当您需要修复网站中的 bug，而您的用户需要花费数小时或数天时间才能获得修复时，这就很烦人了。解决这个问题需要一些规划。

如果您使用类似 Webpack 的东西，您应该确保文件名是散列的，所以这对于大多数文件来说并不重要。这给你留下了一个仍然是个问题的文件:`index.html`。因为在我们的例子中，这个文件很小，所以我最后禁用了 TTL。

在我们的 CI 构建中，我们使用 AWS cli 与 AWS 进行交互。我们使用这个命令上传我们的 index.html 如下:

```
aws s3 cp ./build/index.html ${S3_BUCKET} \
  --acl public-read \
  --cache-control max-age=0,no-cache,no-store,must-revalidate 
```

Enter fullscreen mode Exit fullscreen mode

一旦你修好了什么东西，S3 就会尊重这些标题并开始为你的新 index.html 服务。Cloudfront 也会将这些头传递给浏览器。

对于其他文件，我们使用合理的 TTL。我们有一堆很少改变的静态文件和许多生成新文件的 webpack 散列工件。

这里有一个问题是不要使用`aws sync --delete`。这里的问题是，如果您从 s3 中删除一个文件，而用户仍然有一个旧的`index.html`指向旧的文件散列，他们现在将运行到 404，直到他们在浏览器中强制重新加载页面。当然，假设他们知道如何做到这一点；这给手机带来了一些独特的挑战。

## 云锋失效

您可以在部署新内容时强制 Cloudfront 失效。要作废的主文件是`index.html`。这确保了 Cloudfront 可以在几分钟内更新全球范围内的 CDN 节点，而不是在下一个用户第一次尝试加载文件后更新得更慢。因此，在更新后的几分钟内，任何重新加载我们页面的浏览器都会点击 Cloudfront 来获取最新版本，因为它会转发`max-age=0,no-cache,no-store,must-revalidate`然后获取最新版本。

```
aws Cloudfront create-invalidation \
  --distribution-id ${CLOUDFRONT_ID} \
  --paths / /index.html /app/index.html 
```

Enter fullscreen mode Exit fullscreen mode

当然，保持 index.html 较小会有所帮助，因为来自 s3 的加载会比来自 Cloudfront 的缓存命中稍慢。这没问题，因为我们的大部分内容都在 javascript 和其他文件中。

## Url 使用 S3 重写

我们在 nginx 中做了一些 url 重写。这让我们有些头疼，直到我发现如何使用 S3 路由规则。这里的部分问题是，我们分发给用户的一些链接在我们不知道的情况下断开了，因为在重定向过程中丢失了一些东西。例如，我们有一个推荐新用户的奖励计划。他们点击了一个链接，这个链接过去会转到`http://inbot.io/join/XYZ`(我们已经修改了这个链接，至少会转到`https`)，在这里，XYZ 是一个推荐代码，需要被传递到 javascript 中，这个 JavaScript 在我们的网站上构造注册表单，这样它就可以被传递到我们的服务器。为了重定向它，我向我们的 S3 桶的静态内容属性添加了一个规则:

```
<RoutingRules>
  <RoutingRule>
    <Condition>
      <KeyPrefixEquals>join/</KeyPrefixEquals>
    </Condition>
    <Redirect>
      <Protocol>https</Protocol>
      <HostName>www.inbot.io</HostName>
      <ReplaceKeyPrefixWith>app/index.html#/join/</ReplaceKeyPrefixWith>
    </Redirect>
  </RoutingRule>
</RoutingRules> 
```

Enter fullscreen mode Exit fullscreen mode

注意，协议和主机名是必不可少的，否则 S3 会很乐意将你重定向到没有 SSL 的 bucket url。

您还可以使用`RoutingRules`来修复更好的 http 错误，例如 S3 在找不到对象时抛出的 403(这怎么不是 404？).总有一天，我可能会花一些时间使用 AWS cli 或 Cloudfront 在一个命令中做到这一点，但我最终在 AWS ui 中单击了所有这些。

所以这解决了我们的问题。请注意，我们正在重定向到`app/index.html#/join/XYZ`。我决定为 web 应用去掉我们的子域，简单地把所有东西放在`www.inbot.io`下。在我们的 web 应用程序中，我们使用`#`路径(又名锚),一切都由我们的 javascript 处理。

## CORS 头球攻门

我们遇到的另一个问题是，我们的网站上有一个文件需要正确设置 CORS 标题。问题中的文件是 Stellar 用来计算我们的加密货币 InToken 的元数据的`stellar.toml`文件。怎么做和为什么做并不重要，但是设置 CORS 是一个常见的需求，并且在 nginx 中很简单。这个问题让我思考了一会儿。S3 并没有为此提供一个好的解决方案。然而，事实证明，在 Cloudfront 中，您可以为特定路径配置所谓的缓存行为。在我们的例子中，我们为`/.well-known/stellar.toml`添加了一个带有自定义标题的行为。这个位置是 Stellar 客户期望找到元数据文件的地方。由于很多优秀的客户端都是浏览器应用程序，它们需要正确设置 CORS 头。如果你想了解更多，请参考[恒星文档](https://www.stellar.org/developers/guides/concepts/stellar-toml.html)。

## 结论

这个设置工作得很好。我们有一个简单的 CI 工作来完成大部分工作。我确实使用了 AWS UI 将 buckets 和 Cloudfront 设置连接在一起。你也许可以使用例如 Cloudformation 或者一些 aws cli 咒语来实现这个，但是因为这是一次性的事情，所以我没有费心去自动化它。另一方面，这是一个漫长的过程，有很多步骤，很容易出错。

此外，我对不得不在 AWS UI 中做很多事情(痛苦地无法使用)并不完全满意。然而，我不能让自己去自动化这个有希望的一次性设置。当自动化这种东西时，回报是递减的。