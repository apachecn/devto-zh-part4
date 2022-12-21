# 🍗Show Dev:免费向 Slack 发布 Twitter 搜索结果，无需使用 AWS Amplify 编写代码

> 原文：<https://dev.to/howyi/show-dev-posting-twitter-search-results-to-slack-for-free-and-without-writing-code-using-aws-amplify-c3g>

[![Alt Text](img/d9d5df3764d7bc72b4f1921fdc6a5d89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dPvTcUsN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m1omv6x3fgxo9aj65fsj.png)

# 储存库

## ![GitHub logo](img/375dfcc32199b4dedf2b526645c27ff7.png) [豪伊](https://github.com/howyi) / [推特黑](https://github.com/howyi/tweettoslack)

<article class="markdown-body entry-content p-5" itemprop="text">

搜索 Twitter 并自动将结果发送到 Slack。做好你的 AWS。

[![amplifybutton](img/655d874f17e48001226716c9475089d5.png)](https://console.aws.amazon.com/amplify/home#/deploy?repo=https://github.com/howyi/tweettoslack)

# 费用

它是免费的，因为它都在 [AWS 免费层](https://aws.amazon.com/free/)内。

# 要求

*   [Slack 传入 Webhook URL](https://api.slack.com/incoming-webhooks)
    *   SLACK_WEBHOOK_URL
*   [Twitter API 令牌](https://developer.twitter.com/en/docs/basics/authentication/guides/access-tokens.html)
    *   TWITTER _ 消费者 _ 密钥
    *   TWITTER _ 消费者 _ 秘密
    *   TWITTER_ACCESS_TOKEN_KEY
    *   推特 _ 访问 _ 令牌 _ 秘密

# 部署

*   点击`DEPLOY TO AMPLIFY CONSOLE`按钮。
*   部署后，访问 Lambda `tweettoslack-amplify`。和设置环境变量。
    *   搜索 _ 查询: [Twitter 标准搜索操作符](https://developer.twitter.com/en/docs/tweets/search/guides/standard-operators.html)

# 图表

[![](img/ccfd05a54b55d84940177abea0736671.png)](https://camo.githubusercontent.com/0896bd2379de378fa826e4b128b30b90d53d0167/68747470733a2f2f63646e2d616b2e662e73742d686174656e612e636f6d2f696d616765732f666f746f6c6966652f682f686f7779692f32303139303832302f32303139303832303030343331352e706e67)

</article>

[View on GitHub](https://github.com/howyi/tweettoslack)

# 截图

[![](img/b4da91f66a6a2e0234751cb2030b0803.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--B4CWjuTR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-ak.f.st-hatena.cimg/fotolife/h/howyi/20190820/20190820211259.png)

# 成本

它是免费的，因为它都在 [AWS 免费层](https://aws.amazon.com/free/)内。

# 要求

*   [Slack 传入 Webhook URL](https://api.slack.com/incoming-webhooks)
    *   SLACK_WEBHOOK_URL
*   [Twitter API 令牌](https://developer.twitter.com/en/docs/basics/authentication/guides/access-tokens.html)
    *   TWITTER _ 消费者 _ 密钥
    *   TWITTER _ 消费者 _ 秘密
    *   TWITTER_ACCESS_TOKEN_KEY
    *   TWITTER_ACCESS_TOKEN_SECRET #部署
*   点击`DEPLOY TO AMPLIFY CONSOLE`按钮。
*   部署后，访问 Lambda `tweettoslack-amplify`。和设置环境变量。
    *   搜索 _ 查询: [Twitter 标准搜索操作符](https://developer.twitter.com/en/docs/tweets/search/guides/standard-operators.html)