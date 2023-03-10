# 假阳性和 ML

> 原文：<https://dev.to/petercour/false-positives-and-ml-2763>

计算机有时会得出错误的结论。那是因为不同的原因。模型([机器学习](https://pythonprogramminglanguage.com/python-machine-learning/)算法)可能不正确，可能数据不够等等。

让我们以简单的花卉数据集为例，找出假阳性的数量。下面的 Python 程序计算:

```
#!/usr/bin/python3 from sklearn import datasets
from sklearn.naive_bayes import GaussianNB

iris = datasets.load_iris()
gnb = GaussianNB()
y_pred = gnb.fit(iris.data, iris.target).predict(iris.data)
print("Number of false positives out of a total %d points : %d"  % (iris.data.shape[0],(iris.target != y_pred).sum())) 
```

150 个错误中有 6 个。你可以说这不算多，但这取决于你处理的是什么样的数据，允许什么样的容错。

[![](img/fb1b95b98d40974ef999b7d20df26394.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DZT-cdlx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Ftse1.mm.bing.net%252Fth%253Fid%253DOIP.-dkSwZaXTpgcsAf93d0ZHgHaEB%2526pid%253DApi%26f%3D1)

预测算法有时会得出错误的结论。有 4 种可能的结果:

[![](img/5c85adfadf90f0df4b177e5c1c9f16a5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hk7ZNUvR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fi.stack.imgur.com%252F65Rsd.jpg%26f%3D1)

在理想情况下，它会得到 100%的分数，并且只有真正的肯定和否定。实际上，算法往往不太准确。

对于一个玩具程序来说，这没什么大不了的，但如果算法正在驾驶你的汽车或驾驶你坐的飞机呢？

事实上，你的自动驾驶飞机中的一个错误可能是一场灾难:

[![](img/a21680d3ba07370b0b2faf9562cbe51d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sM16yPg_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252FArYyMzY10qNnq%252Fgiphy.gif%26f%3D1)

你想用大量的训练和测试数据尽可能地优化你的算法。

相关链接:

*   [sk learn 模块](https://scikit-learn.org/stable/index.html)
*   [机器学习课程及实例](https://gumroad.com/l/MnRYU)
*   [机器学习博客](https://pythonprogramminglanguage.com/python-machine-learning/)
*   [为什么用 Python 进行机器学习](https://pythonbasics.org/why-python-for-machine-learning/)