# 水果推车:配置

> 原文：<https://dev.to/sleepycecy/fruit-cart-config-31f6>

故事切片，目标设定，现在是时候开始推车了。

[![woman spinning a rolling shopping cart and catching it](img/d7c1fe902ee182d925ee4b54aa12a9fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0Ia-0G1q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/hMwNmp6.gif)

配置和项目启动对我来说一直很尴尬。在项目持续期间，你只启动一次项目，所以你必须采取的所有步骤都不在我的肌肉记忆和大脑记忆中。毕竟，我不会不断地开始新项目。

但是我们必须开始。一开始，我们是这样做的:

*   使用[https://start.spring.io/](https://start.spring.io/)引导一个 Gradle 项目我们添加了 JPA、Devtools 和 Postgres 依赖项，你可以在网站上搜索。我们将使用 MyBatis 来运行迁移，但不是作为一个映射器；这将由 JPA 存储库“秘密”处理。

*   为 Fruit Cart 启动了一个 Git repo(远程和本地),并执行了初始代码库的第一次提交。

*   安装并运行 Postgres 服务器。按照以下说明使用超级用户“fruitcart”创建了一个新的数据库“fruitcart”:

`Start postgres server and setup database
* check that the fruitcart superuser exists
run "psql -c '\du'"
* if user does not exist
run "c" (no need to set a password, defaults to 'postgres')
* create database fruitcart
run "./gradlew initdb"
* create migrations for fruit table with columns id, description, and
name. Run "./gradlew createdb"
* to add some data
run "psql -h localhost -U fruitcart --password -d fruitcart < db/backup.sql"`

*   运行一个干净的构建，看看是否有任何错误(剧透警告:有-数据库没有正确配置)。

*   创建了 Gradle 任务来初始化和创建我们的数据库。在我们正确地建立了数据库并在端口 5432 上运行之后，我们准备开始我们的第一次测试。

但是让我们倒回去一秒钟。来说说外卖吧。

[![golden retriever dog trotting with a paper bag](img/883fc2c7e9d5bf4dbed8bd7c62fb417c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ME_TzCZ1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/WxJqbwf.gif)

首先，必须在数据库环境所在的同一目录中从命令行创建和运行迁移。我以前确实知道这一点，但它值得重复，因为我经常忘记它(嘿，这个博客对我来说也是一个资源)。

命令是:
`./mybatis/bin/migrate new name-of-migration`

这将在脚本目录中创建一个文件(其名称以时间戳开头——迁移按照创建的顺序运行)。使用它来添加 SQL 语句。

[![back of a bald man's head with fingers plugging both ears](img/8eb1f9028fc7710da54d81cac8c98934.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--za8za4Tx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/JXrQnuq.jpg)

第二:如果你正确对待它。gitignore 是你的朋友。

因此，我们不希望某些文件出现在我们的远程回购中。我通常会想到。gitignore 是一个隐藏秘密的方便地方，但我认为更实际的是，它允许我们隐藏那些特定于我们本地构建的项目片段，而这些片段对于在其他环境中构建我们的项目是不必要的，例如那些跟踪我们工作区或构建中的变化的片段。

我们使用[https://gitignore.io/](https://gitignore.io/)来生成正确的。git ignore IntelliJ 的文件，这是我们这个项目的 IDE。这有点矫枉过正:我们没有启用吉拉或 Crashalytics 插件(对我们来说是纯智能级别)，但它让我们很好地意识到应该包括什么。所以我们只是复制并粘贴到我们自己的 gitignore 中。一切都很好。

杰夫可不这么想。

[![Lego man typing on computer, then pounding on computer, then throwing computer](img/37ea5b0319ce251fdeeeff799ba4f542.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MUmEsG7G--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/lfLyFz8.gif)

其中一个。idea 文件——workspace.xml——一直从. gitignore 中逸出。他会尝试提交/推送他的代码，但会出现失败:他的 workspace . XML 文件的更改没有被跟踪。但是当然有，当然也不会被跟踪:它是一个文件，跟踪他在 IDE 中的位置和其他特定于他的工作空间的内部结构。出于某种原因，。gitignore 没有告诉 git 不应该跟踪这个文件。

事实证明，如果您已经提交了该文件并将其推送到您的远程 repo，它将会困扰您。用杰夫的话来说，“是一件 sh**”。

解决方案:删除。idea 文件夹(它将由 IntelliJ 自动生成)，提交您的更改，然后添加。idea/workspace.xml 到您的。gitignore，并添加/提交/推送所有这些内容。

TL；dr:如果一个文件已经提交到您的回购中，那么无论您是否将它放入，它都会被自动跟踪。gitignore

[![glowing 3D table rendering of a database](img/3c376ed6fa0f6e513859618e0b3c1f00.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--EMOrMQ59--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/XLuwDE2.jpg)

第三:可能需要手动设置 Postgres 数据库中的 SQL。

我们的建造失败了。原来我们的数据库并没有被创建。如此怪异，考虑到 Postgres 正在运行(我认为它总是在我的机器上；that 和 Docker)。但是数据库没有被创建。它就是不在那里。

原来我们必须创建一个 shell 脚本来实际创建数据库，当我们这样做时，我们创建了一个实际上并不存在的所有者 fruitcart。

我们的 shell 脚本使得 createdb 函数运行良好，并且我们在构建中执行了该任务，但是数据库实际上会失败，因为没有超级用户，甚至没有具有写权限的用户。基本上，我们没有遵循本文开头的前两个指令。一旦我们有了，我们的水果车就诞生了。

[![Tina Fey giving herself a high five](img/f7e10124263c776825d11616f5481a22.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Wrs3PbMU--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://i.imgur.com/pgDcke5.gif)

这一切花费的时间比我们想象的要长得多。这将是我们的一个持续主题:我们突破，进入互联网虫洞，花大量时间探索。我们着眼于让 Jetty 发生，在无数次失败后放弃 Jetty。我们在 workspace.xml 上花了很长时间。我们在 Postgres 上修修补补。越来越好。但是当你学习的时候，甚至很难知道该问什么问题。当你不知道自己不知道什么的时候，甚至很难知道去哪里找。

毕竟，这就是:学习。