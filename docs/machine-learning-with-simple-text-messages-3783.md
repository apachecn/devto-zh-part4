# 用简单的文本消息进行机器学习

> 原文：<https://dev.to/petercour/machine-learning-with-simple-text-messages-3783>

您可以[自动对文本数据进行分类](https://pythonbasics.org/machine-learning-classifier/)。假设你收集关于烹饪的信息和关于编程的信息。

然后，机器学习算法可以决定新消息属于哪个组(烹饪、编程)。

[![](img/adf37c1167b8ae21c6d421a967ac07f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eyFWsjwC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia.giphy.com%252Fmedia%252FiLsDDqQYZJWpy%252Fgiphy.gif%26f%3D1)

如果您有这样的消息列表:

```
#!/usr/bin/python3 data = [ "Help me impress the girl of my dreams!", 
         "How do you measure ingredients like butter in cups?",
         "Tips on making fried rice", 
         "immutability in javascript. It has a declarative approach of programming, which means that you focus on describing what your program must accomplish", 
         "Facing a Programming Problem. Everybody has encountered it, the programming problem that makes NO sense. This problem has no fix, it just cannot be done",
         " 5 Uses for the Spread Operator. The spread operator is a favorite of JavaScript developers. It's a powerful piece of syntax that has numerous applications."] 
```

其中每个消息属于一个类别

```
target = [ 0,0,0,1,1,1 ] 
```

您可以预测新消息的类别:

```
#!/usr/bin/python3 sentence = input("Enter some text: ")
sentence_x = transfer.transform([sentence])
y_predict = estimator.predict(sentence_x)
print("y_predict:\n", y_predict) 
```

旋转一下:

```
Enter some text: im a cook
y_predict: [0] 
```

另一次运行:

```
Enter some text: programming javascript is great
y_predict: [1] 
```

[![](img/1ea395bcaf96a9ace8b7c893fff400bc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6smUROp0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Fmedia1.tenor.com%252Fimages%252F0332b491d62a2c7e13d680c64538a322%252Ftenor.gif%253Fitemid%253D4485025%26f%3D1)

### 程序

我们定义的数据集非常小(6 个样本)。你的样本越多，它就变得越好。

```
#!/usr/bin/python3 from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.model_selection import train_test_split

def text_classify():
    data = [ "Help me impress the girl of my dreams!", 
             "How do you measure ingredients like butter in cups?",
             "Tips on making fried rice", 
             "immutability in javascript. It has a declarative approach of programming, which means that you focus on describing what your program must accomplish", 
             "Facing a Programming Problem. Everybody has encountered it, the programming problem that makes NO sense. This problem has no fix, it just cannot be done",
             " 5 Uses for the Spread Operator. The spread operator is a favorite of JavaScript developers. It's a powerful piece of syntax that has numerous applications."]
    target = [ 0,0,0,1,1,1 ]
    x_train, x_test, y_train, y_test = train_test_split(data,target)

    transfer = TfidfVectorizer()
    x_train = transfer.fit_transform(x_train)
    x_test = transfer.transform(x_test)

    estimator = MultinomialNB()
    estimator.fit(x_train,y_train)

    score = estimator.score(x_test, y_test)
    print("score：\n", score)

    sentence = input("Enter some text: ")
    sentence_x = transfer.transform([sentence])
    y_predict = estimator.predict(sentence_x)
    print("y_predict: ", y_predict)

    return None

text_classify() 
```

相关链接:

*   [sk learn 机器学习模块](https://scikit-learn.org/stable/index.html)
*   [为什么用 Python 进行机器学习](https://pythonbasics.org/why-python-for-machine-learning/)
*   [什么是机器学习](https://pythonprogramminglanguage.com/What-is-Machine-Learning/)
*   [什么是分类](https://pythonprogramminglanguage.com/machine-learning-classifier/)
*   [机器学习课程及实例](https://gumroad.com/l/MnRYU)