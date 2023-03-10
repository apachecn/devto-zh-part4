# 调试星期六:React 不与我合作

> 原文：<https://dev.to/jpin27/test-post-1-1jm1>

你好！这个帖子最初是一个虚拟帖子，用来测试 DEV.to API 如何与我的网站通信。好吧，既然我已经在这里了，我还是描述一下我想要完成的事情吧。

## 问题👻

我在用 [@emasuriano](https://dev.to/emasuriano) 的惊人的 [Mate starter](https://www.gatsbyjs.org/starters/EmaSuriano/gatsby-starter-mate/) 给盖茨比。我正在尝试使用开发到职位，而不是中等职位。我已经成功地将源代码插件更改为`gatsby-source-dev`并且我的 DEV.to 帖子已经被 GraphQL 成功抓取，但是我正在尝试弄清楚如何在我的网站上呈现实际的元素。

我的站点成功地输出了卡片——每篇博文一张，因为到目前为止我有两篇博文——但是返回的元素是“未定义的”:

[![Alt Text](img/218f9f46a5478c2c022bdb44c653c459.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--VF5SDa76--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/c19sn8us9mgy9wzig2e3.PNG)

## 解决问题的尝试🤔

我真的认为这与我的 GraphQL 查询后的`render`函数有关。希望我能在本周末解决这个问题——我想尽快有一个好的投资组合网站。谁没有呢？

## 我学到了什么🎓

我发现了什么是 ES6 扩散函数。这是一个非常漂亮的工具——如果我们在工作中使用 ES6，我肯定会使用它。

我还学习了 lambda 函数。当我试图找出 double => arrow => function 的含义时，我大学时代的幽灵——function currying 来拜访我。我很高兴在学校里学到了这一切；否则，我会更加困惑。

## 状态

这是我第一次调试 React web 应用。谈谈一次火烧眉毛的审判🔥！对某些人来说，解决方案可能非常简单，但我仍在试图解开这段代码的所有抽象，而且我已经喝了第二杯星巴克咖啡。

我应该去吃晚饭了。我头疼。