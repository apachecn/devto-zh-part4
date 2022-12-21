# 🌐我如何在子域上托管我的项目

> 原文：<https://dev.to/chiangs/host-your-projects-on-subdomains-59m3>

我喜欢在子域名上托管我的项目，并保持我的主网站专注于成为我的投资组合的门户。下面是我如何使用 [Netlify](https://netlify.com) 和 [Porkbun](https://porkbun.com) 来完成的。

## 什么是[子域](https://en.wikipedia.org/wiki/Subdomain)？

子域是一个更大的域的一部分；唯一不是子域的域是根域。

📐示例:

```
root domain: www.mainSite.com

subdomain: someUrl.mainSite.com 
```

Enter fullscreen mode Exit fullscreen mode

## 为什么要在子域上托管项目？

1.  不会膨胀主站点的回购。
2.  它更灵活，所以我的项目可以是不同的技术栈。

## 步骤

### 主站点

#### 设置

1.  创建个人网站。
2.  在 GitHub 上为它创建一个 repo。
3.  推到回购上。

#### 部署

1.  在 Netlify 上，我为我的主站点选择回购和主分支。
2.  在它被成功构建和部署之后，我有一个指向我的已部署项目的 someWeirdString.netlify.com。
3.  我会将其重命名为更简单的名称，例如`myPortfolio.netlify.com`
4.  如果我在 Porkbun 还没有自定义 URL，我会设置一个。
5.  指定我的自定义网址，如[www.myPortfolio.com](http://www.myPortfolio.com)。

#### DNS 设置

1.  登录 Porkbun 并选择我的自定义 URL 详细信息。
2.  在记录中，我更新了回答我的 Netlify 子域的别名记录，即`myPortfolio.netlify.com`。

它应该是这样的:

| 类型 | 宿主 | 回答 |
| --- | --- | --- |
| 别名 | myPortfolio.com | www myPorfolio.netlify.com CNAME |

1.  在它传播之后，我继续下一步，提供一个 SSL 证书。

### 添加一个子域

一旦我的主网站建立并运行与[www.myPortfolio.com](http://www.myPortfolio.com)工作，我会设置从另一个回购在`project.myPortfolio.com`找到的另一个项目。

1.  就像前面的第一步一样，我将项目添加到 Netlify 中，并构建和部署它。
2.  我将把网络子域更新为`project.netlify.com`。
3.  然后我回到 Porkbun，给`www.myPortfolio.com`添加另一个 CNAME 记录，它会是这样的:

| 类型 | 宿主 | 回答 |
| --- | --- | --- |
| 别名 | myPortfolio.com | www myPorfolio.netlify.com CNAME |
| CNAME | project.myPortfolio.com | project.netlify.com |

就是这样！现在，每当我导航到 project.myPortfolio.com 时，我都会转到我的项目，该项目在每次项目主分支更新时都会自动部署。

❗:不要忘记添加一个像导航图标一样的逃生出口，让用户回到主网站和/或去另一个项目...这就是好 UX。

如果你觉得这很有价值，请留下评论并关注我的[dev . to @ Chiang](https://dev.to/chiangs)和 [Twitter @chiangse](https://twitter.com/chiangse) ，🍻干杯！