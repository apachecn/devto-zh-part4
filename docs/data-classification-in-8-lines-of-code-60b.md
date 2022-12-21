# 8 行代码中的数据分类

> 原文：<https://dev.to/petercour/data-classification-in-8-lines-of-code-60b>

分类是一件非常自然的事情。每次你看一样东西，你就决定它属于哪一组。它是一只鸟吗？它是一架飞机吗？

[![](img/a7f8806dff3427b6dd0e80c7d8ecd5c2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tUeO8NpV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252Fl9FG64wy5CT5e%252Fgiphy.gif%26f%3D1)

人类分类到此为止。电脑也能做到吗？是的，他们可以！

流行的解决方案是机器学习或人工智能。Python 模块 sklearn 是个不错的选择。([为什么用 Python 进行机器学习](http://pythonbasics.org/why-python-for-machine-learning/))

### 那么为什么要机器学习呢？

你就不能写一堆 if 语句吗？

好吧，伟大的程序员都很懒。想象一下，每当客户想要一个“智能机器人”时，都必须编写一个计算机程序。

太多的工作。

代码必须自己学习！怎么会？让代码从数据中学习。

[![](img/abed6a4c804c2a52adbeef7a5983cdb1.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--p-_XhaVV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F6fL4ZqndWKpOM%252Fgiphy.gif%26f%3D1)

### 你就这样码着

所以首先你需要加载 Python 和 sklearn。我们将使用一个名为 svm 的分类器。

```
#!/usr/bin/python3 from sklearn import svm 
```

那么我们需要数据。没问题，这里有一点数据。

```
x = [[2, 0], [1, 1], [2, 3]]
y = [0, 0, 1] 
```

那么 x 和 y 是什么呢？

*   x 是测量值。
*   y 是输出。

看 y，有两种可能的输出:0 和 1。

记住，svm 是一个分类器。所以它的输出不是**级 0** 就是**级 1** 。

#### 训练和预测

下一步，创建 svm 并训练它。和人类一样，机器学习算法也需要训练或学习。

```
clf = svm.SVC(kernel = 'linear')
clf.fit(x, y) 
```

训练时间！

[![](img/a32553b68d6a82fc040fe3ae318d5c3d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5CZeogHz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F1Ba856QSINOjC%252Fgiphy.gif%26f%3D1)

训练完成后，它可以分类。给定新数据[2，0]，它是哪个类:

```
print (clf.predict([[2,0]])) 
```

下面的神奇程序

```
#!/usr/bin/python3 from sklearn import svm

x = [[2, 0], [1, 1], [2, 3]]
y = [0, 0, 1]
clf = svm.SVC(kernel = 'linear')
clf.fit(x, y)
print (clf.predict([[2,0]])) 
```

所以如果你对术语感兴趣

*   svm =支持向量机
*   分类器=输出类的算法
*   fit =训练算法
*   人工智能=计算机代码+数据
*   机器学习=算法+数据

关于代码，我们称它为什么并不重要。

相关链接:

*   [sk learn 模块](https://scikit-learn.org/stable/index.html)
*   [机器学习初学者教程及示例](https://gumroad.com/l/MnRYU)
*   [机器学习文章](https://pythonprogramminglanguage.com/python-machine-learning/)
*   [什么是监督学习](https://pythonprogramminglanguage.com/What-is-supervised-learning/)
*   [为什么用 Python 进行机器学习](https://pythonbasics.org/why-python-for-machine-learning/)
*   [机器学习分类器](https://pythonbasics.org/machine-learning-classifier/)