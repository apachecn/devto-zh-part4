# 机器学习分类比较

> 原文：<https://dev.to/petercour/machine-learning-classification-comparison-2l5c>

分类是算法可以完成的任务。给定一个新样本，分类器可以决定它属于哪一类。

我们说的算法是[机器学习](https://pythonprogramminglanguage.com/python-machine-learning/)算法。

这些算法基于数据进行分类。例如，在花卉数据集上收集数据。

[![](img/5b9a19baaf465943f73504a411827bed.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8IxJgikN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fcdn-images-1.medium.com%252Fmax%252F1600%252F1%2A1q79O5DCx_XNrAARXSFzpg.png%26f%3D1)

然后基于这些测量和算法，可以进行分类。

这些算法具有不同的性能。有些算法比其他算法做得更好。理想情况是让分类器得分尽可能高(接近 100%)，但是分类器并不总是能达到这个目标。

[![](img/ad3d8cd3cf001f130ccc16ba2165c625.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--iP2KPoR5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Ftse3.mm.bing.net%252Fth%253Fid%253DOIP.P2DUKQYyX-6gIycMun77rAHaEK%2526pid%253DApi%26f%3D1)

Python 模块 sklearn 包含了许多算法的实现

```
from sklearn.ensemble import  RandomForestClassifier
from sklearn.naive_bayes import MultinomialNB
from sklearn.neighbors import KNeighborsClassifier
from sklearn.linear_model import LogisticRegression 
```

还有很多。下面的程序评估了他们对不同算法进行分类测试的效果。

```
#!/usr/bin/python3
#coding=utf-8

from sklearn import datasets
from sklearn.ensemble import  RandomForestClassifier
from sklearn.naive_bayes import MultinomialNB
from sklearn.neighbors import KNeighborsClassifier
from sklearn.linear_model import LogisticRegression
import numpy as np
from sklearn.model_selection import train_test_split

# Load dataset.
iris = datasets.load_iris()
X, y = iris.data, iris.target

# split data into training and test data.
train_x, test_x, train_y, test_y = train_test_split(X, y,
                                                    train_size=0.5,
                                                    test_size=0.5,
                                                    random_state=123)

clfs = {'random_forest' : RandomForestClassifier(n_estimators=50),
        'knn' : KNeighborsClassifier(),
        'bayes': MultinomialNB(alpha=0.01)}

def try_different_method(clf):
    clf.fit(train_x,train_y.ravel())
    score = clf.score(test_x,test_y.ravel())
    print('the score is :', score)

for clf_key in clfs.keys():
    print('the classifier is :',clf_key)
    clf = clfs[clf_key]
    try_different_method(clf) 
```

相关链接:

*   [Sklearn 机器学习模块](https://scikit-learn.org/stable/index.html)
*   [用 Python 进行机器学习](https://pythonprogramminglanguage.com/python-machine-learning/)
*   [Python 教程](https://pythonbasics.org/)