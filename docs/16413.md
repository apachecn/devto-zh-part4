# Python 中的轻量级文本分类器

> 原文：<https://dev.to/kambanthemaker/a-light-weight-text-classifier-in-python-4o36>

## 文本分类

文本分类是自然语言处理领域的一个著名课题，广义上分为两类，

*   监督
*   无人监督的

## 监督分类

将给定文本归类到预定义的类别集中。比方说，你有新的电子邮件，你想把它们分成两类:社交类和促销类。在这种情况下，您需要为每个类别设置训练集，并以此建立模型，然后分类引擎将对任何给定的文本执行分类。

## 无监督分类

它不需要训练数据集，而是将给定的文档集合分组为逻辑单元。无监督学习是一种形式聚类。K-Means 是这里流行的算法。

## 轻量级监督分类

我们感兴趣的是本文中的监督分类。一般来说，这需要大量的训练数据来给出正确的预测。我想对我的一个与电子邮件处理相关的项目进行分类，大多数工具都需要大量的数据集，而我没有，但我有少量准确的数据。所以，我用 Python 构建了一个轻量级分类器，它采用一个小的训练数据集，根据单词产生结果。

结账[https://github.com/kambanthemaker/textclassifier](https://github.com/kambanthemaker/textclassifier)

该工具完全依赖于单词 presence，并根据训练集计算给定输入中的总出现次数，然后返回类别列表。

### 培训文件格式

```
__label__ category1 training data
__label__ category1 some other data
__label__ category2 some data 
```

文本后跟标签是类别名称，后跟一个空格，然后是输入句子。选择这种格式是为了与 [fasttext](https://github.com/facebookresearch/fastText) 库保持一致，这样如果你想转移到那个库，训练文件可以重新移植。

### 调用分类器

```
import classifierresults = classifier.classify("offer linkedin linkedin", "somerandomcategory") 
```

结果将是一个元组列表，像`[('category'1', 10), ('category2',5)]`一样先按顶部匹配排序。10.5 是分数，即单词匹配的数量。“somerandomcategory”是在没有匹配的情况下您将收到的默认类别！

这个小库应该可以在 Python 2.x 和 3.x 中工作，并且没有依赖性。

### 工作原理

该库将训练准备成统计的单词，然后将其与给定的输入文本进行比较，根据单词匹配对结果进行排序。对于上述训练数据，它将有

```
{'category1':['training','data','some','other','data'],'category2':['some','data']} 
```

当您输入`Hello data`时，它将返回[('类别 1 '，2)，('类别 2 '，1)]，因为`data`在类别 1 中出现两次，在类别 2 中出现一次。

## 文本分类工具

当您需要更强大的分类时，有几个不错的选择，

*   阿帕奇看象人

[https://www . Oracle . com/tech network/community/book store/taming-text-sample-523387 . pdf](https://www.oracle.com/technetwork/community/bookstore/taming-text-sample-523387.pdf)有一些基本的细节。

*   脸书的快速文本

[https://github.com/facebookresearch/fastText](https://github.com/facebookresearch/fastText)，用 Python 写的，号称运行速度更快。我们的训练集格式与该工具兼容。

*   AWS 分类

https://aws.amazon.com/comprehend 是一项自然语言处理服务，用于在文本中寻找观点和关系。我们在这里讨论的另外两个服务需要安装在你自己的服务器上，而这个服务可以使用 API 访问，你可以按使用量付费。

## 下载

[https://github.com/kambanthemaker/textclassifier](https://github.com/kambanthemaker/textclassifier)