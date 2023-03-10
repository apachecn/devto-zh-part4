# 如何用小的 git 命令改变来加速 ci 服务器？

> 原文：<https://dev.to/gokhanaliccii/how-to-speed-up-ci-server-with-small-git-command-change-24i9>

去年我在做一个企业项目，已经维持了 3/4 年。在项目的开始阶段，存储库被设计为 android 和 ios 项目的单一存储库，但是在项目的开发过程中，它被拆分为不同的存储库。但是即使我运行 git clone 命令也花了将近 15 分钟。我以为这不会有问题，因为我不会经常运行这个命令，但是发生了什么呢？由于 ci 服务器，我开始频繁地运行 git 克隆提交。因为我创建了詹金斯的工作来运行测试，并在我们的公关过程中建立 apk，这基本上做以下步骤

*   设置 android 环境
*   **克隆项目**
*   运行单元测试
*   构建 apk

直到今天我才意识到如果我跑的话，如何加快项目克隆的速度

`git clone **url** --single-branch **branch-name** depth=1`

代替

`git clone **url**`

它会快 7 倍。

#### TIL: [浅层克隆](https://git-scm.com/docs/git-clone#Documentation/git-clone.txt---depthltdepthgt)