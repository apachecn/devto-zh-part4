# 将“开发数据”添加到您的投资组合中

> 原文：<https://dev.to/robp773/adding-dev-data-to-your-portfolio-5afk>

当招聘人员检查你的投资组合时，他们通常会很快进出。有可能他们有一大堆申请人需要查看，他们根本没有时间花超过 1-2 分钟的时间来查看单个投资组合(如果有的话)。因此，不言而喻，你的投资组合需要脱颖而出，以避免被遗忘。帮助招聘人员记住你的一个方法是利用你的“开发人员数据”,让他们更好地了解你是一名开发人员。

## 开发者数据

你的开发者数据基本上是你在做开发工作时在互联网上留下的所有指纹。你的公开提交历史、技术博客帖子、编码挑战、拉请求和开源贡献都是很好的例子。公开展示这些数据并组织到你的文件夹中是展示你的技能的好方法，也是展示你拥有这些技能的经验的好方法。如果你只有有限的时间给招聘人员留下印象，这就是你想让他们看到的东西。下面是我想分享的一些开发数据的来源。

## GitHub 数据

[GitHub API 文档](https://developer.github.com/v3/)

GitHub 可能是寻找开发者数据最明显的地方，所以我们将从那里开始。幸运的是，GitHub 有一个公共 API，如果你只是想获得提交历史和基本的用户数据，它非常简单。这是我的投资组合网站上的 getGitHubData 函数。

```
// high level wrapper for GitHub API
import GitHub from "github-api";
import axios from "axios";

export default async function getGitHubData() {
  // authenticate with your token - this is your "personal access token" and can
  // be created through your GitHub account dashboard
  var gh = new GitHub({
    token: process.env.GITHUB_TOKEN
  });

  // get profile data
  let me = gh.getUser();
  return me.getProfile().then(userData => {
    return (
      axios
        // get public commits
        .get("https://api.github.com/users/Robp773/events/public")
        .then(events => {
          let filteredCommits = [];

          // events from your GitHub account - these come automatically ordered by most recent
          events.data.map(event => {
            // filtering out events that don't have a commit in their payload
            if (event.payload.commits) {
              event.payload.commits.map(singleCommit => {
                filteredCommits.push({
                  repo: event.repo.name,
                  singleCommit,
                  created_at: event.created_at
                });
              });
            }
          });

          // I also count up my recent commits by repo for a chartjs graph 
          // but I cut that out for simplicity

          return {
            // commit list
            commits: filteredCommits,
            //  user data
            userData: userData.data
          };
        })
    );
  });
} 
```

我最终在我的站点上使用这些数据，在一个可滚动列表中显示我的提交，该列表位于一个图表的下方，该图表显示了我的提交是如何按存储库分解的。我还在我的主页上使用 GitHub 用户数据作为我的简历，并显示我作为开发人员工作了多少天(我基本上使用我的 GitHub 帐户创建日期作为我的开发人员生日——我知道这很俗气)。

我还没有一个令人印象深刻的开源贡献列表，但是如果你有，我强烈推荐你通过 API 编译它们。这会让你立刻从任何浏览你的投资组合的人那里得到印象分。

## DevTo + Dev 博客 API

我一直在努力写一些关于技术的文章，以巩固我在这方面的知识，并向雇主展示我确实喜欢在正常的 9-5 小时之外思考代码。因为我现在主要使用 dev . to——我使用他们的非官方 API 来获取我的博客文章。这里是另一篇 dev.to 文章，作为一些已知端点的参考-[dev . to v 0 API 的状态](https://dev.to/alfredosalzillo/the-state-of-devto-v0-api-1o2)。看起来官方 API 已经准备了一段时间，但是还没有发布。总结一下- `https://dev.to/api/articles?username={your user name}&page=0`会给你一系列的博客文章，还有评论、反应、标签、URL 等等。

## CodeWars 编码挑战

[CodeWars API 文档](https://dev.codewars.com/)

展示您技能的另一种方式是展示您的编码挑战活动。我在我的网站上使用 CodeWars。我最喜欢的端点是`/api/v1/users/:id_or_username/code-challenges/completed?page=0`，它将返回您最近完成的挑战的名称，以及完成时间和使用的语言。不管出于什么原因，没有很多其他的编码挑战平台允许你访问你的活动，所以在 CodeWars 之外获取这些数据可能会很困难。Codewars 还允许你访问 SVG 徽章，在外部网页上显示你的等级和个人资料信息。这里有一个例子。

[![Alt text of image](img/ca85fdb4ae815e0d0816fa797a0dae7d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--XnETF22b--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.codewars.com/users/jhoffner/badges/large)

## 附加来源

当我发现更多的源代码时，我会添加到这个列表中，但是现在我只想提一下 [Stack Overflow 的用户 API](https://api.stackexchange.com/docs/types/user) 。这是一个很好的地方来获得数据详细说明你的声誉和徽章计数，也将有助于显示你愿意帮助其他开发人员。

## 最后的想法

这是所有这些数据在我的[投资组合的活动页面](https://rob-peterman-dev-portfolio.netlify.com/activity)中的样子。UI 可能需要更多的工作，但希望你能得到我试图创建的东西——一个招聘人员可以快速浏览并立即感受到我作为开发人员所做的事情的页面。一切都在一个地方，所以他们不必去挖掘，以了解更多关于我的信息。

我想提到的最后一件事是拥有一个快速加载的投资组合网站的重要性，因为这在第一印象中起着很大的作用。为了避免在我的活动页面上加载 5 个以上的 spinners，我用 React Static +一个来自 Zapier 的自动化日常 webhook 构建了我的站点，以保持所有的静态数据都是最新的。如果您不熟悉静态站点生成器，基本上它们会在构建时将您的所有数据捆绑到您网站的路径中，这样您的静态数据几乎可以在页面加载时立即获得。如果出于性能原因，您打算获取大量数据，我强烈建议您走这条路。实际上，我写了一篇关于 React Static 的文章，如果有人想了解如何开始使用它的话。

这就是我所知道的，如果任何人对“开发者数据”的其他来源有任何进一步的想法，我很乐意听到他们！