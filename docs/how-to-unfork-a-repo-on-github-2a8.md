# 如何在 GitHub 上取消回购

> 原文：<https://dev.to/nickymeuleman/how-to-unfork-a-repo-on-github-2a8>

> TL；DR: **2 个步骤**:重命名存储库。以原始名称导入重命名的存储库。

有时，GitHub 上的存储库一开始是一个分支。你现在想中断这种联系。

GitHub 上的 Forks 有一些特殊的行为，比如默认情况下不启用 issues 选项卡。

[![no issue tab](img/527cc3da9545a52fc62cc88c1aaf2f57.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---RjkZ60w--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickymeuleman.netlify.com/static/e54f9389ab54dc92af5a1049f2346a2d/b9e4f/no-issue-tab.png)

## 1。在 GitHub 上重命名您的存储库

去 GitHub 上的库，找到**设置**页面，重命名你的库。(别担心，你可以保留原来的名字。)

[![rename your repository on Github](img/2c719ae5d80bfb021e96f81a6c055075.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jQedqe6O--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickymeuleman.netlify.com/static/730e62fe508381b39b386524f5ef5321/1f44b/rename.png)

这个新重命名的存储库仍然被标记为分叉！

[![repository marked as a fork](img/b85e2fdb0f7e0680e52599a971b635e2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qWUT6TzC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickymeuleman.netlify.com/static/2edab85f41cc900f2fbdb388d15b35b2/1654f/repo-marked-fork.png)

## 2。导入存储库

GitHub 提供了一种导入存储库的方法。

[![import repository feature](img/dcd31f94127cb66e92f8b205de512e14.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Qdsjqt8p--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickymeuleman.netlify.com/static/b6e34f2ae3fa42f7008020e724ec8ea5/1b606/import-repo.png)

抓取链接以克隆您刚刚重命名的存储库并输入它。

> 注意:提供 https 链接，SSH 链接不行

对于名称，输入您的存储库的原始名称。

[![import screen](img/babf48523ca48e43b2d277d5526ac6ba.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4sS3X1Ec--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickymeuleman.netlify.com/static/342abcb4d68ec7737606c95eac1e8846/b9e4f/import-with-original-name.png)

过一会儿,“新的”存储库就准备好了。如果有一个电子邮件地址链接到该 GitHub 帐户，也会有一封电子邮件通知您成功。

[![Successfully imported the repository](img/16651c6b8a279cc44dd8159e95fabfae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--X6Vdi4yz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickymeuleman.netlify.com/static/44dc88e0bab3407fa962332f71162085/b9e4f/completed-import.png)

## 享受你的不可伪造的仓库

[![unforked repository](img/469befe07027c4a62b491bca992a6dc5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FDjPbViA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickymeuleman.netlify.com/static/6cba667e876aa0263879e2d4bd3e13c2/b9e4f/unforked-repo.png)

git 历史仍然存在。👍

本地克隆的项目不必接触。

由于远程 url 与之前相同，推送更改应该无需干预。

由于从技术上讲这是一个全新的资源库，一些东西(特定于 GitHub web UI)丢失了。例如:拉取请求、描述、提供的网站 url。

[![an empty header for a description and website](img/820b29f92af628481e6cf4dbdd3a4285.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yjckXPwm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://nickymeuleman.netlify.com/static/aaddc041f774f20b5e9abd89486e6556/b9e4f/empty-description.png)