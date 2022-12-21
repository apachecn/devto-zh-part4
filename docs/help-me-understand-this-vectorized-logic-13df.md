# 请帮助我理解这种矢量化逻辑

> 原文：<https://dev.to/rpalo/help-me-understand-this-vectorized-logic-13df>

我希望在理解多维数组上的矢量化运算方面得到一些帮助。具体来说，我有一个问题和一些代码，我认为*应该工作，但它没有工作，我肯定这是因为我的想法是错误的，但我想不出为什么。*

一些警告:

*   这是为了一些家庭作业。我真的不想要我应该复制/粘贴而不理解的扑通一声代码。如果我想要的话，我会去找 StackOverflow。我想要概念。
*   我想只用`numpy`来做这件事。我知道`scipy`和其他 ML 库有奇特的功能，可以在黑盒中完成我所要求的，但这不是我想要的。这是一个学习练习。

场景是这样的:

## 场景

我有两个虹膜数据集(是的，那个虹膜数据)——一个训练集和一个测试集。这两个集合都有 4 列浮点值，以及对每个数据点进行分类的相关标签向量。

```
5.1,3.5,1.4,0.2,Iris-setosa
4.9,3,1.4,0.2,Iris-setosa
7,3.2,4.7,1.4,Iris-versicolor
... 
```

Enter fullscreen mode Exit fullscreen mode

我们正在做一个最近邻分类。目标是做到以下几点:

*   对于测试集中的每个数据点，通过计算两者之间的“距离”，将其与训练集中的所有点进行比较。距离计算如下

```
def distance(x, y):
    return math.sqrt((x[0] - y[0])**2 + (x[1] - y[1])**2 + (x[2] - y[2])**2 + (x[3] - y[3])**2) 
```

Enter fullscreen mode Exit fullscreen mode

也称为每个点的每个特征之间的差异的平方和根。

现在。这是我现在有的:

## 我的代码

```
import numpy as np

def distance(x, y):
    return np.sqrt(np.sum((x - y)**2, axis=1))

def main():
    # ... blah blah load data
    # training_data is 75 rows x 4 cols of floats
    # testing_data is 75 rows x 4 cols of floats
    # training_labels is 75 rows x 1 col of strings
    # testing_labels is 75 rows x 1 col of strings 
    # My thought is to use "broadcasting" to do it without loops
    # so far, to me, "broadcasting" == "magic" 
    training_data = training_data.reshape((1, 4, 75))
    testing_data = testing_data.reshape((75, 4, 1))

    # So this next bit should work like magic, producing a 75 x 1 x 75 matrix of
    # distances between the testing data (row indices) and the training data
    # (column indices) 
    distances = distance(testing_data, training_data)

    # And the column index of the minimum distance should in theory be the 
    # index of the training point that is the "closest" to the given testing point
    # for that row 
    closest_indices = distances.argmin(axis=1)

    # And this should build an array of labels corresponding to the indices
    # gathered above
    predicted_labels = training_labels[closest_indices]

    number_correct = np.sum(predicted_labels == testing_labels)
    accuracy = number_correct/len(testing_labels) 
```

Enter fullscreen mode Exit fullscreen mode

在我看来这一切都是对的。

酪

当我运行它时，根据我的提示，我应该期望精度在 0.94 范围内，而我得到的是 0.33 范围内的东西。也就是便便。

所以。我错过了什么？我完全误解了哪些关键概念？

谢谢大家！