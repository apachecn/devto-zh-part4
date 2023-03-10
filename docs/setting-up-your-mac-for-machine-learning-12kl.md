# 为机器学习设置 Mac

> 原文：<https://dev.to/leapfrogtechnology/setting-up-your-mac-for-machine-learning-12kl>

从零到使用 scikit-learn、Keras 和 TensorFlow 在数小时内运行第一个机器学习和深度学习项目

我买了一台新的苹果电脑。我需要在本地机器上安装构建机器学习项目所需的一切。我本可以用时光机把我的旧 Mac 上的所有东西都复制下来。然而，我想从零开始，这样我就可以记录这些步骤，并且我可以与所有那些日常工作不是编码而是想不时弄脏手的人分享。

我不是开发商。我是产品经理。我不每天写代码，但是在必要的时候写一些代码是很有趣的。至少，我可以在我的机器上本地克隆一个项目。

1.  **安装 Python**
    检查是否默认安装了 Python。Mac 自带默认 Python 版本 2.7.0。我将对 Python 使用 Anaconda，对数据科学和机器学习使用不同的包。

2.  安装 Miniconda
    我选择 Miniconda 是因为我不需要所有的软件包。我可以根据需要在我创建的虚拟环境中安装这些包。我喜欢隔离不同类型的依赖包。

    *   [Anaconda vs Miniconda vs 虚拟环境](http://deeplearning.lipingyang.org/2018/12/23/anaconda-vs-miniconda-vs-virtualenv/)
    *   [如何安装 Miniconda](http://deeplearning.lipingyang.org/2018/12/24/install-miniconda-on-mac/)
3.  **管理环境**

    *   [为什么你需要环境](%5Bhttps://www.freecodecamp.org/news/why-you-need-python-environments-and-how-to-manage-them-with-conda-85f155f4353c/)
    *   [康达文件](%5Bhttps://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#removing-an-environment)
4.  **安装 Jupyter 笔记本**
    T3】安装 Jupyter 笔记本 T5】如何在 Jupyter 笔记本中安装包？有些事情很复杂，我们需要小心。如果你有兴趣，那么这里有一篇[好文章](https://jakevdp.github.io/blog/2017/12/05/installing-python-packages-from-jupyter/)。

5.  在你安装了 Jupyter 笔记本之后，你可以按照[这个笔记本](https://github.com/bimalmaharjan/settingupmacforml/blob/master/Helloml.ipynb)来安装和检查所有那些软件包。

希望你觉得这个策展有用！

* * *

#### 现在你的 Macbook 已经准备好了，想要迈出下一步，了解更多关于在产品中集成 AI 的信息吗？

#### [![Lean AI Playbook](img/d8cfa38a2989fa915a857975a22ca5da.png)T4】](https://landing.lftechnology.com/ai/?utm_source=devto&utm_medium=blog&utm_campaign=initial_distribution)

这篇文章最初发表在 [lftechnology 博客](https://www.lftechnology.com/blog/setting-mac-machine-learning/)上