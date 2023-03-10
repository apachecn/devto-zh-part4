# 基于机器学习的新闻组文本分类

> 原文：<https://dev.to/petercour/text-classification-with-machine-learning-laf>

文字可以自动[分类](https://pythonbasics.org/machine-learning-classifier/)。和机器学习一样，它需要数据。那么我们要把什么数据交给我们呢？

### 数据

假设我们的数据源是 fetch_20newsgroups 数据集。这个数据集包含分布在 20 个不同新闻组中的近 20，000 篇新闻组帖子的文本。

数据集很旧了，但这没关系。你可以在这里找到原文首页: [20 个新闻组数据集](https://web.archive.org/web/20140327110740/http://qwone.com/~jason/20Newsgroups/)

*Python 机器学习模块 sklearn 中默认包含的数据集。*

为了简单起见，我们只取 2 个新闻组“rec.motorcycles”和“rec.sport.hockey”。

```
#!/usr/bin/python3 news = fetch_20newsgroups(subset="all", categories=['rec.sport.hockey', 'rec.motorcycles']) 
```

[![](img/8ab229a2a74f74757f9dc21698a912ff.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--oB9lqdEs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252F67XS8111U1uuI%252Fgiphy.gif%26f%3D1)

### 测试算法

在使用分类器之前，您想知道它的工作情况。这是通过将数据集分成[训练和测试集](https://pythonbasics.org/split-train-test/)来完成的。

```
#!/usr/bin/python3 x_train, x_test, y_train, y_test = train_test_split(news.data,news.target) 
```

我们处理的数据是文本。它必须是矢量。然后使用 TfidfVectorizer。所以我们有两个向量:x_train 和 x_test。

```
#!/usr/bin/python3 transfer = TfidfVectorizer()
x_train = transfer.fit_transform(x_train)
x_test = transfer.transform(x_test) 
```

无需更改 y_train 和 y_test，因为它们是输出标签(0 级或 1 级)

创建一个算法对象并用数据对其进行训练。

```
#!/usr/bin/python3 estimator = MultinomialNB()
estimator.fit(x_train,y_train) 
```

然后你可以做预测，看看它对测试数据的分类有多好

```
#!/usr/bin/python3 y_predict = estimator.predict(x_test)
print("y_predict:\n", y_predict)

score = estimator.score(x_test, y_test)
print("score：\n", score) 
```

运行该程序，您将看到其准确性:

```
score： 0.9939879759519038 
```

[![](img/abb1eaefcc786679c3047d8f98856a09.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rrHjz-Sf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Ftse2.mm.bing.net%252Fth%253Fid%253DOIP.DQdhW0-IoV8dxYS2A61MdQAAAA%2526pid%253DApi%26f%3D1)

### 做出自己的预测

您可以使用新文本进行预测:

```
Enter some text: i like to drive motor cycle on the highway
y_predict:
[0]

Enter some text: i like to play hockey game
y_predict:
[1] 
```

为此，请添加以下几行:

```
#!/usr/bin/python3 sentence = input("Enter some text: ")
sentence_x = transfer.transform([sentence])
y_predict = estimator.predict(sentence_x)
print("y_predict:\n", y_predict) 
```

### 程序

下面的程序完成了所有的工作

```
#!/usr/bin/python3 from sklearn.datasets import fetch_20newsgroups
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.model_selection import train_test_split

def nb_news():
    news = fetch_20newsgroups(subset="all", categories=['rec.sport.hockey', 'rec.motorcycles'])

    x_train, x_test, y_train, y_test = train_test_split(news.data,news.target)

    transfer = TfidfVectorizer()
    x_train = transfer.fit_transform(x_train)
    x_test = transfer.transform(x_test)

    estimator = MultinomialNB()
    estimator.fit(x_train,y_train)

    y_predict = estimator.predict(x_test)
    print("y_predict:\n", y_predict)

    score = estimator.score(x_test, y_test)
    print("score：\n", score)
    return None

nb_news() 
```

相关链接:

*   [sk learn 机器学习模块](https://scikit-learn.org/stable/index.html)
*   [为什么用 Python 进行机器学习](https://pythonbasics.org/why-python-for-machine-learning/)
*   [什么是机器学习](https://pythonprogramminglanguage.com/What-is-Machine-Learning/)
*   [什么是分类](https://pythonprogramminglanguage.com/machine-learning-classifier/)
*   [机器学习课程及实例](https://gumroad.com/l/MnRYU)