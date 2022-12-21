# 如何在不需要先学 Python 的情况下学习 Python

> 原文：<https://dev.to/dcrane604/how-to-learn-python-without-first-needing-to-learn-python-9je>

谈到驾驶，我们不会让学生在学会驾驶之前组装汽车。然而，在学习 Python 时，用户首先需要学习如何:

1.  安装 Python，最好是在虚拟环境中
2.  使用 pip 安装所有需求
3.  根据需要解决任何依赖关系冲突

在他们开始他们的 Python 课程之前。这种方法假设用户在安装 Python 之前已经对其有所了解，这对于课程创建者来说是一种错误的假设，对于学生来说也是一种威胁。例如:

*   我应该使用哪个 Python 版本？该课程的自述文件看起来似乎有一段时间没有更新了，并且推荐了一个旧版本，但我不应该使用最新版本吗？
*   我应该使用哪个发行版？预装在我的操作系统上的旧版本？或者我应该从 python.org 下载一个更新的版本，或者直接得到 Anaconda？这些会和我的 OS 安装的冲突吗？
*   我听说我应该使用虚拟环境，我该怎么做？

所有好的理由都说明了为什么新用户在开始之前应该得到一本好的 Python 书籍或者一个老师/顾问。但是还有另一种方法可以降低 Python 新用户的准入门槛:ActiveState 的状态工具。

### 简单的 Python 安装

State 工具可以通过一条命令将 Python 运行时安装到 Linux 或 Windows 上的虚拟环境中，从而简化 Python 的部署:

*   在开发人员的工作站上
*   在您的 CI/CD 链中的任何/所有实例上
*   生产中

它也非常适合第一次使用的用户，因为它可以自动创建他们的 Python 环境，而不需要任何以前的知识。例如，以 Oleksii Trekhleb 在 Github[trek Hleb/Learn-Python](https://github.com/trekhleb/learn-python)上创建的流行的“学习 Python”存储库为例。自述文件是这样描述环境设置的:

1.  “确保你的机器上安装了 Python3”并附有 RealPython 网站的链接，详细解释了这个过程。
2.  “您可能希望使用 venv 标准 Python 库来创建虚拟环境…”
3.  通过运行“pip install -r requirements.txt”安装项目所需的所有依赖项

或者，您可以安装状态工具并运行“state activate”命令，只需几秒钟就可以开始，而不需要任何 Python 知识。

事实上，你就可以在这里这样做:[https://github.com/shnewto/learn-python](https://github.com/shnewto/learn-python)。这个链接指向 Trekhleb 的 repo 的一个分支，我们创建这个分支是为了展示 State 工具简单而强大的功能。在这种情况下，您只需:

1.  [下载状态工具](https://docs.activestate.com/platform/start/state.html)
2.  [将 Repo](https://github.com/shnewto/learn-python) 和 cd 克隆到其中
3.  类型状态激活

从最终用户的角度来看，State 工具极大地减少了在您真正想做的事情:编码之前，为了设置您的环境而需要的特定语言知识的数量。

但是对于 Github 库所有者来说，这也意味着您的用户和贡献者可以更快地开始使用您的项目，这可能会导致更多的代码贡献。

### 还原自述

为了简化项目的环境设置，您可以完全按照我们对 Learn Python 项目所做的来做:在 ActiveState 平台上创建一个 Python 运行时，并在您的 repo 中包含一个指向它的 YAML 文件。让我们一步一步来:

1.  [创建一个免费的 ActiveState 平台账户](https://platform.activestate.com/create-account)
2.  在 Linux 或 Windows 上为 Python 创建一个新项目
3.  添加项目所需的包，让平台自动获取所有依赖项，解决它们，并在几分钟内为您创建一个安装程序
4.  创建一个 activestate.yaml 文件，并将其添加到您的存储库中。该文件包含四行:

*   名称:(输入步骤 2 中的项目名称)
*   所有者:(输入您在步骤 1 中输入的用户名)
*   语言:(此行目前为空白)
*   名称:(如果是 python 项目，请输入 Python)

不要忘记在你的自述文件中添加一个链接到[下载状态工具](https://docs.activestate.com/platform/start/state.html)，以及将你的 repo，cd 克隆到其中并发出“状态激活”命令的说明。

虽然在自述文件中可能还需要其他步骤，例如设置数据库，但是状态工具可以让您大大减少用户在开始处理项目之前必须经历的困难。或者，如果您是一名教育工作者，它可以使每学期建立一个充满 Python 环境的教室变得简单，就像编写一个脚本来删除旧的虚拟环境并在每个系统上“激活”一个新的虚拟环境一样。

想看看 ActiveState 平台如何让开发人员在几分钟内而不是几周内构建 Python 发行版吗？观看此[短视频](https://www.activestate.com/resources/videos/activestate-platform-trailer/)或[创建免费账户](https://platform.activestate.com/create-account)。