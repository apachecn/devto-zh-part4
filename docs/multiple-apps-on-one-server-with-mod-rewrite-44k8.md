# 一台服务器上的多个应用程序，支持修改重写

> 原文：<https://dev.to/syntaxseed/multiple-apps-on-one-server-with-mod-rewrite-44k8>

对于小公司和自由职业者或独立开发者来说，虚拟主机可能是一项花费，随着时间的推移，它会成为一笔很大的账单。无论你有几个 VPSes 或者共享主机账户，在他们自己的服务器上有多个小的、实验性的或者低流量的站点是没有意义的。您可以从多个子域或子目录中为它们提供服务，但是真讨厌！域名是便宜的，我喜欢一旦公开，就有东西在自己的域名上。

但是，托管公司通常只给你一个 web 根目录。就 VPS 而言，有时提供它们会对每个“应用”收取少量费用。一个应用程序在它自己的域上是一个独特的项目或站点，有一个单独的网络根目录(如`/www/appname/html/`)。

## 假装直到成功

让我们看看如何使用 Apache 的 mod-rewrite 模块创建几个假的 web 根，并根据请求域向它们发送流量。

### 设置

首先，我们假设您已经有一个域(比如 example.com)指向您的服务器。它是服务的网页，你把你的网站根。

接下来，将第二个域(example.net)放在同一个服务器或托管帐户上。我不会进入如何域停车工程。第二个域应该像别名一样在同一个 web 根目录中提供相同的页面。两个域连接到一个站点。

然后在 web 根目录下，创建两个目录:

*   站点-示例 com/
*   网站示例网/

并将现有的站点文件移到第一个文件中。向第二个站点添加一个带有一些文本的占位符 index.html，这样我们就可以看到这个或那个站点何时被提供。现在你已经准备好开始魔法了。

### Mod 重写是牛逼的多工具

Apache HTTP Server 有一个名为 **[Mod_Rewrite](https://httpd.apache.org/docs/current/mod/mod_rewrite.html)** 的模块，通常默认安装。请确保您的服务器上有它。你可以通过**给阿帕奇下达指令。web 根目录下的 htaccess** 文件。这将影响文件所在的目录以及所有子目录。创建一个名为的空文件。htaccess，或者编辑现有的。不要把它放在我们之前创建的那些 site-*目录中。

接下来，将以下内容放入该文件，我们来看看它会做什么。

```
<IfModule mod_rewrite.c>
RewriteEngine On

# Direct requests for example.COM: RewriteCond %{HTTP_HOST} ^(?:www\.)?example\.com
RewriteCond %{REQUEST_URI} !^/site-examplecom/
RewriteRule ^(.*)$ /site-examplecom/$1 [L]

# Direct requests for example.NET: RewriteCond %{HTTP_HOST} ^(?:www\.)?example\.net
RewriteCond %{REQUEST_URI} !^/site-examplenet
RewriteRule ^(.*)$ /site-examplenet/$1 [L]

</IfModule> 
```

Mod_Rewrite 接受请求的 URL，并基于 PCRE 正则表达式规则，可以更改相应的文件系统路径，甚至重写到不同的 URL。

<ifmodule>指令将确保只有当模块存在时，里面的行才被执行。</ifmodule>

里面的第一行很清楚，它打开了重写引擎。不开玩笑。

然后我们有两个几乎相同的 3 行代码块，由一个注释引导。让我们检查这三行。

第一:

```
RewriteCond %{HTTP_HOST} ^(?:www\.)?example\.com 
```

一个或多个 **RewriteCond** 指令可以在一个**重写器**之前。只有满足上述条件时，才会执行该规则。

这一行将来自请求头(URL 的基域部分)的传入 http 主机与正则表达式`^(?:www\.)?example\.com`进行比较。^符号的意思是“始于”。所以我们检查请求主机是否以 example.com 开始，并且`www.`是可选的，没有被捕获到变量中。如果主机匹配这个正则表达式，我们就进入下一行。

第二:

```
RewriteCond %{REQUEST_URI} !^/site-examplecom 
```

该条件检查请求 URI(主机/域后面的部分)是否不(“！”否定正则表达式)从`/site-examplecom`开始。当我们成功重写时它将包含。这很重要，它阻止我们进入一个死亡的无限循环，并得到一个服务器 500 错误。

第三:

```
RewriteRule ^(.*)$ /site-examplecom/$1 [L] 
```

最后，如果条件得到满足，我们将替换所请求的路径(主机和端口之后的 url 部分)。注意:已经映射的目录将被删除。所以如果。htaccess 文件位于顶部 web 根目录中，您将获得不带前导斜杠的完整 URL 路径。如果。htaccess 文件在 web 根目录的一个`test/`子目录中，你只需要匹配 URL 路径中 `test/`之后的*部分的模式。*

`(.*)`模式从 URL 路径的开头('^')到结尾(' $ ')获取所有内容，保存在变量`$1`中，然后粘贴在`/site-examplecom/`之后。`[L]`标志表示如果该请求被执行，则停止处理该请求的规则。

### 高层看

我们有两个条件和一个规则，为我们的两个“应用程序”复制。流程是这样的:

*   如果是正确的 http 主机。
*   如果它不是已经请求我们的假`site-*`应用程序根。
*   然后将`site-example*/`追加到 URL 路径的前面。

仅此而已。如果您访问不同的域，您现在应该会看到两个不同的站点。您可以添加另一个驻留的域、子目录和重写指令块来添加另一个“应用程序”。

### 注意事项

*   伪造网络根有一些缺点。你的应用程序并不是彼此孤立的。理论上，它们可以互相干扰，一个漏洞会影响其他漏洞。

*   你的主人可能会反对这样做。尽管我不明白为什么你不能利用你所掌握的工具的力量。

*   您应该将应用程序的数据库分开。这将使移动和迁移应用程序/站点更容易，并避免与表的冲突。

*   确保任何处理文件系统路径的代码都考虑到 url 路径和文件系统路径不匹配。

*   使用。htaccess 文件会使 Apache 变慢一点。如果您可以访问服务器的主 httpd 配置，那么您应该在那里的一个<directory>块中设置这些规则。</directory>

*   一个服务器或主机帐户上的多个站点会很快耗尽系统资源。这就是为什么我只建议流量非常低的小项目这样做。还没有自己的服务器/主机的网站。

### 好玩的临时演员

madewithlove 网站上有一个测试 Mod_Rewrite 规则的[优秀工具。](https://htaccess.madewithlove.be/)

如果你想去除 www 上的请求，并确保你的站点只通过裸域访问，在你的其他规则之前添加这个(适用于所有域)(`R=301`标志告诉浏览器和搜索引擎这是一个永久的重定向，考虑让它关闭，直到你满意它的工作):

```
# Remove the www: RewriteCond %{HTTP_HOST} ^www\.
RewriteRule ^(.*)$ https://%{HTTP_HOST}/$1 [R=301,L] 
```

### 这就是所有的乡亲

我希望这个小教程是有帮助的。写这篇文章的时候我学到了很多，虽然我已经用了很多年了，但是我也很好奇是否有人有我应该考虑的问题或者我可以改进的地方。感谢阅读！

——
[原载于 Blog.SyntaxSeed.com](https://blog.syntaxseed.com/multiple-apps-on-one-server-with-mod-rewrite/)