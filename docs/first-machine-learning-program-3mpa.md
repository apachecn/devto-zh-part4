# 第一个机器学习程序

> 原文：<https://dev.to/petercour/first-machine-learning-program-3mpa>

觉得机器学习一定很难？

再想想。安装 sklearn 模块后可以从一个简单的程序开始。

```
#!/usr/bin/python3
import sklearn;
print("Scikit-Learn", sklearn.__version__) 
```

那么它是做什么的呢？你猜对了。

```
python3 first.py  
Scikit-Learn 0.21.2 
```

所以让我们开始制作一个机器学习应用程序

[![](img/fbc020f7cb2be8702356499c39cd209c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--plhoIJr8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thumbs.gfycat.com/ScholarlyKindlyDog.webp)

### 算法

机器学习有两个重点:算法和数据。Sklearn 包含了很多像这样的算法:

```
#!/usr/bin/python3
import sklearn;
from sklearn import tree
from sklearn.naive_bayes import GaussianNB
from sklearn import neighbors

# knn classifier                                                                                                                                                        
clf = neighbors.KNeighborsClassifier(n_neighbors, weights=weights)

# decission tree algorithm                                                                                                                                              
clf = tree.DecisionTreeClassifier()   

# naive bayes                                                                                                                                                           
gnb = GaussianNB() 
```

这里没什么特别的。只是说明 sklearn 包含了很多算法(机器学习算法)

[![](img/5770c846a77f67cafe6482c9e7b5ada4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--OONar7oY--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://proxy.duckduckgo.com/iu/%3Fu%3Dhttps%253A%252F%252Ftse4.mm.bing.net%252Fth%253Fid%253DOIP.Zc16FPQZU81xO9teLja0kgHaFC%2526pid%253DApi%26f%3D1)

### 下厨吧！

做饭时间！现在如果你没看过绝命毒师系列，可能会有点奇怪。那我们要做什么？

我们会做一个预测器。

根据输入数据，它将预测这是一个好产品还是坏产品。

示例运行:

```
python3 skfirst.py  
Enter color: blue
How many percent pure: 99

Prediction : ['heisenberg']

-----------------------------
python3 skfirst.py
Enter color: blue
How many percent pure: 25

Prediction : ['garbage']
-----------------------------
python3 skfirst.py  
Enter color: white
How many percent pure: 99

Prediction : ['garbage'] 
```

任何机器学习算法都使用(训练)数据。数据有两个变量(颜色，纯度)和两个可能的结果(海森堡，垃圾)

```
# training data                                                                                                                                                         
X = [[0,90], [0,80], [1,20], [1,60], [0,70],[0,70],[1,99]] 
Y = ['heisenberg','heisenberg','garbage','garbage','garbage', 'garbage', 'garbage'] 
```

然后，对于任何新的输入，它可以预测它是好还是坏的产品:

```
P = [[color,pure]]

# make prediction                                                                                                                                                       
c = tree.DecisionTreeClassifier()
c = c.fit(X,Y) 
```

以下所有代码:

```
#!/usr/bin/python3
import sklearn;
from sklearn import tree
from sklearn.naive_bayes import GaussianNB
from sklearn import neighbors

# training data                                                                                                                                                     
X = [[0,90], [0,80], [1,20], [1,60], [0,70],[0,70],[1,99]]
Y = ['heisenberg','heisenberg','garbage','garbage','garbage', 'garbage', 'garbage']

# new data for prediction                                                                                                                                           
color = input("Enter color: ")
if color == "blue":
    color = 0.0
else:
    color = 1.0

pure = input("How many percent pure: ")
pure = float(pure)

P = [[color,pure]]

# make prediction                                                                                                                                                   
c = tree.DecisionTreeClassifier()
c = c.fit(X,Y)
print("\nPrediction : " + str(c.predict(P))) 
```

那很有趣！

相关链接:

*   [机器学习课程&例题](https://gumroad.com/l/MnRYU)
*   [Python 机器学习博客](https://pythonprogramminglanguage.com/python-machine-learning/)
*   [Sklearn 机器学习模块](https://scikit-learn.org/stable/index.html)
*   [为什么用 Python 进行机器学习](https://pythonbasics.org/why-python-for-machine-learning/)