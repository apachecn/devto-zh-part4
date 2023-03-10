# 为初学者讲解的机器学习

> 原文：<https://dev.to/petercour/machine-learning-explained-for-beginners-22cb>

所以从一个非常简单的角度来看。分类在机器学习中是如何工作的？当一个算法学习的时候意味着什么？

在计算机中，数据是最重要的。没有数据，计算机就变得毫无用处。许多公司已经成为数据贮藏者。这些数据被用来输入算法。

这些算法然后对社会、政治和你的个人生活做出决定。

是的，机器正在接管。

[![](img/854bb36951a5d5330b3d947714301db9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vaHyLeb0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttp%253A%252F%252Fs3lab.deusto.es%252Fwp-content%252Fuploads%252F2014%252F10%252FAll-Your-Data-Are-Belong-To-Us-S3lab.jpg%26f%3D1)

### 位和字节

所以从数据开始。如果你不是一个亿万美元的公司，你手头可能没有很多数据。

没有数据，就无法做出有用的机器学习程序。所以数据对公司来说是非常宝贵的资产。

[![](img/48d387d7d4c126a9b6a5931114132a13.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tpkfrhB_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fi.imgflip.com%252Fs03p0.jpg%26f%3D1)

因此，您可以使用预定义的数据集或随机数据。随机？是的:

```
#!/usr/bin/python3 import numpy as np
import pylab as pl
from sklearn import svm

np.random.seed(0)

X = np.r_[np.random.randn(100, 2) - [2, 2], np.random.randn(100, 2) + [2, 2]]
Y = [0]*100 +[1]*100 
```

是的，全部用 Python。([为什么用 Python 进行机器学习](http://pythonbasics.org/why-python-for-machine-learning/))。

所以这只是创建随机对(X，Y)。你可能记得在数学课上，你可以用(X，Y)对来创建图

[![](img/94e0e0e85c5bd2954d1e1f074b86121c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--b6f8eZsA--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zfkr3oe7tgeapq9j355f.png)

这些是数据点(X，Y)。然后需要一个算法来分类。
用这个数据怎么分类？

### 训练

它不能，你首先需要把数据点分成两组。

[![](img/7f3500045dfcaaeaf3219e8f45bb1dd1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OBWwi0ap--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f7oin3bxzcrfbb5syszz.png)

许多算法使用训练阶段。这时，算法得到示例输入:数据点和类。

科学家称之为“监督学习训练数据集”。所以如果你看到这个术语，它的意思是“数据点和类”。

[![](img/309bc0aac9121a60aaa83e4a299a7c89.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2NFpAQdg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F3o7btOXSkRujwwVueI%252Fgiphy.gif%26f%3D1)

这意味着在你收集数据之后，你需要给每个记录一个标签/类。好，然后把它输入算法:

```
clf = svm.SVC(kernel='linear')
clf.fit(X, Y) 
```

就这样。第一行加载一个名为 svm 的算法。第二行用给定的数据训练算法。

### 预测

现在如果你有一个新的测量，一个新的点，它落在平面上的某个地方。然后你就可以简单的看是在蓝班还是棕班了。

那个“看见”叫做预测。只要是二维平面，视觉就能工作。如果它变成 4 维或 12 维，就看不见了。

相关链接:

*   [sk learn 模块](https://scikit-learn.org/stable/index.html)
*   [机器学习初学者教程及示例](https://gumroad.com/l/MnRYU)
*   [机器学习文章](https://pythonprogramminglanguage.com/python-machine-learning/)
*   [什么是监督学习](https://pythonprogramminglanguage.com/What-is-supervised-learning/)
*   [为什么用 Python 进行机器学习](https://pythonbasics.org/why-python-for-machine-learning/)
*   [机器学习分类器](https://pythonbasics.org/machine-learning-classifier/)